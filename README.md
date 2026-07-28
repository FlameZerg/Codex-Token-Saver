# 💰 Codex Token Saver

> ⚡ **Stop Paying for Idle Heartbeats! Save 10% - 90% Tokens on Codex CLI & IDE.**  

[English Documentation](README.md) | [中文文档](README_CN.md)

![Node.js Version](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Token Savings](https://img.shields.io/badge/token__savings-10%25--90%25-green)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-orange)

---

## 💰 How Does It Save 10% - 90% Tokens?

By default, when OpenAI Codex CLI or IDE triggers an `exec` tool call, the upstream server returns a 30-second timeout (`yield_time_ms = 30000`).  
During long tasks, compilation, or while waiting for user confirmation, Codex **re-sends the entire conversation context every 30 seconds**. Because Codex contexts often grow to tens or hundreds of thousands of tokens, this frequent polling drains your API quota rapidly.

**Codex Token Saver Solution:**
* Intercepts SSE responses in memory and extends `yield_time_ms` to **12 hours (43,200,000 ms)**.
* Stops unnecessary 30s context-reloading loops when suspended.
* **Saves 10% to 90% of total Token consumption and API costs** during multi-turn tool calls and long conversations!

---

## 📊 Token Consumption Comparison

| Scenario / Metric | Default Codex CLI/IDE (No Proxy) | With Codex Token Saver (Active) | Token Savings Rate |
| :--- | :--- | :--- | :--- |
| **Waiting for User Confirmation** | Re-sends full context every 30s | Suspends for 12 hours (**0 API calls**) | **95%+ Saved** |
| **Multi-turn Tool Execution** | Frequent polling reloads full context | Only queries LLM when work completes | **50% - 80% Saved** |
| **Architecture Review & Chat** | Continuous background polling | Yields turn until next prompt | **30% - 70% Saved** |

---

## 🌟 Key Features

- 💰 **Massive 10%-90% Token Savings**: Intercepts tool call timeouts to 12 hours, breaking 30s polling loops and protecting your API quota.
- 🔑 **Native Auth Passthrough**: 100% transparent forwarding for official `OAuth Session` tokens (Bearer sess- / ChatGPT-Account-ID) and `API Key` (Bearer sk-).
- ⚡ **Zero External Dependencies**: Built entirely with native Node.js `http`, `https`, and `tls` modules. No `npm install` needed; starts in milliseconds.
- 💥 **Zero-Crash Architecture**: Features dual-stream disconnect protection (`!res.writableEnded`), gracefully swallowing GFW/TLS socket resets (`ECONNRESET`) without crashing.
- 🧹 **Zero Memory Leaks**: Uses pure binary stream piping with no global response buffering. Memory footprint stays constant at ~15MB.
- 🌐 **Upstream Proxy Chaining**: Native support for forwarding through local Clash / V2Ray proxy ports (127.0.0.1:7890).

---

## 🚀 Quick Start

### 1. Run the Proxy
Ensure [Node.js](https://nodejs.org/) (>= 18.0.0) is installed. Run directly without installing any packages:

```bash
node proxy.js
```

### 2. Configure Codex `config.toml`
Edit your `~/.codex/config.toml` file:

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

## ⚙️ Configuration

You can adjust mode flags at the top of `proxy.js`:

| Option | Default | Description |
| :--- | :--- | :--- |
| `purePassthroughMode` | `false` | `false`: Enables Token Saver mode & 12h timeout extension (Recommended); `true`: Pure binary passthrough. |
| `useUpstreamProxy` | `true` | Forward requests via local proxy (e.g. Clash at 127.0.0.1:7890). |
| `YIELD_TIMEOUT_MS` | `43200000` | Extended hang timeout duration in milliseconds (Default: 12 hours). |

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
