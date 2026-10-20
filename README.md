# PCL-Uncut

> 本仓库旨在**恢复原版 PCL2 中被删减的功能**。

基于 [Meloong-Git/PCL](https://github.com/Meloong-Git/PCL) 的 **2.99.0.0** 版本源码，只做最小必要的还原改动，其余代码与原版保持一致。

<br/>

## 为什么要做这个仓库

PCL2 从 **2.99.0.0** 起移除了 MC 下载功能，代码里新增了一个硬编码开关 `RestrictMcDownload`，把下载相关的入口全部关掉：

```vb
' Modules\ModSecret.vb
Public Const RestrictMcDownload As Boolean = True
```

这个常量被 6 处代码引用（加上定义本身共 7 处），等于一次性砍掉了原版长期提供的核心能力之一。本仓库把它改回 `False`，让这些功能重新可用：

| 位置 | 恢复后的行为 |
|---|---|
| `ModModpack.vb` | 整合包安装入口不再被拦截 |
| `ModModpack.vb` | 安装前的二次拦截跳过 |
| `PageDownloadInstall.xaml.vb` | 走正常的 `DlClientListLoader`，下载页可正常加载 |
| `PageDownloadModify.vb` | MC 版本卡片重新显示 |
| `PageDownloadCompDetail.xaml.vb` | 整合包条目重新提供「安装」按钮 |
| `FormMain.xaml.vb` | 不再弹出「暂时不再提供 MC 下载功能」的提示 |

<br/>

## 与原版的差异

**代码只有一行。**

```
Plain Craft Launcher 2\Modules\ModSecret.vb
- Public Const RestrictMcDownload As Boolean = True
+ Public Const RestrictMcDownload As Boolean = False
```

其余全部来自上游 **2.99.0.0** 的原样源码，没有其他功能性修改。版本号、更新日志、UI 文案都保持原样。

<br/>

## 下载

前往 [Releases](https://github.com/snake-aabb-wtf/PCL-Uncut/releases) 下载最新构建的 `PCL2-Uncut-*.exe`，双击即可运行，无需安装。

构建产物由本仓库的 GitHub Actions 从源码现场编译（`.github/workflows/build.yml`），不是对上游二进制做的补丁——因为 `RestrictMcDownload` 是 `Const`，编译器会在编译期把相关分支折叠掉，二进制里没有可翻的开关。

<br/>

## 已知限制

**微软登录与 CurseForge 不可用。** 这两项依赖上游仓库配置的 `CLIENT_ID` 与 `CURSEFORGE_API_KEY` secrets，本仓库没有配置，构建时被替换为空值。如果需要，可以自行申请后加到仓库的 Secrets 里，`build.yml` 会自动注入。

**MC 下载本身不受影响。** 它走 Mojang 官方源与 BMCLAPI 镜像，不需要上述密钥。

**部分资源源可能不可用。** 下载依赖的第三方镜像（BMCLAPI 等）由社区维护，是否可用不由本仓库控制。

<br/>

## 自行构建

仓库自带 GitHub Actions 工作流，支持手动触发：

1. Fork 或克隆本仓库
2. 在 Actions 页面选择 **Build** → **Run workflow**，勾选 `Release`
3. 构建完成后在 Artifacts 中下载

子模块 `MeloongCore` 需要一并拉取（工作流已配置 `submodules: true`）。

本地构建需要 Visual Studio Build Tools + .NET 桌面生成工具工作负载 + .NET Framework 4.8 目标包，然后：

```
git submodule update --init --recursive
msbuild "Plain Craft Launcher 2\Plain Craft Launcher 2.vbproj" -p:Configuration=Release
```

<br/>

## 声明

- 本项目是基于 [Meloong-Git/PCL](https://github.com/Meloong-Git/PCL) 的衍生修改版，**著作权归原作者所有**，遵循原仓库的 [LICENCE](LICENCE)。
- 原版移除相关功能是作者出于法规合规考虑所做的明确表态。本仓库不代表原作者立场，也不为其背书。
- 本仓库仅供个人学习与自用，**按「现状」提供，不提供任何明示或暗示的担保**。使用本仓库产生的任何后果由使用者自行承担。
- 如果你喜欢 PCL，请支持原作者：[PCL 下载](https://meloong.com/pcl) · [爱发电](https://meloong.com/afd/a/LTCat) · [BiliBili](https://space.bilibili.com/11343203/dynamic)

<br/>

### 上游资源

- [PCL 下载](https://meloong.com/pcl)：原版 PCL 正式版
- [PCL 功能投票](https://github.com/Meloong-Git/PCL/discussions/2)
- [帮助文档库](https://github.com/LTCatt/PCL2Help)：PCL 内置帮助文档
