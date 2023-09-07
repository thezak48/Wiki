---
标题：配置 Prowlarr 的 PostgreSQL 数据库
描述：使用 Postgres 数据库配置 Prowlarr
已发布：真
日期：2022-11-29T13:19:38.953Z
标签：
编辑器：markdown
创建日期：2022-01-10T15:38:53.538Z
---

# Prowlarr 和 Postgres

本文档将详细介绍在 Prowlarr 中迁移和设置 Postgres 支持的关键点。

本指南由出色的 [Roxedus](https://github.com/Roxedus) 创建。

> Prowlarr 不会备份 Postgres 数据库，任何备份必须由用户实施和维护
{.is-danger}

## 设置 Postgres

首先，我们需要一个 Postgres 实例。本指南是为使用 `postgres:14` Docker 镜像的用户编写的。

> 别想使用 `latest` 标签！{.is-danger}

```bash
docker create --name=postgres14 \
    -e POSTGRES_PASSWORD=qstick \
    -e POSTGRES_USER=qstick \
    -e POSTGRES_DB=prowlarr-main \
    -p 5432:5432/tcp \
    -v /path/to/appdata/postgres14:/var/lib/postgresql/data \
    postgres:14
```

## 创建数据库

Prowlarr 需要两个数据库，它们的默认名称是：

- `prowlarr-main`   用于存储所有配置和历史记录
- `prowlarr-log`    用于存储产生日志条目的事件

> Prowlarr 不会为你创建数据库。确保你提前创建它们{.is-warning}

使用你喜欢的方法创建上述数据库 - 例如 [pgAdmin](https://www.pgadmin.org/) 或 [Adminer](https://www.adminer.org/)。

> 为了运行 `Housekeeping` 任务，此用户必须是超级用户，因为它执行 vacuum 任务{.is-info}

你可以给数据库取任何你想要的名字，但确保 `config.xml` 文件有正确的名字。更多信息请参阅 [schema 创建](/prowlarr/postgres-setup#schema-creation)。

### Schema 创建

我们需要告诉 Prowlarr 使用 Postgres。`config.xml` 应该已经填充了我们需要的条目：

```xml
<PostgresUser>qstick</PostgresUser>
<PostgresPassword>qstick</PostgresPassword>
<PostgresPort>5432</PostgresPort>
<PostgresHost>postgres14</PostgresHost>
```

如果你想指定数据库名称，那么还应该包括以下配置：

```xml
<PostgresMainDb>MainDbName</PostgresMainDb>
<PostgresLogDb>LogDbName</PostgresLogDb>
```

只有在**创建了**两个数据库后，你才能开始从 SQLite 到 Postgres 的 Prowlarr 迁移。

## 数据迁移

> 如果你不想将现有的 SQLite 数据库迁移到 Postgres，那么你已经完成了本指南！{.is-info}

为了迁移数据，我们可以使用 [PGLoader](https://github.com/dimitri/pgloader)。然而，它有一些需要注意的地方：

- 默认情况下，事务是不区分大小写的，我们使用 `--with "quote identifiers"` 使它们区分大小写。
- Debian 和 Ubuntu 的 apt 仓库中打包的版本对于新版本的 Postgres 来说太旧了（Roxedus 没有测试其他发行版的包）。
  Roxedus [构建了一个二进制文件](https://github.com/Roxedus/Pgloader-bin) 来启用这个支持（不需要修改代码，只需要用更新的依赖项构建）。

> 不要在 Postgres 实例中删除任何表{.is-danger}

在开始迁移之前，请确保你已经至少成功地对创建的 Postgres 数据库运行了 Prowlarr 一次。按照以下步骤开始迁移：

1. 停止 Prowlarr
1. 打开你喜欢的数据库管理工具并连接到 Postgres 数据库实例
1. 通过以下任一选项开始迁移：

    - ```bash
      pgloader --with "quote identifiers" --with "data only" prowlarr.db 'postgresql://qstick:qstick@localhost/prowlarr-main'
      ```

    - ```bash
      docker run --rm -v /absolute/path/to/prowlarr.db:/prowlarr.db:ro --network=host ghcr.io/roxedus/pgloader --with "quote identifiers" --with "data only" /prowlarr.db "postgresql://qstick:qstick@localhost/prowlarr-main"
      ```

  > 如果你在使用 pgloader 时遇到错误，可能是因为你的数据库太大，为了解决这个问题，尝试在上述命令中添加 `--with "prefetch rows = 100" --with "batch size = 1MB"`{.is-warning}

  > 处理了这些问题后，只要告诉它不要使用 `--with "data only"` 来修改方案，就非常简单了{.is-info}

1. 启动 Prowlarr