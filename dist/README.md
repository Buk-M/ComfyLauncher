# ComfyLauncher

**一款把 ComfyUI 版本控制权交还给用户的 Windows 启动器。**

单文件免安装、本地 Web 界面、绿色便携。双击 `ComfyLauncher.exe` 即可管理 ComfyUI 的启停、版本、插件、依赖与模型路径，无需记忆命令行参数。

- 版本：`0.12.4`
- 平台：Windows 10 / 11（x64）
- 协议：MIT
- 界面语言：简体中文（内置深色主题）

---

## 为什么需要它

ComfyUI 本身是一个 Python 项目，日常使用却要面对一堆琐事：

- 想切回上一个稳定版本，却不确定 `git` 该执行什么命令，还担心本地改动被覆盖；
- 装插件要手动 clone、处理依赖、重启验证，卸载时更是一团乱；
- 想看服务到底报了什么错，只能翻黑窗口或者去翻日志文件；
- 启动参数、镜像源、CUDA 设备、模型目录散落在各个配置文件里。

ComfyLauncher 把这些操作收敛到一个图形界面里，并遵循一个核心原则：

> **默认不自动改动你的 git 仓库。** 版本切换、插件更新等操作前会先检测本地改动并提供保护 / 打补丁 / 回滚，控制权始终在用户手上。

---

## 功能特性

### 🏠 首页

- 一键 **启动 / 停止** ComfyUI 进程，无需打开命令行
- 实时显示服务运行状态（运行中 / 已停止 / 端口占用等）
- 快捷打开 ComfyUI 工作台（默认 `http://127.0.0.1:8188`）
- 自动探测 GPU、环境与整合包目录，首次使用即可跑通

### 🖥 控制台

- 实时滚动查看 ComfyUI 进程的标准输出 / 错误日志
- 节点加载失败、依赖缺失、CUDA 报错等问题直接在界面里就能看到
- 不再需要盯着黑窗口，也不用手动翻日志文件

### 🧬 版本管理

- 查看 / 切换 / **锁定** ComfyUI 版本（锁定后不会被自动更新改动）
- 自动识别当前版本号，支持手动指定目标版本
- **本地改动保护**：切换前检测工作区是否有未提交修改，可选择打补丁保存
- 一键 **回滚** 到切换前状态（基于 `te-local-changes.patch` + 守护脚本）
- 关闭不必要的自动 git 操作，避免"用着用着代码被改了"

### 🧩 插件管理

- 浏览已安装的自定义节点插件
- 从仓库安装新插件、更新已有插件、卸载插件
- 幂等安装：重复安装同一插件不会产生重复副本或残留目录
- 插件更新源支持镜像加速（可配置 githubfast.com 等代理）

### 📦 依赖库

- 查看当前 Python 环境已安装的依赖包及版本
- 安装 / 升级 / 卸载 Python 包
- 支持配置 pip 镜像源（清华、腾讯等国内镜像）

### 🗂 模型路径

- 可视化管理 `extra_model_paths.yaml` 风格的模型目录映射
- 自动映射（auto map）模式：自动把散落的模型目录纳入 ComfyUI 识别范围
- 覆盖 checkpoints / diffusion_models / unet / text_encoders / clip / vae / loras / controlnet / upscale_models 等类型

### ⚙ 设置

- **服务器**：IP、端口、协议、启动参数、CUDA 设备、是否自动打开浏览器
- **路径**：ComfyUI 目录、Python 解释器、输出目录、工具链目录（git / ffmpeg / cmake / ninja）
- **镜像**：pip 源、HuggingFace 源、插件更新源
- **界面**：主题、语言
- 所有配置项可视化编辑，改动即时写回 `config.json`

### 🛠 其他

- **工作流适配**：批量整理 / 规范化工作流文件（选择目录或单文件）
- **开发 / 发布**：打包、上传发布产物到指定目标目录
- **自更新**：通过 manifest 检查并更新启动器自身
- **AI 辅助**：可配置 DeepSeek API Key，用于辅助分析

---

## 快速开始

### 系统要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 10 / 11 x64 |
| ComfyUI | 已存在的 ComfyUI 安装目录（便携整合包 / git 克隆均可） |
| Python | 已存在的 Python 3.10+ 解释器（整合包自带的 `python_embeded` 即可） |
| 磁盘 | 约 30 MB（启动器本身） |

> 启动器**不内置** Python 与 ComfyUI，它只负责管理你已有的那份安装。

### 使用步骤

1. 把 `ComfyLauncher.exe` 放到任意目录（建议放在 ComfyUI 整合包根目录下）
2. **双击 `ComfyLauncher.exe`**
3. 首次运行会在 exe 同目录生成 `config.json`，进入 **设置** 页填好：
   - **ComfyUI 目录**：你的 ComfyUI 项目路径
   - **Python 路径**：对应的 `python.exe`
   - **输出目录**：ComfyUI 的 output 目录
4. 回到 **首页**，点击 **启动**
5. 浏览器自动打开工作台，或点击界面上的快捷按钮打开

启动器自身监听本地 `http://127.0.0.1:8899`（仅本机访问）。

### 首次运行生成的目录

```
ComfyLauncher.exe 同级目录/
├── ComfyLauncher.exe     # 启动器（本仓库发布物）
├── config.json           # 配置文件（首次运行自动生成）
├── config/               # 迁移数据（FK 旧配置、GPU 信息等）
├── core/state/           # git 状态快照
├── core/patches/         # 本地改动补丁与守护脚本
└── logs/                 # 运行日志（如有）
```

> 配置是纯 JSON，可直接用文本编辑器修改，也可在界面里改。

---

## 配置说明（`config.json` 主要字段）

| 字段 | 说明 | 默认 |
|------|------|------|
| `comfyui_path` | ComfyUI 项目目录 | 空（需填写） |
| `python_path` | Python 解释器路径 | 空（需填写） |
| `output_path` | ComfyUI 输出目录 | 空 |
| `server.ip` / `server.port` | ComfyUI 监听地址与端口 | `127.0.0.1` / `8188` |
| `server.extra_args` | 启动 ComfyUI 时附加的参数 | 空 |
| `server.cuda_device` | 指定 CUDA 设备（如 `0`） | 空 = 自动 |
| `server.auto_launch` | 打开启动器时是否自动拉起 ComfyUI | `true` |
| `mirrors.pip` | pip 镜像源 | 空 |
| `mirrors.plugin_update` | 插件更新加速源 | `githubfast.com` |
| `tools.*` | git / ffmpeg / cmake / ninja / comfyui-api 路径 | `tools/` 下 |
| `version.lock_enabled` | 是否锁定 ComfyUI 版本 | `false` |
| `version.patches_enabled` | 是否启用本地改动补丁保护 | `true` |
| `modelpaths.auto_map` | 是否自动映射模型目录 | `true` |
| `deepseek.api_key` | DeepSeek API Key（可选） | 空 |
| `update.manifest_url` | 自更新 manifest 地址 | 空 |
| `ui.theme` / `ui.language` | 主题 / 语言 | `dark` / `zh` |

---

## 界面导航

| 页面 | 作用 |
|------|------|
| 首页 | 启动 / 停止、状态、快捷打开工作台 |
| 控制台 | 实时服务日志 |
| 版本管理 | 版本切换、锁定、改动保护与回滚 |
| 插件管理 | 插件浏览 / 安装 / 更新 / 卸载 |
| 依赖库 | Python 依赖查看与管理 |
| 设置 | 路径、服务器、镜像、模型路径、界面配置 |
| 关于 | 版本信息、更新检查 |

---

## 常见问题

**Q：双击后没反应 / 打不开界面？**
先确认是否被安全软件拦截；exe 默认监听 `127.0.0.1:8899`，端口被占用时会在日志中提示，可在 `config.json` 中调整或释放端口。

**Q：启动 ComfyUI 失败？**
到 **控制台** 页查看实时输出，最常见原因是 `python_path` / `comfyui_path` 填写不正确，或依赖未安装。

**Q：会不会把我的 ComfyUI 改坏？**
不会主动改。版本切换前会检测本地改动，默认走"打补丁保存 → 切换 → 可回滚"的路径，且可随时关闭自动 git 操作。

**Q：必须用便携整合包吗？**
不必。任何本地 ComfyUI 安装（git 克隆或便携包）都可以，只要在设置里指对目录和 Python 解释器。

**Q：可以卸载吗？**
可以。删除 exe 及其同目录生成的 `config.json`、`config/`、`core/state/` 即可，不会触碰 ComfyUI 本身。

---

## 免责声明

- 本工具为第三方启动器，与 ComfyUI 官方无关联。
- 涉及 git 切版本、安装 / 更新插件等操作前请自行备份重要改动。
- 使用者需自行承担因配置不当或操作失误导致的数据风险。

---

## 开源协议

[MIT License](./LICENSE) © 2026 buk-m

自由使用、修改、分发，保留版权与许可声明即可。
