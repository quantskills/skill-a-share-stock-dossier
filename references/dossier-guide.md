# A-Share Dossier Guide

Read this guide when generating or revising an A-share stock dossier. Use it as a compact operating checklist, not as a replacement for the exact Pandadata API documentation.

## Default Scope

- Target: one A-share common stock in `XXXXXX.SH` or `XXXXXX.SZ` format.
- Financial window: latest available annual and interim reports across roughly three fiscal years.
- Market-fund window: latest one year unless the user requests a shorter event review.
- Event window: next twelve months for restricted-share unlocks; latest available announcements for reductions, pledges, ST changes, forecasts, and audit opinions.
- Output: Markdown report unless the user requests HTML, Word, PDF, or another deliverable.

## API Stage Map

Before calling any method, use the `pandadata-api` skill to confirm the exact parameters and return fields.

| Stage | Methods | Use |
|---|---|---|
| Company profile | `get_stock_detail`, `get_stock_industry`, `get_share_float`, `get_stock_status_change` | Identify company, listing status, industry, share capital, and ST or special-treatment history. |
| Financial statements | `get_fina_reports`, `get_fina_performance`, `get_fina_forecast`, `get_audit_opinion` | Build revenue/profit/cash-flow profile, identify forecast changes, and flag non-standard audit opinions. |
| Dividends and capital actions | `get_stock_dividend`, `get_stock_cash_dividend`, `get_stock_dividend_amount`, `get_repurchase`, `get_stock_private_placement`, `get_stock_allotment`, `get_stock_split`, `get_investor_activity` | Review shareholder returns, buybacks, refinancing, share changes, and investor-relations activity. |
| Shareholders and event risks | `get_top_holders`, `get_holder_count`, `get_stock_shareholder_change`, `get_stock_pledge`, `get_stock_pledge_stat`, `get_restricted_list` | Analyze holder concentration, holder-count trend, reductions, pledges, and unlock pressure. |
| Market funds | `get_stock_daily`, `get_lhb_list`, `get_lhb_detail`, `get_block_trade`, `get_margin`, `get_hsgt_hold` | Review price/volume, abnormal trading, block trades, margin financing, and northbound holdings. |

## Metrics To Derive

- Growth: revenue YoY, net profit YoY, operating profit YoY when fields exist.
- Profitability: gross margin, net margin, ROE or ROA when the required numerator and denominator are available.
- Solvency: asset-liability ratio, interest-bearing debt trend if fields exist.
- Cash quality: operating cash flow divided by net profit; mark negative or persistently weak matching.
- Capital returns: cash dividend frequency, total cash dividend amount, dividend payout ratio, and implied dividend yield when price and dividend fields support it.
- Ownership: top-holder concentration, controller/major-holder changes, holder-count QoQ or period-over-period change.
- Event pressure: unlock size versus float or market value, pledge ratio, planned reduction size, and whether multiple negative signals overlap.

State the formula and field names used whenever a metric is derived rather than directly returned.

## Risk Rules

Use these defaults unless the user supplies thresholds. If the data lacks a needed denominator, downgrade the rule to a qualitative watch item and say what is missing.

| Level | Trigger |
|---|---|
| High | Active or newly announced ST/*ST/delisting-risk status. |
| High | Non-standard audit opinion, qualified opinion, adverse opinion, or disclaimer of opinion. |
| High | Pledge ratio at or above 50%, or controlling shareholder pledge appears both high and rising. |
| High | Restricted-share unlock in the next 90 days above 10% of free float, or above 5% of current market value when market value is available. |
| High | Major shareholder reduction plan overlaps with profit-warning downgrade, first loss, continued loss, or large negative forecast revision. |
| Medium | Pledge ratio between 30% and 50%, or a new large pledge appears in the latest window. |
| Medium | Restricted-share unlock in the next 90 days between 5% and 10% of free float. |
| Medium | Holder count rises more than 20% while price is flat or weak over the same broad window. |
| Medium | No cash dividend for three consecutive fiscal years while refinancing, private placement, or allotment is frequent. |
| Low | Single isolated event with limited size, stale date, or incomplete denominator; include it in the appendix instead of the executive risk list when immaterial. |

For combined signals, describe the combination explicitly, for example `质押率高 + 解禁临近` or `减持计划 + 业绩预告下修`.

## Report Blueprint

Use this chapter order unless the user asks for a custom structure:

1. `摘要与结论`: three to six bullets covering business profile, financial trend, key positives, key risks, and data freshness.
2. `公司概况`: basic info, industry, listing status, share capital, and recent special-treatment history.
3. `财务分析`: core financial tables, trend charts if requested, derived metrics, and audit/forecast interpretation.
4. `分红与资本运作`: dividends, buybacks, private placements, allotments, splits, and investor activities.
5. `股东结构与变动`: top holders, concentration, holder-count trend, reduction/increase plans, and pledge status.
6. `风险事件`: ST history, restricted-share unlocks, pledge pressure, reductions, audit issues, and forecast risks.
7. `资金面`: price/volume trend, 龙虎榜, block trades, margin financing, and northbound holdings.
8. `风险信号清单`: table with risk level, signal, trigger rule, evidence, date, and source method.
9. `数据附录`: method-by-method source table with query window, returned rows, latest date/report period, and caveats.

## Evidence And Output Requirements

- Include at least one source table in the appendix with columns similar to: `数据模块`, `来源接口`, `查询窗口`, `返回行数`, `最新日期/报告期`, `备注`.
- For each risk signal, include source method names and dates in the same row or immediately following sentence.
- If a section has no returned data, keep the heading and state the query method/window.
- Prefer concise tables over long prose when comparing periods or events.
- Keep the final tone analytical and non-promotional; avoid buy/sell language.

## Final QA Checklist

- Symbol is normalized and displayed consistently.
- Data date or report period appears in each major section.
- Derived metrics have formulas or field names.
- High/medium/low risks cite the triggering rule.
- Empty data is disclosed rather than hidden.
- Final disclaimer is present exactly as required by `SKILL.md`.
