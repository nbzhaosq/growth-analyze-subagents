# ODPS 增长分析 Sub-Agent 套件

基于 [Qoder CLI Subagent](https://docs.qoder.com/zh/cli/subagent) 规范构建的 ODPS (MaxCompute) 数据分析代理套件，通过 6 个专业化 Agent 串联协作，端到端完成数据分析任务。

## 架构

```
用户输入
  │
  ▼
┌──────────────────────┐
│  requirement-analyzer │  需求分析
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  schema-explorer      │  表结构探索
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  growth-analyst       │  数据分析
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  result-validator     │  结果校验
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  report-writer        │  报告撰写
└──────────┬───────────┘
           │
           ▼
        最终报告
```

## Agent 说明

| Agent | 文件 | 职责 | 产出 |
|-------|------|------|------|
| `requirement-analyzer` | [requirement-analyzer.md](agents/requirement-analyzer.md) | 解析用户意图，输出结构化需求文档 | `output/requirement.md` |
| `schema-explorer` | [schema-explorer.md](agents/schema-explorer.md) | 探查 ODPS 表结构、字段语义、数据分布和质量 | `output/schema-analysis.md` |
| `growth-analyst` | [growth-analyst.md](agents/growth-analyst.md) | 编写并执行分析 SQL，覆盖增长/活跃/留存/漏斗/收入/渠道六大分析场景 | `output/analysis-result.md` |
| `result-validator` | [result-validator.md](agents/result-validator.md) | SQL 逻辑审查、数据合理性校验、交叉验证 | `output/validation-report.md` |
| `report-writer` | [report-writer.md](agents/report-writer.md) | 整合分析结果，输出含执行摘要、行动建议的完整报告 | `output/report.md` |
| `odps-orchestrator` | [odps-orchestrator.md](agents/odps-orchestrator.md) | 串联调度上述 5 个 Agent，管理质量门控和中间产物 | `output/progress.md` |

## 快速开始

### 1. 安装到项目

将 `agents/` 目录下的 `.md` 文件复制到目标项目的 `.qoder/agents/` 目录：

```bash
cp agents/*.md /your-project/.qoder/agents/
```

### 2. 使用方式

**端到端分析**（推荐，由 orchestrator 自动调度全流程）：

```
使用 odps-orchestrator subagent 分析过去30天的用户增长情况
```

**分步调用**（手动控制每一步）：

```
先使用 requirement-analyzer subagent 分析需求：最近一周各渠道新增用户留存率
然后使用 schema-explorer subagent 探索用户表和事件表结构
接着使用 growth-analyst subagent 执行留存分析
再使用 result-validator subagent 校验分析结果
最后使用 report-writer subagent 撰写报告
```

### 3. 支持的分析类型

| 类型 | 说明 | 关键指标 |
|------|------|---------|
| 用户增长 | 新增趋势、累计用户、渠道拆解 | 新增用户数、CAC、注册转化率 |
| 活跃分析 | DAU/MAU 趋势、活跃度分级 | DAU、MAU、DAU/MAU 比 |
| 留存分析 | 队列留存、功能留存、渠道留存 | 次日/7日/30日留存率 |
| 漏斗分析 | 多步转化率、流失点定位 | 各步转化率、整体转化率 |
| 收入分析 | 收入趋势、ARPU/LTV | MRR、ARPU、付费转化率 |
| 渠道分析 | 渠道质量排名、ROI | 渠道新增、ROI、渠道留存 |

## 目录结构

```
growth-analyze-subagents/
├── agents/                         # Agent 定义文件
│   ├── requirement-analyzer.md
│   ├── schema-explorer.md
│   ├── growth-analyst.md
│   ├── result-validator.md
│   ├── report-writer.md
│   └── odps-orchestrator.md
├── output/                         # 运行时中间产物（gitignore）
└── README.md
```

## 环境要求

- [Qoder CLI](https://qoder.com) 已安装并配置
- ODPS (MaxCompute) 连接已配置，agent 可通过 Bash 工具执行 SQL
- 建议配置 `odpscmd` 或 `pyodps` 作为 SQL 执行通道
