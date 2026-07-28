# 💰 Codex Token Saver

> ⚡ **拒绝为无意义的空闲心跳付费！最高可为您节省 10% - 90% 的 Codex API Token 消耗！**  

[English Documentation](README.md) | [中文文档](README_CN.md)

![Node.js Version](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Token Savings](https://img.shields.io/badge/token__savings-10%25--90%25-green)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-orange)

---

## 💰 为什么能暴降 10% ~ 90% 的 Token 用量？

在默认情况下，OpenAI Codex CLI 或 IDE 在触发 `exec` 工具调用时，官方服务端返回的等待超时机制只有 **30 秒** (`yield_time_ms = 30000`)。  
这会导致在长任务、编译构建、或等待用户思考确认的间隙，Codex 会**每隔 30 秒不断重新向云端 API 发起完整的对话上下文轮询**！由于 Codex 的 Context 往往长达数万至数十万 Token，这种频繁心跳轮询会快速榨干您的 Token 额度。

**Codex Token Saver 的解决方案：**
* 自动在内存中拦截 SSE 响应，将 `yield_time_ms` 动态扩展为 **12 小时（43200000 ms）**。
* 客户端挂起后不再发起无意义的 30 秒上下文重载轮询。
* **在复杂多轮工具调用与长对话场景中，直接帮您省下 10% 至 90% 的 Token 消耗与费用开销！**

---

## 📊 Token 消耗对比表

| 场景 / 指标 | 原生 Codex CLI/IDE (无代理) | 搭配 Codex Token Saver (开启本代理) | Token 节约率 |
| :--- | :--- | :--- | :--- |
| **等待用户确认 / 思考** | 每 30 秒刷新一次数万 Token Context | 静默挂起 12 小时，**0 频调用** | **省 95%+** |
| **长任务多轮工具调用** | 频繁心跳重载完整上下文 | 仅在必要时发起模型交互 | **省 50% - 80%** |
| **复杂架构讨论与调试** | 持续消耗 API 配额 | 挂起等待指令 | **省 30% - 70%** |

---

## 🌟 核心特性 (Key Features)

- 💰 **暴降 10%-90% Token 消耗**：拦截改写工具心跳延时至 12 小时，彻底斩断 30 秒轮询死循环，保护 API 额度与账号安全。
- 🔑 **原生 Auth 无损透传**：完美兼容官方 `OAuth Session` (Bearer sess- / ChatGPT-Account-ID) 与标准 `API Key` (Bearer sk-)。
- ⚡ **零依赖极简原生**：基于 Node.js 原生 `http` / `https` / `tls` 模块构建，无需任何 `npm install` 第三方依赖，毫秒级启动。
- 💥 **Zero-Crash 防崩溃架构**：内置双重流断连防护（`!res.writableEnded`），优雅吞吐 GFW / TLS 套接字重置（`ECONNRESET`），服务常驻永不宕机。
- 🧹 **内存零泄露设计**：纯二进制流式管道（Stream Piping）转发，无全局响应缓存，长时间运行内存恒定在十几 MB。
- 🌐 **灵活代理链支持**：支持自适应本地科学上网代理链（Clash / V2Ray 7890 端口转发）。

---

## 🚀 快速开始 (Quick Start)

### 1. 运行代理服务
确保安装了 [Node.js](https://nodejs.org/) (>= 18.0.0)，无需安装第三方依赖，直接运行：

```bash
node proxy.js
```

### 2. 配置 Codex `config.toml`
编辑您的 `~/.codex/config.toml` 文件：

```toml
model_provider = "openai_http"

[model_providers.openai_http]
name = "openai"
wire_api = "responses"
requires_openai_auth = true
supports_websockets = false
base_url = "http://127.0.0.1:8080"
```

---

## ⚙️ 模式配置 (Configuration)

在 `proxy.js` 头部可根据需求调整模式控制标志：

| 配置项 | 默认值 | 作用说明 |
| :--- | :--- | :--- |
| `purePassthroughMode` | `false` | `false`: 开启防心跳拦截与 12 小时超时扩展（推荐，省 Token 模式）；`true`: 纯二进制流透传。 |
| `useUpstreamProxy` | `true` | 是否通过本地代理（如 Clash 127.0.0.1:7890）链式出海转发。 |
| `YIELD_TIMEOUT_MS` | `43200000` | 超时时间扩展设定（默认 12 小时）。 |

---

## 📄 开源许可证 (License)

本项目基于 [MIT License](LICENSE) 开源。
