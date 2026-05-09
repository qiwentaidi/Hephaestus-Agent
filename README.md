# Hephaestus Scan Agent

Hephaestus Scan Agent 是一个可独立部署的扫描节点运行目录，用于连接 Hephaestus 服务端并执行下发任务。

## 使用方法

1. 确保目录中存在以下内容：

```text
agent-standalone/
├── config.yaml
├── hephaestus-scan-agent
└── config/
    ├── crack/
    ├── dirsearch/
    └── pocs/
```

2. 首次启动前，如果当前目录不存在 `config.yaml`，Agent 会自动在二进制同级生成一份默认配置。

3. 按需修改生成后的 `config.yaml`。

推荐至少配置以下内容：

```yaml
database:
  type: postgres
  host: 127.0.0.1
  port: "5432"
  user: postgres
  password: postgres
  dbname: hephaestus
  sslmode: disable

agent:
  id: scan-agent-1
  displayName: Scan Agent 1
  version: stable
  capabilities:
    - webscan
    - portscan
    - crack
    - dirscan
    - jsscan
  server:
    address: http://你的Hephaestus服务IP:18181
```

说明：

- `database` 需要指向 Hephaestus 服务端正在使用的同一套数据库。
- `agent.server.address` 需要指向 Hephaestus 服务端地址。
- `config.yaml` 中不包含单独的许可证密钥字段。
- `AGENT_ID`、`AGENT_DISPLAY_NAME`、`AGENT_VERSION`、`AGENT_TYPES` 等环境变量会覆盖 `config.yaml` 中对应配置。

## 许可证说明

`scan-agent` 它启动后会先连接数据库，再读取 `license` 表中的许可证状态。

只有在以下条件同时满足时，Agent 功能才可用：

- 当前数据库中存在有效许可证记录。
- 许可证状态为 `valid`。
- 许可证版本为 `pro`。

因此，如果出现下面的报错：

```text
Agent 功能不可用: 当前许可证无效，无法使用 Agent 功能
```

通常表示以下几种情况之一：

- Agent 连到的不是服务端正在使用的数据库。
- 当前数据库里还没有完成许可证激活。
- 当前许可证已过期或不是 `pro` 版本。

## 常见排查

1. 先确认 Hephaestus 服务端本身已经完成许可证激活，并且功能正常。
2. 再确认 Agent 的 `config.yaml` 中 `database` 配置与服务端一致。
3. 确认 `agent.server.address` 可以访问到 Hephaestus 服务端。
4. 如果使用环境变量覆盖配置，检查是否误传了错误的数据库地址或 Agent 参数。

## 启动

macOS / Linux:

```bash
./hephaestus-scan-agent
```

Windows:

```powershell
.\hephaestus-scan-agent.exe
```
