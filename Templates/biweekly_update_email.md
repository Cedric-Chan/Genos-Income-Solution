# Genos Bi-Weekly Update Email Template

> 双周进展邮件。根据 Linear 当前状态生成，保持原文语气和篇幅。
> 上次真实邮件: 2026-01-26 ~ 2026-02-06

---

## 协作流程

1. 你告诉我"有较大进展，帮我发 update email"
2. 我 review Linear，逐个确认 NotStart/In Progress/Done 的最新描述和状态
3. 我批量更新 Linear，确保 board 和邮件一致
4. 我 draft 邮件，按下面模板填充，篇幅对齐原始邮件（约 3 个表格，每表 3-6 行）
5. 你确认后发送

---

## 邮件骨架

Subject: [Risk Genos Service] Income Document Service Bi-Weekly Update YYYYMMDD_YYYYMMDD

Dear ${user_name},

You are receiving this notification because you are inside this user group: Genos Risk Internal.
This is the latest update on the Genos Income Document Extraction Service.

---

## 1. Extraction Model

> 规则：包含所有 非 Done/Not Cancelled 的 Extraction 类 issue。只列有实质性进展的，Backlog 且无新信息的不列。

| Market | Document Type | Status | Key Detail | Next Step |
|--------|--------------|--------|------------|-----------|
| ${market} | ${doc_type} | ${human_status} | ${latest_progress_1line} | ${next_step_1line} |

## 2. Quality Check Model

> 规则同上，包含所有 QC 类非 Done issue。

| Market | Document Type | Status | Key Detail | Next Step |
|--------|--------------|--------|------------|-----------|
| ${market} | ${doc_type} | ${human_status} | ${latest_progress_1line} | ${next_step_1line} |

## 3. ReportCheck & Rule Automation

> 规则同上，包含所有 ReportCheck/Rule 类非 Done issue。

| Market | Initiative | Status | Key Detail | Next Step |
|--------|-----------|--------|------------|-----------|
| ${market} | ${initiative} | ${human_status} | ${latest_progress_1line} | ${next_step_1line} |

---

## 4. Key Highlights（可选，仅当有重大里程碑时）

> 上线 / 放量 / 数据拐点 / 阻塞解除 等值得单列的事件。

- ${bullet_point}

---

To help track income submissions and rule hits, we built [this dashboard](${dashboard_link}) so you can easily view key metrics and conduct case studies.

Below is the current Genos solution map for Credit Risk:
[Solution Map Image]

The detailed project tracker can be found [here](${tracker_link}).

If you have any questions, concerns, or additional requirements, please reach out to cedric.chencan@monee.com.

Thanks and Regards,

Risk Data Team

---

## 写作约束

- 篇幅: 总行数对齐原始邮件（约 30-40 行正文），每个表格 3-6 行，不追求穷举全部 issue
- 语气: 客观事实陈述，不用感叹号、不夸大、不含糊。"Live Testing" > "Live" > "UAT" > "In Progress" > "Planned"
- Key Detail 列：一句话，最多两句。包含时间节点和人名。
- Next Step 列：一句话，动词开头。

## Status 映射表（Linear → 邮件文案）

| Linear Status | 邮件文案 |
|---|---|
| Done | 不列 |
| In Progress | Live / UAT / In Progress（按实际阶段） |
| In Review | In Review |
| Todo | Not Started / In Progress（按是否有实际进展） |
| Backlog | Planned（仅列有具体时间节点的） |
| Canceled | 不列 |