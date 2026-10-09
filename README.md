# Hephaestus Agent

Hephaestus 的独立 Agent 节点，支持扫描与 AI Worker。**仅供有效 Pro 授权用户使用。** 本仓库只发布说明、安装器和已编译发行包；主项目与 Agent 源码保持闭源，用户不需要源码或编译环境。

当前发行版本 **v0.4.6**，与 Hephaestus 主服务 **v0.4.6** 配套使用。

## 下载并安装

先启动新版主服务，完成 PostgreSQL 表迁移，确保节点能访问主服务和主服务使用的同一套 PostgreSQL。主服务必须提供 `/api/v1/agent/release`；旧进程需要更新并重启。将下列 `https://your-server` 替换为节点可访问的真实地址。

### Linux / macOS

在终端运行：

```bash
curl -fsSL https://github.com/qiwentaidi/Hephaestus-Agent/releases/download/v0.4.6/install.sh -o install.sh
bash install.sh --server 'https://your-server'
```

默认安装到当前目录下的 `hephaestus-agent`，支持 amd64 和 arm64。需要 Bash、curl 或 wget、unzip，以及 sha256sum 或 shasum。Linux 的 AI 执行工具仍需要 Bubblewrap；Python、stdio MCP 等运行依赖应在节点准备。

首次安装会询问 PostgreSQL 地址、账号、密码、数据库和节点角色。密码不回显，`config.yaml` 权限为 `0600`。已有配置不覆盖；非交互安装可传 `--config /path/to/node-config.yaml`。仅安装、不启动可传 `--no-start`；指定目录可传 `--install-dir /path/to/agent`。

默认在前台运行，正常退出使用 Ctrl+C。再次运行：

```bash
bash ./hephaestus-agent/run.sh
```

Linux 可选择安装 systemd 服务：

```bash
sudo bash install.sh --server 'https://your-server' --service
```

服务默认目录 `/opt/hephaestus/agent`，服务名 `hephaestus-scan-agent`。

### Windows amd64

下载 [install.bat](https://github.com/qiwentaidi/Hephaestus-Agent/releases/download/v0.4.6/install.bat) 后双击，或在 PowerShell 中运行：

```powershell
Invoke-WebRequest -Uri 'https://github.com/qiwentaidi/Hephaestus-Agent/releases/download/v0.4.6/install.bat' -OutFile install.bat
.\install.bat -Server 'https://your-server'
```

BAT 内嵌完整 PowerShell 安装器。默认安装到 `%LOCALAPPDATA%\Hephaestus\agent`，首次交互配置 PostgreSQL 与节点角色，数据库配置只允许当前用户和 SYSTEM 访问。参数支持 `-InstallDir`、`-ConfigFile`、`-NoStart`。

默认前台运行，正常退出使用 Ctrl+C。再次运行：

```powershell
& "$env:LOCALAPPDATA\Hephaestus\agent\run.bat"
```

## 后续更新

**先正常停止前台节点，再运行更新器。** 更新器每次都重新查询主服务要求的版本，不盲目追随 GitHub latest；先完成下载、SHA-256 校验和实际二进制版本检查，再替换程序。配置、节点 ID、角色、容量和资源目录保留，同时保留上一份程序 `.previous`。

Linux / macOS，在初次安装命令所在目录执行：

```bash
bash ./hephaestus-agent/update.sh
```

Linux systemd 安装可以自动正常停止并重启服务：

```bash
sudo bash /opt/hephaestus/agent/update.sh
```

停止没有确认时不替换程序，不强杀仍在运行的 Worker。新服务启动检查失败时尝试恢复上一程序；服务启动后仍需在 Agent 管理页确认版本和心跳。

Windows，在 PowerShell 中执行：

```powershell
& "$env:LOCALAPPDATA\Hephaestus\agent\update.bat"
```

自定义安装目录请使用该目录中的 run/update 脚本。Windows 安装器不强制终止任务，也不注册 Windows 服务；macOS 默认同样使用前台运行。

## 节点配置与授权

Agent 必须连接主服务使用的同一套 PostgreSQL。数据库地址和主服务地址都应从节点可达；远端机器上的 `127.0.0.1` 指向远端机器自身。

节点角色支持 `scan`、`ai` 或 `ai,scan`；AI 和扫描使用独立并发池。配置保存在节点安装目录的 `config.yaml`，环境变量覆盖仍按主程序规则生效。同一主服务数据库内，每台主机只允许一个运行中的节点，内置 Worker 与外部 Agent 共用该限制。

Agent 读取主服务数据库中的许可证状态，无需另填许可证密钥。启动失败时检查：

1. 主服务已激活有效 Pro 授权。
2. Agent 与主服务版本匹配，主服务已经更新并重启。
3. Agent 的 PostgreSQL 配置指向正确数据库且可连接。
4. 服务端「Agent 管理 → 设置」配置了节点可访问的主服务地址。
5. 原节点或本机内置 Worker 已正常停止，未重复启动。

模板、字典等资源由用户自己的主服务同步，不随公开发行包发布。首次部署完成后，在 Agent 管理页确认心跳、版本、角色与并发。

## 发行文件

[Releases](https://github.com/qiwentaidi/Hephaestus-Agent/releases) 提供 Linux/macOS amd64、arm64 和 Windows amd64 ZIP，以及 `install.sh`、`install.bat`、`install.ps1`、`release.json`、`SHA256SUMS.txt`。

GitHub 的 Source code ZIP/TAR 只包含本仓库的说明和安装脚本，并非 Agent 程序；需要手动安装时请选择对应平台的 `hephaestus-scan-agent_<os>_<arch>.zip`。
