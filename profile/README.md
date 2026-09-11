# oheco · 让开源软件运行在鸿蒙上

oheco 是一个面向 OpenHarmony 开源生态建设的 GitHub 组织。我们的核心目标是将众多开源软件移植到 OpenHarmony 系统中，覆盖 HarmonyOS 等使用场景，让用户能够方便地获取软件，也让开发者能够共同参与适配与长期维护。

组织负责协调项目接入、打包和发布；项目维护者负责具体软件的适配与维护。适配完成的软件通过 GitHub Releases 发布，并收录到统一的软件目录，供所有人使用。

## 如何使用 oheco

你可以通过下载站查找软件、直接下载发行包，也可以使用 oheco 包管理器在终端完成搜索、下载、安装和版本切换。

| 入口 | 用途 |
| --- | --- |
| [oheco 包管理器](https://github.com/oheco/oheco) | 命令名为 `oo`，用于安装和管理原生软件包 |
| [软件下载站](https://oheco.github.io/oheco-packages/) | 浏览软件、选择版本、获取下载链接与安装命令 |
| [oheco-packages](https://github.com/oheco/oheco-packages) | 维护软件描述、版本信息和下载索引 |
| [参与生态建设](https://github.com/oheco/.github/blob/main/CONTRIBUTING.md) | 申请适配和维护一个开源项目 |

组织面向 OpenHarmony 生态持续扩展；**当前包管理器支持 HarmonyOS arm64，软件包平台标识为 `ohos-arm64`**。具体软件的支持范围以下载站和项目说明为准。

## 安装 oheco

在 **HarmonyOS 原生 zsh 终端**中执行安装命令。安装器会检查宿主系统与架构，请勿在 Linux 子系统中运行。

安装需要能够访问 GitHub Releases 和下载站，并具备 `curl`、`tar`、`sha256sum`（或 `shasum`）及基础文件命令；无需预先安装 Go、Git 或 jq。

```zsh
curl -fsSL https://oheco.github.io/oheco-packages/install.sh | zsh
```

默认安装目录为 `~/.oheco`。安装脚本会向 zsh 配置文件（通常为 `~/.zshrc`）添加命令路径。安装成功后，重新打开终端，或者在当前终端执行：

```zsh
export PATH="$HOME/.oheco/bin:$PATH"
oo --version
```

使用自定义安装目录时，请先在 zsh 配置文件中添加 `export OHECO_ROOT="/实际安装目录的绝对路径"`，加载配置后再执行安装命令。安装器会配置对应的 `PATH`；后续终端也需要保留该 `OHECO_ROOT` 配置，才能管理同一目录中的软件。

## 案例：下载并安装 Go

以下以 Go 工具链为例，演示从查找软件到开始使用的完整过程。

### 1. 更新目录并查找软件

```zsh
oo update
oo search go
oo info go
```

`oo update` 将远端软件索引同步到本地；`oo search go` 查找相关软件；`oo info go` 查看 Go 的可用版本、下载地址和软件说明。可用版本以最新索引为准。

### 2. 下载并安装

```zsh
oo install go
```

这条命令会下载当前平台在索引中标记为最新的 Go 发行包，校验文件大小和 SHA-256，解压到 `~/.oheco/packages/go/<版本>/`，并启用该版本的 `go` 和 `gofmt` 命令。

也可以指定包版本。Go 的鸿蒙适配包 `1.27.1-ohos.1` 对应上游 Go `1.27.1`：

```zsh
oo install go@1.27.1-ohos.1
```

### 3. 确认安装结果

```zsh
go version
oo list
```

`go version` 显示已启用的 Go 版本；`oo list` 列出已安装的软件和版本，其中 `*` 表示当前启用的版本。

安装 Go 工具链后，编译新程序还需要宿主 `PATH` 中的 `binary-sign-tool`，可通过 `oo install ohos-sdk-toolchains` 安装；使用 cgo 还需要 OHOS LLVM/SDK，可通过 `oo install ohos-sdk-native` 安装。请按 [Go 适配说明](https://github.com/oheco/go/blob/go1.27.1-ohos.1/misc/harmony/README.md)配置工具链，并将 `TMPDIR` 指向当前应用的私有可写目录。这些依赖需要单独安装。

### 只下载软件包

如果希望自行保存发行包，可在[软件下载站](https://oheco.github.io/oheco-packages/)搜索 `go`，选择版本，点击“下载软件包”，并展开 SHA-256 信息用于核验文件。也可以直接打开 [Go Releases](https://github.com/oheco/go/releases)，在对应版本的 Assets 中下载适配包，例如 `go1.27.1.ohos-arm64.tar.gz`。

下载预编译软件时请选择适配包；GitHub 自动提供的 `Source code` 是源码归档。浏览器下载后，软件尚未安装，也不会自动配置命令路径；希望自动完成这些步骤时，使用 `oo install go`。

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `oo update` | 更新本地软件索引 |
| `oo search` | 浏览索引中的软件 |
| `oo info go` | 查看 Go 的版本和详情 |
| `oo install go` | 安装并启用索引为当前平台指定的最新 Go 版本 |
| `oo install go@1.27.1-ohos.1 --no-switch` | 安装指定版本，保留当前启用状态 |
| `oo switch go 1.27.1-ohos.1` | 切换到已安装的指定版本 |
| `go@1.27.1-ohos.1 version` | 直接调用已安装的指定 Go 版本 |
| `oo list` | 查看已安装软件与当前启用版本 |
| `oo remove go@1.27.1-ohos.1` | 卸载指定版本 |
| `oo remove go --all` | 卸载 Go 的全部已安装版本 |
| `oo update && oo install oheco` | 更新索引并安装、启用最新的 oheco 包管理器 |
| `oo --help` | 查看命令帮助 |

`oo update` 只更新目录，不升级已安装的软件。要获取新版软件，先执行 `oo update`，再执行 `oo install <包名>`。切换版本只对已安装版本生效；卸载当前启用版本后，需要手动切换到其他已安装版本。

## 参与 OpenHarmony 开源生态建设

如果你是开发者和开源维护者，愿意适配并持续维护一个开源项目，欢迎阅读[参与指南](https://github.com/oheco/.github/blob/main/CONTRIBUTING.md)，选定项目及其 Release 版本后，在本组织的 [`.github` 仓库提交 Issue](https://github.com/oheco/.github/issues/new)。

## 维护者

### 组织维护者

| 姓名 | GitHub |
| --- | --- |
| Guo Wei | [@kdada](https://github.com/kdada) |

### 开源项目维护者

| 项目 | 维护者 |
| --- | --- |
| [oheco](https://github.com/oheco/oheco) | Guo Wei（[@kdada](https://github.com/kdada)） |
| [ohos-sdk](https://github.com/oheco/ohos-sdk) | Guo Wei（[@kdada](https://github.com/kdada)） |
| [go](https://github.com/oheco/go) | Guo Wei（[@kdada](https://github.com/kdada)） |
