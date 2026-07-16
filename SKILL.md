---
name: a-share-stock-dossier
description: Generate a structured Chinese A-share stock due-diligence dossier from
  Pandadata interfaces, covering company profile, financials, dividends and capital
  actions, shareholder behavior, pledge/unlock/reduction risks, market funds, and
  sourced appendices. Use when the user asks for A股个股体检、个股尽调、公司全面分析、股票基本面报告、质押解禁减持风险排查,
  or a one-stop A-share company dossier.
license: GPL-3.0-only
metadata:
  organization: QuantSkills
  organization_url: https://github.com/quantskills
  repository: skill-a-share-stock-dossier
  repository_url: https://github.com/quantskills/skill-a-share-stock-dossier
  project_type: skill
  collection: a-share-stock-dossier
  creator: abgyjaguo
  maintainer: abgyjaguo
quantSkills:
  project_type: skill
  category: analyst
  tags:
  - a-share
  - stock-dossier
  - fundamentals
  - due-diligence
  - pandadata
  platforms:
  - claude-code
  - codex
  - hermes
  - openclaw
  - cursor
  status: stable
  validation_level: runnable
  maintainer_type: official
  summary_zh: 输入一个 A 股代码，输出一份可溯源的中文个股尽调报告：基本面、分红资本运作、股东行为、质押解禁减持风险、资金面，一次查清。
  summary_en: A-share stock dossier skill that uses Pandadata to produce company,
    financial, dividend, shareholder, and risk analysis.
  license: GPL-3.0
  requires:
  - skill-pandadata-api
---

```json qsh-form
{
  "version": 1,
  "task": {
    "placeholder": "补充尽调范围、关注风险或时间窗口；留空则按技能默认口径"
  },
  "fields": [
    {
      "key": "symbol",
      "label": "股票代码或名称",
      "type": "text",
      "placeholder": "如：600519.SH 或 贵州茅台",
      "required": true
    }
  ],
  "prompt_template": "{{#task}}任务与材料：\n{{task}}\n\n{{/task}}{{#attachments}}用户上传的材料（已放入工作区）：\n{{attachments}}\n\n{{/attachments}}请为 {{symbol}} 生成可溯源的 A 股个股尽调档案，覆盖公司画像、财务、分红与资本动作、股东行为、质押/解禁/减持风险、市场资金及来源附录，区分事实、衍生指标与判断，输出中文报告。"
}
```

# A-Share Stock Dossier

Use this skill to turn one A-share symbol, such as `000001.SZ`, into a sourced Chinese due-diligence report covering fundamentals, corporate actions, shareholder behavior, event risks, and market funds.

## Core Workflow

1. Normalize the target symbol to `XXXXXX.SH` or `XXXXXX.SZ`. If the user gives only a six-digit code, infer `SH` for `600/601/603/605/688/689` and `SZ` for `000/001/002/003/300/301`; ask only when the code is ambiguous.
2. Confirm the report scope. Default to the latest available data, three fiscal years of financial statements, one year of market-fund data, and twelve months of forward-looking unlock/reduction events unless the user specifies another window.
3. Read `references/dossier-guide.md` before the first dossier in a session. Use it for the API map, default risk thresholds, output blueprint, and appendix requirements.
4. Use the `pandadata-api` skill for all real data calls. Open its `references/method-index.md` and the exact method section in `references/api-docs.md` before calling `scripts/call_api.py`; do not invent parameters, fields, symbols, or credentials.
5. Collect evidence first, then analyze. Keep raw returned tables or row counts long enough to cite source method, data date/report period, and missing-data status in the final report.
6. Produce Markdown by default. If the user asks for Word, PDF, or a polished document, generate the analytical content here first, then use the relevant document skill for final layout.

## Analysis Rules

- Separate facts, derived metrics, and judgment. Label derived calculations such as growth rates, margins, ROE, leverage, cash-flow-to-profit matching, pledge ratios, unlock pressure, and holder-count changes.
- Sort multi-period tables by the relevant date field such as `end_date`, `ann_date`, `announcement_date`, `trade_date`, or the field documented by Pandadata before selecting the latest or windowed rows.
- Treat empty API results as evidence. State "无数据" with the method name and queried window instead of silently omitting the section.
- Use high/medium/low risk levels only when a rule in `references/dossier-guide.md` or a user-provided rule is triggered. Include the triggering rule text beside each risk item.
- End every report with this disclaimer: `本报告基于公开数据与规则化分析生成，仅供研究参考，不构成任何投资建议。`

## Resource Guide

- `references/dossier-guide.md`: API-stage map, metrics, risk rules, report outline, and final QA checklist.

## Quality Bar

- Every material claim must trace to a Pandadata method, report period/data date, and fetch window.
- Financial comparisons must use the same report period where possible; avoid mixing single-quarter and cumulative periods unless clearly labeled.
- Do not overstate causality from event signals. Prefer "可能提示", "需要关注", and "与...共同出现" when the data is only indicative.
