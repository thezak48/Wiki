---
标题：Prowlarr 贡献
描述：
已发布：真
日期：2022-11-16T19:35:23.515Z
标签：prowlarr，开发，贡献
编辑器：markdown
创建日期：2021-12-11T19:42:15.627Z
---

# 如何贡献

我们一直在寻找人们来帮助我们使 Prowlarr 变得更好，有许多方式可以贡献。

# 文档

设置指南，[常见问题](/prowlarr/faq)，我们在[wiki](https://wiki.servarr.com/prowlarr)上有的信息越多越好。

# 开发

Prowlarr 用 C#（后端）和 JS（前端）编写。后端基于 .NET6 框架构建，而前端则使用 Reactjs。

## 所需工具

- 推荐使用 Visual Studio 2022 或更高版本 (<https://www.visualstudio.com/vs/>)。社区版是免费的并且可以工作 (<https://www.visualstudio.com/downloads/>).

> 建议使用 VS 2022 V17.0 或更高版本，因为它包含 .NET6 SDK
{.is-info}

- HTML/Javascript 编辑器（VS Code/Sublime Text/Webstorm/Atom/等）
- [Git](https://git-scm.com/downloads)
- 需要 [Node.js](https://nodejs.org/) 运行时。支持以下版本：
  - **12.0** 或更高版本
  - **14.0** 或更高版本
  - **16.0** 或更高版本
{.grid-list}

> Prowlarr 将**不会**在旧版本上运行，如 `10.x`，`8.x`，`6.x`，或任何低于 12.0 的版本！
{.is-warning}

- 需要 [Yarn](https://yarnpkg.com/getting-started/install) 来构建前端
  - Yarn 默认包含在 **Node 16.10**+ 中。使用 `corepack enable` 启用它
  - 对于其他 Node 版本，使用 `npm i -g corepack` 安装它

## 入门

1. Fork Prowlarr
1. 将仓库克隆到你的开发机器中。[*信息*](https://docs.github.com/en/get-started/quickstart/fork-a-repo)

> 确保在提交前对你的代码运行 lint `yarn lint --fix` 以进行任何前端更改。
对于 css 更改 `yarn stylelint-windows --fix`
{.is-info}

### 构建前端

- 导航到克隆的目录
- 安装所需的 Node 包

     ```bash
     yarn install
     ```

- 启动 webpack 来监视你的开发环境，以便进行任何需要后处理的更改：

     ```bash
     yarn start
     ```

### 构建后端

后端解决方案最容易在 Visual Studio 或 Rider 中构建和运行，但是如果只关注前端 UI，当安装了正确的 SDK 时，也可以很容易地从命令行构建。

#### Visual Studio

> 确保启动项目设置为 `Prowlarr.Console`，并将框架设置为 `net6.0`
{.is-info}

1. 首先在 Visual Studio 中 `Build` 解决方案，这将确保所有项目正确构建并恢复依赖项
1. 接下来在 Visual Studio 中 `Debug/Run` 项目以启动 Prowlarr
1. 打开 <http://localhost:9696>

#### 命令行

1. 清理解决方案

  ```shell
  dotnet clean $slnFile -c Debug
  ```

1. 为正确的平台（Posix 或 Windows）恢复并构建调试配置

```shell
dotnet msbuild -restore src/Prowlarr.sln -p:Configuration=Debug -p:Platform=Posix -t:PublishAllRids
```

1. 从 `/_output` 运行生成的可执行文件

## 贡献代码

- 如果你正在添加一个新的，已经请求的功能，请在 [GitHub Issues](https://github.com/Prowlarr/Prowlarr/issues) 上评论，以免工作被重复（如果你想添加那里还没有的东西，请先和我们讨论）
- 从 Prowlarr 的 develop 分支进行 rebase，不要合并
- 做有意义的提交，或者 squash 它们
- 在工作完成之前，随时可以提出拉取请求，这将让我们看到它在哪里，并做出评论/建议改进
- 如果你有任何问题，请在 discord 上联系我们
- 添加测试（单元/集成）
- 为了保持一致性，请使用 \*nix 行结束符进行提交（我们在 Windows 上检出并在 \*nix 上提交）
- 每个拉取请求应该只有一个功能/错误修复，以保持事情清晰易懂
- 使用 4 个空格代替制表符，这是 VS 2022 和 WebStorm 的默认设置

### 贡献索引器

### C# 索引器

- C# 索引器应该向 [Prowlarr App Repository](https://github.com/prowlarr/prowlarr) 的 `develop` 分支提出拉取请求
- 如果你正在贡献一个 C# 索引器，请将你的提交描述为类似于：`New: (Indexer) {Indexer Name}`，`New: (Indexer) {Usenet|Torrent} {Indexer Name}`，`New: (Indexer) {Torznab|Newznab} {Indexer Name}`
- 如果你正在更新一个 C# 索引器，请将你的提交描述为类似于：`Fixed: (Indexer) {Indexer Name} {changes}` 例如 `Fixed: (Indexer) Changed BHD to use API`

### Cardigann (YML) 索引器

- Cardigann 和 YML 索引器应该向 [Prowlarr Indexer Repository](https://github.com/prowlarr/indexers) 的 `master` 分支提出拉取请求
- 对于 Cardigann/YML 索引器的详细信息，请参见 [Prowlarr Cardigann yml 格式的定义和描述](/prowlarr/cardigann-yml-definition)
- 对于测试自定义 yml 定义，请参见 [索引器页面中的自定义 yml 部分](/prowlarr/indexers#adding-a-custom-yml-definition)

## 拉取请求

- 只对 `develop` 发起拉取请求，永远不要对 `master` 发起，如果你对 `master` 发起了 PR，我们会对其进行评论并关闭它
- 你可能会从我们那里得到一些评论或问题，这些都是为了确保一致性和可维护性
- 我们会尽快回应拉取请求，如果已经过了一两天，请联系我们，我们可能错过了它
- 每个 PR 应该来自你的 fork 中的自己的 [功能分支](http://martinfowler.com/bliki/FeatureBranch.html)，而不是 develop，它应该有一个有意义的分支名（正在添加/修复什么）
  - `new-feature` (好)
  - `fix-bug` (好)
  - `patch` (坏)
  - `develop` (坏)
- 提交应该写为 `New:` 或 `Fixed:`，对于那些不会被认为是 `maintenance release` 的更改

## 单元测试

Prowlarr 使用 nunit 进行其单元，集成和自动化测试套件。

### 运行测试

可以使用包含的 nunit3testadapter nuget 包在 VS 中轻松运行测试，或者使用包含的 bash 脚本 `test.sh` 在命令行中运行。

在 VS 中，只需导航到 Test Explorer 并运行或调试你想要检查的测试。

在 VS 中可以一次运行所有测试，也可以一次运行一个测试。

在命令行中，`test.sh` 脚本接受 3 个参数

```bash
test.sh <PLATFORM> <TYPE> <COVERAGE>
```

### 编写测试

虽然并不总是有趣，但我们鼓励为任何后端代码更改编写单元测试。这将确保更改按你的意图运行，并且未来的更改不会破坏预期的行为。

> 我们目前要求在提交 PR 时新代码的覆盖率达到 80%
{.is-info}

如果你对此有任何疑问，请告诉我们。

# 翻译

Prowlarr 使用自托管的开放访问 [Weblate](https://translate.servarr.com) 实例来管理其 json 翻译文件。这些文件存储在 `src/NzbDrone.Core/Localization` 的仓库中

## 贡献现有翻译

Weblate 处理所有语言（除英语外）的字符串同步和翻译。对于 Prowlarr 项目，应在那里进行已支持语言的已翻译字符串的编辑和现有字符串的翻译。

英文翻译，`en.json`，作为所有其他翻译的源头，并在 GitHub 仓库上进行管理。

## 添加语言

向 Prowlarr 添加翻译需要两个步骤

- 将语言添加到 weblate
- 将语言添加到 Prowlarr 代码库

## 在代码中添加翻译字符串

英文翻译，`src/NzbDrone.Core/Localization/en.json`，作为所有其他翻译的源头，并在 GitHub 仓库上进行管理。当在 UI 或后端添加新字符串时，必须在 `en.json` 中添加一个键以及英文的默认值。然后可以按照以下方式使用此键：

> 不会接受日志消息的翻译 PR
{.is-warning}

### 后端字符串

可以使用 Localization Service 的 `GetLocalizedString` 方法添加后端字符串

```dotnet
private readonly ILocalizationService _localizationService;

public IndexerCheck(ILocalizationService localizationService)
{
  _localizationService = localizationService;
}
        
var translated = _localizationService.GetLocalizedString("IndexerHealthCheckNoIndexers")
```

### 前端字符串

可以通过导入 translate 函数并使用从 `en.json` 指定的键来添加新字符串

```js
import translate from 'Utilities/String/translate';

<div>
  {translate('UnableToAddANewIndexerPleaseTryAgain')}
</div>
```