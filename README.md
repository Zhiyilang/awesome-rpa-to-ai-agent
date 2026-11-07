# Awesome RPA → AI Agent 工程

> 给**影刀 / 来也 UiBot 的 RPA 开发**一条能落地的转型路线：把流程自动化经验迁移到 LLM / AI Agent 工程。
> 作者本人就是 RPA 开发转 Agent 工程，以下每一项都经过实跑或长期关注，不是收藏夹吃灰清单。

[![stars](https://img.shields.io/github/stars/Zhiyilang/awesome-rpa-to-ai-agent?style=social)](https://github.com/Zhiyilang/awesome-rpa-to-ai-agent)
[![blog](https://img.shields.io/badge/技术博客-已上线-blue)](https://github.com/Zhiyilang/blog)

---

## 为什么 RPA 开发天然适合做 Agent 工程

- 你已经懂**流程编排、异常处理、选择器/反模式、与业务系统对接**——这正是 Agent 工作流的核心。
- 差别只是「执行节点」从录制的 UI 操作，换成「能推理的 LLM 调用」。
- 转型路径：**RPA 流程 JSON → 可运行的 Agent 计划 → 接 LLM → 接业务系统 API**。

> 一句话定位：**你不是从零学编程，是把「会动的流程」升级成「会思考的流程」。** 这一步的竞争壁垒，比纯 CS 出身的人低得多。

## 学习路线（按优先级，别跳步）

1. **Python 标准库打底**——能写零依赖脚本（见下方工具集），这是你比很多「只调 API」的人强的地方。
2. **跑通一个 Agent 框架的最小例子**（下面 5 分钟示例）。
3. **把一段真实 RPA 流程改写成 Agent 计划**（见实战改写）。
4. **接一个真实业务系统**（ERP / 钉钉 / 飞书 / 数据库）。
5. **用竞赛 / 赏金验证能力、攒作品集**（见最后）。

---

## 5 分钟跑通你的第一个 Agent

用 [smolagents](https://github.com/huggingface/smolagents)（HuggingFace 出品，最轻量的 CodeAgent）：

```bash
pip install smolagents
```

```python
from smolagents import CodeAgent, DuckDuckGoSearchTool, InferenceClientModel

# 模型可换：HuggingFace InferenceClientModel（需 HF token）或 OpenAIModel（需 OPENAI_API_KEY）
agent = CodeAgent(
    tools=[DuckDuckGoSearchTool()],
    model=InferenceClientModel(model_id="Qwen/Qwen2.5-Coder-32B-Instruct"),
)

agent.run("上海到杭州的高铁最快多久？折算成豹子全速跑完巴黎艺术桥要几秒？")
```

**跑通后你立刻理解了 Agent 的三件事**：① 它自己写代码当「动作」；② 工具（search）是它的「手」；③ 模型是它的「脑子」。这和影刀里「流程 + 组件 + 变量」的结构是同构的。

> 零 API key 替代方案：用 `HfApi` 登录拿 token，或本地跑 `ollama` + `OpenAIModel(model_id="qwen2.5:7b", api_base="http://localhost:11434/v1")`。先把示例跑起来比纠结模型重要。

---

## 实战改写：影刀流程 → Agent 计划

**原 RPA 流程（影刀 / UiBot 伪代码）**——每天 9 点登录后台，下载昨日账单 CSV，汇总金额，发邮件给财务：

```
启动 → 打开浏览器(后台URL)
     → 输入账号密码(凭据)
     → 点击「导出账单」→ 等待下载完成
     → 读取 CSV → 按部门求和
     → 打开邮件客户端 → 发送汇总表给 finance@x.com
```

**改写成 Agent（同一个任务，但「判断」交给 LLM）**：

```python
from smolagents import CodeAgent, tool, InferenceClientModel
import csv, smtplib, datetime

@tool
def download_bill() -> str:
    """登录后台并下载昨日账单 CSV，返回本地路径。"""
    # 这里复用你影刀里已有的登录/下载逻辑（Selenium / requests / 影刀组件导出）
    return "/tmp/bill_yesterday.csv"

@tool
def sum_by_department(path: str) -> dict:
    """读取 CSV，按部门汇总金额。"""
    out = {}
    with open(path, newline="", encoding="utf-8") as f:
        for row in csv.DictReader(f):
            out[row["dept"]] = out.get(row["dept"], 0) + float(row["amount"])
    return out

@tool
def send_email(summary: dict) -> str:
    """把汇总表发送给财务。"""
    body = "\n".join(f"{k}: {v:.2f}" for k, v in summary.items())
    # smtp 发送逻辑（略）
    return "sent"

agent = CodeAgent(
    tools=[download_bill, sum_by_department, send_email],
    model=InferenceClientModel(),
)
agent.run("执行每日账单汇总任务，发给财务。")
```

**关键迁移点**：
- 影刀的「点击/输入/读取」节点 → 上面的 `@tool` 函数（你已有的自动化经验直接复用）。
- 影刀的「分支/循环」控制流 → Agent 自己决定调用顺序（LLM 规划）。
- 你的优势在 **tool 层**（业务系统对接），不是模型层。把 tool 写稳，Agent 就稳。

---

## Agent 框架怎么选（先跑通一个）

| 框架 | 适合 | 上手成本 | 链接 |
|------|------|----------|------|
| **smolagents** | 极简 CodeAgent，新手首选 | 低 | [github.com/huggingface/smolagents](https://github.com/huggingface/smolagents) |
| **LangGraph** | 有状态、可控的多步工作流 | 中 | [github.com/langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) |
| **AutoGen** | 多智能体对话/协作 | 中 | [github.com/microsoft/autogen](https://github.com/microsoft/autogen) |
| **CrewAI** | 角色化团队（角色+任务） | 中 | [github.com/crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) |
| **Agno** | 轻量、多模态 Agent | 低 | [github.com/agno-agi/agno](https://github.com/agno-agi/agno) |

> 新手建议：**smolagents 跑通最小闭环 → LangGraph 学状态编排**。别一上来就追「多智能体炫技」，先把单 Agent + 好 tool 做扎实。

---

## 零依赖 Python 工具（作者维护，单个 .py 即可运行）

全部纯 Python 标准库、无 `pip install`、可审计、跨平台——这是 RPA 转 Agent 最该练的「底层手感」：

- **[billfusion](https://github.com/Zhiyilang/billfusion)** — 合并微信/支付宝 CSV 账单，自动分类去重
- **[lanshare](https://github.com/Zhiyilang/lanshare)** — 同 Wi-Fi 手机⇄电脑互传文件（纯 stdlib HTTP 服务）
- **[dupescan](https://github.com/Zhiyilang/dupescan)** — SHA-256 跨目录查重复文件
- **[csvdiff](https://github.com/Zhiyilang/csvdiff)** — 按主键对比 CSV/JSON，字段级变更报告
- **[md2html](https://github.com/Zhiyilang/md2html)** — Markdown→带样式 HTML，离线零依赖
- **[dirsize](https://github.com/Zhiyilang/dirsize)** — 目录体积分析，找吃空间的大文件

---

## 常见坑（RPA 开发转 Agent 最容易踩）

1. **把 Agent 当「更聪明的影刀」**——Agent 不保证每步成功，必须给失败兜底（重试 / 人工审核节点），不像录制流程那么确定。
2. **tool 写得不纯**——tool 里混了业务逻辑和 LLM 调用，导致不可复现。原则：tool 只做「确定动作」，判断交给模型。
3. **没有评估**——先写 `eval.py` 断言关键输出，再迭代。作者每个工具都带测试。
4. **过度依赖云模型**——核心流程尽量本地可跑，避免「断网即崩」。
5. **忽视成本**——每次 `agent.run` 都是真金白银的 token，先在小任务上估量。

---

## 能力扩展（工具 / 算力）

- **[browser-use](https://github.com/browser-use/browser-use)** — 让 Agent 操控浏览器（替代影刀的 UI 自动化）
- **[Composio](https://composio.dev)** — 几百个 SaaS/业务系统的 Agent 工具集
- **[Modal](https://modal.com)** — 按需 GPU/算力，跑模型推理不必自备机器

## 部署与上线

- **GitHub Pages**（作品集/文档，零成本）——[作者的作品集](https://zhiyilang.github.io/)
- 云函数 / 容器（把 Agent 包成 API）
- 微信 / 飞书机器人（把 Agent 接到日常沟通入口）

---

## 实战：用竞赛与赏金攒作品集

真实可投、且对转型最有帮助的赛道：

- **AI Agent / LLM 黑客松**（lablab.ai、SkillHub、各大云厂商定期办）
- **GitHub 赏金**（搜 `label:"💎 bounty"` 的开放 issue）
- **开源创新赛 / 直通赛**（中文赛普遍接受 Gitee 提交）

> 目标不是「赢大奖」，而是**每赛产出一份可公开、可演示的作品**。积少成多就是求职/接单的硬通货——作者本人就是靠一条条 autonomous 作品在 GitHub 上铺出接单入口的。

---

## 延伸阅读（作者博客）

> 博客已迁移到 GitHub 仓库（即时渲染、Google 高权重收录，比 Pages 更稳）：[github.com/Zhiyilang/blog](https://github.com/Zhiyilang/blog)

- [RPA 流程一键变成 MCP Server](https://github.com/Zhiyilang/blog/blob/main/01-rpa2mcp.md) — 把影刀/UiBot 组件暴露成标准协议
- [零依赖 UiBot Excel 三件套](https://github.com/Zhiyilang/blog/blob/main/02-uibot-excel.md) — 不装 Office 也能处理报表
- [RPA → 测试：mock 与 dry-run](https://github.com/Zhiyilang/blog/blob/main/03-rpa2test.md) — 给自动化加安全网
- [零依赖调用大模型](https://github.com/Zhiyilang/blog/blob/main/04-llm-client.md) — 不装 openai 包，纯 urllib 调 LLM

## 作者

支一郎 (Zhiyilang) — RPA 开发 → AI Agent 工程。作品集与接单入口：[zhiyilang.github.io](https://zhiyilang.github.io/)

---

*本清单持续更新。欢迎提 PR 补充你验证过的资源。*

---
> 💼 **需要定制 / 接单？** 发邮件 **someonelikebaku@163.com**（标题写「接单 + 需求类型」，工作日 24h 内由自动巡检接住回信）。
> 接单门面（明码标价 + 付款码 + 22 件成品货架）：https://mcd-persona-card.app.workbuddy.host/ ｜ 作品集与开源工具：https://zhiyilang.github.io/
