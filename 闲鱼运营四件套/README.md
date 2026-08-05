---
AIGC:
    Label: "1"
    ContentProducer: 001191440300708461136T1XGW3
    ProduceID: 7d3f5a384d3ea7935c416546d8d2e517_0f87a37d908511f1bafa525400287e28
    ReservedCode1: jc58jzktvrCsF15ka6NdwSbJsWP4lPaOSQEjo9GHfBZPuIkEFyvRsPDlnyPE8h1XtD3Ef3XZCnKJHmgcdfLqu2pPAbyW7bwlyI5F//0Li7nupwcBYH8l2rU91yVd5P9I5O+3G7iYsnaR+9yzYF6em/tKnmtQqAyKA6PgyN9ur5y2TfTib0xN5ul2qWw=
    ContentPropagator: 001191440300708461136T1XGW3
    PropagateID: 7d3f5a384d3ea7935c416546d8d2e517_0f87a37d908511f1bafa525400287e28
    ReservedCode2: jc58jzktvrCsF15ka6NdwSbJsWP4lPaOSQEjo9GHfBZPuIkEFyvRsPDlnyPE8h1XtD3Ef3XZCnKJHmgcdfLqu2pPAbyW7bwlyI5F//0Li7nupwcBYH8l2rU91yVd5P9I5O+3G7iYsnaR+9yzYF6em/tKnmtQqAyKA6PgyN9ur5y2TfTib0xN5ul2qWw=
---

# 闲鱼运营四件套

桌面级多号自动化技能包，基于 Marvis Agent，底层保活统一，三个业务模块按需触发或常驻运行。

## 架构

```
xianyu-base（底层基础设施）
    │  CDP 三端口守护 · 反检测注入 · 统一保活
    │
    ├── xianyu-research → 每日竞品采集（100 帖归档，20 天清理）
    ├── xianyu-post     → AI 文案配图发帖（多号错时排期）
    └── xianyu-service  → 三级智能客服（规则引擎 → LLM → 定时兜底）
```

## 安装

1. 将四个 `.skill` 文件放入 Marvis 技能目录：
   ```
   skills\market\xianyu-base\
   skills\market\xianyu-research\
   skills\market\xianyu-post\
   skills\market\xianyu-service\
   ```
   方式：`.skill` 改名 `.zip`，用解压工具分别解压到对应子目录。

2. 重启 Marvis 或刷新技能列表。

## 部署前置条件

- Windows 10/11 + Edge 浏览器
- Python 3.11（daemon 依赖 `playwright`、`aiohttp`）
- 三个闲鱼账号，Edge Profile 独立配置

## 首次部署步骤

### 1. 部署 base 层脚本

将 `xianyu-base/scripts/` 下的文件复制到对应路径：

| 脚本 | 部署到 |
|------|--------|
| `cdp_guard.py` | `C:\Users\<用户名>\Coze\cdp_guard.py` |
| `inject-stealth.py` | `C:\Users\<用户名>\Coze\inject-stealth.py` |
| `xianyu_guard.ps1` | `C:\Users\<用户名>\.xianyu_scripts\xianyu_guard.ps1` |

### 2. 部署 service 层脚本

将 `xianyu-service/scripts/` 下全部文件复制到 `C:\Users\<用户名>\.xianyu_scripts\`。

### 3. 配置 Edge Profile

为三个闲鱼号分别创建独立 Profile，手动登录一次让 Cookie 持久化：

```powershell
start msedge --remote-debugging-port=9222 --user-data-dir="C:\Users\<用户名>\Coze\edge-xianyu-profile-1"
start msedge --remote-debugging-port=9223 --user-data-dir="C:\Users\<用户名>\Coze\edge-xianyu-profile-2"
start msedge --remote-debugging-port=9224 --user-data-dir="C:\Users\<用户名>\Coze\edge-xianyu-profile-3"
```

### 4. 配置 LLM（可选，仅 service 需要）

编辑 `C:\Users\<用户名>\.xianyu_scripts\xianyu_llm_config.json`：

```json
{
  "enabled": true,
  "api_url": "https://api.moonshot.cn/v1/chat/completions",
  "api_key": "你的API_KEY",
  "model": "kimi-k2.6",
  "timeout": 30,
  "max_tokens": 800,
  "system_prompt": "你是科研CRO服务商的客服代表...",
  "extra_params": { "temperature": 1, "top_p": 0.95 }
}
```

兼容 OpenAI 协议，换成 DeepSeek、通义千问等均可。

### 5. 创建计划任务

| 任务 | 脚本 | 频率 |
|------|------|------|
| XianyuGuard | `xianyu_guard.ps1` | 每 5 分钟 |
| AI 客服回复 | 自动读 pending.json → 生成回复 | 每 30 分钟 |

## 使用

- **调研**：对 Marvis 说「闲鱼调研」「采集竞品」
- **发帖**：对 Marvis 说「发帖：<主题>」
- **客服**：部署后自动运行，无需手动触发

## 安全

- 所有数据本地运行，不出电脑
- 发布包不含 API Key，需自行配置
- 三号独立 Profile，互不串号
*（内容由AI生成，仅供参考）*
