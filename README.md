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

4. 启动 Agent：

macOS / Linux:

```bash
./hephaestus-scan-agent
```

Windows:

```powershell
.\hephaestus-scan-agent.exe
```
