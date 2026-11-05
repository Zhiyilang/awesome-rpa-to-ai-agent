# Awesome RPA → AI Agent 工程

> 给**影刀 / 来也 UiBot 的 RPA 开发**一条能落地的转型路线：把流程自动化经验迁移到 LLM / AI Agent 工程。
> 作者本人就是 RPA 开发转 Agent 工程，以下每一项都经过实跑或长期关注，不是收藏夹吃灰清单。

[![stars](https://img.shields.io/github/stars/Zhiyilang/awesome-rpa-to-ai-agent?style=social)](https://github.com/Zhiyilang/awesome-rpa-to-ai-agent)

---

## 为什么 RPA 开发天然适合做 Agent 工程

- 你已经懂**流程编排、异常处理、选择器/反模式、与业务系统对接**——这正是 Agent 工作流的核心。
- 差别只是「执行节点」从录制的 UI 操作，换成「能推理的 LLM 调用」。
- 转型路径：**RPA 流程 JSON → 可运行的 Agent 计划 → 接 LLM → 接业务系统 API**。

## 学习路线（按优先级）

1. Python 标准库打底（能写零依赖脚本，见下方工具集）
2. 跑通一个 Agent 框架的最小例子
3. 把一段真实 RPA 流程改写成 Agent 计划
4. 接一个真实业务系统（ERP / 钉钉 / 飞书 / 数据库）
5. 用竞赛/赏金验证能力、攒作品集

## Agent 框架（选一个先跑通）

| 框架 | 适合 | 链接 |
|------|------|------|
| **LangGraph** | 有状态、可控的多步工作流 | [github.com/langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) |
| **AutoGen** | 多智能体对话/协作 | [github.com/microsoft/autogen](https://github.com/microsoft/autogen) |
| **CrewAI** | 角色化团队（角色+任务） | [github.com/crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) |
| **Agno** | 轻量、多模态 Agent | [github.com/agno-agi/agno](https://github.com/agno-agi/agno) |
| **smolagents** | HuggingFace 出品，极简 CodeAgent | [github.com/huggingface/smolagents](https://github.com/huggingface/smolagents) |

> 新手建议：先 LangGraph 或 smolagents 跑通一个「读文件→调用 LLM→写结果」的最小闭环。

## 把 RPA 流程编译成 Agent

- **影刀 RPA**：[xiaoding.com](https://www.xiaoding.com/)（流程可导出，逻辑可映射为 Agent 计划）
- **来也 UiBot**：[laiye.com](https://www.laiye.com/)（UiBot Studio 流程 JSON → 可改写为 Python Agent 节点）
- 思路：把「点击/输入/读取」节点替换为「工具调用」，把「分支/循环」保留为控制流，加一个 LLM 决策节点。

## 零依赖 Python 工具（作者维护，单个 .py 即可运行）

全部纯 Python 标准库、无 `pip install`、可审计、跨平台：

- **[billfusion](https://github.com/Zhiyilang/billfusion)** — 合并微信/支付宝 CSV 账单，自动分类去重
- **[lanshare](https://github.com/Zhiyilang/lanshare)** — 同 Wi-Fi 手机⇄电脑互传文件（纯 stdlib HTTP 服务）
- **[dupescan](https://github.com/Zhiyilang/dupescan)** — SHA-256 跨目录查重复文件
- **[csvdiff](https://github.com/Zhiyilang/csvdiff)** — 按主键对比 CSV/JSON，字段级变更报告
- **[md2html](https://github.com/Zhiyilang/md2html)** — Markdown→带样式 HTML，离线零依赖
- **[dirsize](https://github.com/Zhiyilang/dirsize)** — 目录体积分析，找吃空间的大文件

## 能力扩展（工具/算力）

- **[browser-use](https://github.com/browser-use/browser-use)** — 让 Agent 操控浏览器
- **[Composio](https://composio.dev)** — 几百个 SaaS/业务系统的 Agent 工具集
- **[Modal](https://modal.com)** — 按需 GPU/算力，跑模型推理不必自备机器

## 部署与上线

- GitHub Pages（作品集/文档，零成本）
- 云函数 / 容器（把 Agent 包成 API）
- 微信/飞书机器人（把 Agent 接到日常沟通入口）

## 实战：用竞赛与赏金攒作品集

真实可投、且对转型最有帮助的赛道：

- **AI Agent / LLM 黑客松**（lablab.ai、SkillHub、各大云厂商定期办）
- **GitHub 赏金**（搜 `label:"💎 bounty"` 的开放 issue）
- **开源创新赛 / 直通赛**（中文赛普遍接受 Gitee 提交）

> 目标不是「赢大奖」，而是**每赛产出一份可公开、可演示的作品**，积少成多就是求职/接单的硬通货。

## 作者

支一郎 (Zhiyilang) — RPA 开发 → AI Agent 工程。作品集与接单入口：[zhiyilang.github.io](https://zhiyilang.github.io/)

---

*本清单持续更新。欢迎提 PR 补充你验证过的资源。*
