# Hephaestus Scan Agent

Hephaestus Scan Agent 是一个可独立部署的扫描节点运行目录，用于连接 Hephaestus 服务端并执行下发任务（**仅供Pro版本用户使用**）。

## 使用方法

1. 首次启动前，如果当前目录不存在 `config.yaml`，Agent 会自动在二进制同级生成一份默认配置。

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
  id: {{随机uuid不可更改}}
```

说明：

- `database` 需要指向 Hephaestus web端正在使用的同一套数据库。
- `config.yaml` 中不包含单独的许可证密钥字段。
- 如果同级的`config.yaml`不存在会顺延到替补配置文件`~/.config/hephaestus/config.yaml`

3. 配置服务端Agent管理页中的 [**设置 - Agent服务端地址**]

## 配置文件更新

新版中Agent空闲时每5分钟或者启动时向服务端确认配置文件版本，如果有更新会自动同步

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
