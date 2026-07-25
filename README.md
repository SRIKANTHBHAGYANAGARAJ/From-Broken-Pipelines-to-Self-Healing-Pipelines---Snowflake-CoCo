# build-with-coco

A collection of reusable [Cortex Code](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code) skills and copy-paste prompts for automating data engineering workflows on Snowflake.

![Cortex Code Badge](images/cortex-code-badge.png)

---

## 🤖 **Cortex Code Agent SDK – Self-Healing Pipelines**

As part of the **Northstar - Getting Started with Cortex Code** workshop, I built autonomous pipeline triage and repair systems using Snowflake's Cortex Code Agent SDK.

### The Problem: 3 AM Firefighting

![Before Agent SDK - 3 AM Alert](images/01_before_3am_alert.png)

Every engineer's worst nightmare:
- **3:04 AM** – Alert fires. Airflow marks task FAILED. PagerDuty wakes you up.
- **3:11 AM** – Log archaeology begins. Open Airflow UI. Read raw logs.
- **3:19 AM** – Switch to Snowflake. Manually query INFORMATION_SCHEMA.
- **3:41 AM** – Manual fix. Create PR. Write incident note. Go back to sleep.

![Before Agent SDK - Manual Triage Loop](images/02_before_manual_triage.png)

**Result:** 40 minutes. 4 tools. 0 context. Same failure next week.

---

### The Solution: Cortex Code Agent SDK

![Cortex Code Agent SDK Code](images/03_agent_sdk_code.png)

**One `on_failure_callback`. One agent. All four tools – simultaneously.**

What the agent does autonomously:
1. **Detects Failure** – Airflow `on_failure_callback` triggers the agent
2. **Multi-Source Investigation** – Reads logs, queries TASK_HISTORY, checks INFORMATION_SCHEMA
3. **Root Cause Analysis** – Correlates schema drift + code + git history
4. **Auto-Fix** – Edits dbt model, runs dbt test, commits and opens PR
5. **Structured Alert** – Posts diagnosis + PR link to Slack

![After Agent SDK - Autonomous Flow](images/04_after_autonomous_flow.png)

---

### What You Wake Up To

![Agent Output - Slack Message](images/05_agent_slack_output.png)
🔍 INVESTIGATED
Queried TASK_HISTORY and QUERY_HISTORY for task stg_orders.
Error: KeyError: 'customer_id' in models/staging/stg_orders.sql:47

📊 SCHEMA CHECK
Column customer_id no longer exists in RAW.ORDERS. Dropped at 21:14 UTC.
Replaced by order_user_id.

🎯 ROOT CAUSE
Migration PR #847 (merged 21:09 UTC) renamed the column.
Staging model was not updated. 3 downstream models affected.

🔧 FIX APPLIED
Updated customer_id → order_user_id in all 3 models.
Ran dbt test – all tests pass.
Committed to branch fix/stg-orders-column-rename.

✅ ACTION REQUIRED
PR opened: #851 – ready for your review.
Pipeline can be retriggered after merge.
Estimated reruntime: ~4 minutes.



---

### Side by Side Comparison

![Side by Side Comparison](images/06_side_by_side_comparison.png)

| Metric | Before (Manual) | After (Agent SDK) |
|--------|-----------------|-------------------|
| **Failure notification** | Task name + exit code | Root cause + impacted models + fix |
| **Who investigates** | On-call engineer, manually | Agent, autonomously |
| **Tools used** | Sequentially (1 at a time) | All four simultaneously |
| **Time to diagnosis** | 25–40 minutes | **<3 minutes** |
| **Fix** | Engineer writes manually | Agent writes, tests, opens PR |
| **Engineer woken up at 3am** | Yes ❌ | No – reviews PR at 9am ✅ |
| **Integration effort** | Complex custom operators | One callback + 10 lines of code |

---

### Key Learnings

✅ **Self-Healing Pipelines** – Pipelines can remember, learn, and fix themselves
✅ **Autonomous Agents** – One agent can use all tools simultaneously
✅ **Context is Everything** – Agent correlates logs, metadata, and git history
✅ **Sleep Matters** – No more 3 AM firefighting
✅ **Simple Integration** – One `on_failure_callback` + 10 lines of code

---

## Skills

| Skill | Description | Trigger Phrases |
|---|---|---|
| [dbt-model-generator](skills/dbt-model-generator/SKILL.md) | Auto-generate dbt dimensional models (star schema) from raw Snowflake tables | "generate dbt models", "shift left", "dimensional model", "star schema from raw" |
| [developer-voice](skills/developer-voice/SKILL.md) | Research real developer sentiment from Reddit and community forums, then synthesize into talk tracks, research briefs, or competitive intel grounded in practitioner quotes | "developer voice", "community pulse", "reddit research", "talk track", "what are developers saying", "pain points" |
| [know-your-data](skills/know-your-data/SKILL.md) | Discover data you already have access to in a Snowflake account, understand what it contains, and map it to your roles | "know your data", "know my data", "find data", "data discovery", "what can I access", "explore account" |
| [poc-builder](skills/poc-builder/SKILL.md) | Go from zero to a working POC with any Snowflake guide, blog post, doc link, or topic name. Deep-dives the content, extracts a demo spec, finds matching data in your account, and builds a working POC interactively | "poc builder", "build this", "zero to poc", "try this", "teach me", "walk me through", "prototype this", "run this guide" |
| [aws-glue-iceberg-setup](skills/aws-glue-iceberg-setup/SKILL.md) | Set up AWS Glue as an Iceberg catalog for Snowflake — crawl S3 data, register tables, configure external volumes, and query via Iceberg | "glue iceberg", "aws glue setup", "iceberg from s3", "external catalog", "glue crawler" |
| [data-quality-suite](skills/data-quality-suite/SKILL.md) | Multi-framework DQ suite — set up quality checks, monitoring, and pipeline gates across Snowflake DMFs, dbt, Great Expectations, Soda Core, Snowpark, and PySpark. Fork `customer-config.md` to customize for your team | "data quality", "quality checks", "quality gates", "dbt tests", "soda checks", "great expectations", "dq suite" |

## Recipes

Copy-paste prompts in two categories — browse by role or by scenario. Full catalog in [`recipes/README.md`](recipes/README.md).

**By role** (`recipes/by-role/`): [data-engineer](recipes/by-role/data-engineer/prompts.md), [analytics-engineer](recipes/by-role/analytics-engineer/prompts.md), [data-analyst](recipes/by-role/data-analyst/prompts.md), [ai-ml-engineer](recipes/by-role/ai-ml-engineer/prompts.md), [app-developer](recipes/by-role/app-developer/prompts.md), [data-governance-lead](recipes/by-role/data-governance-lead/prompts.md), [snowflake-admin](recipes/by-role/snowflake-admin/prompts.md)

| Scenario | Prompts |
|---|---|
| **Connect sources** | [openflow-postgres-replication](recipes/connect-sources/openflow-postgres-replication.md), [iceberg-external-catalog](recipes/connect-sources/iceberg-external-catalog.md) |
| **Build pipelines** | [dbt-health-check-and-deploy](recipes/build-pipelines/dbt-health-check-and-deploy.md), [dynamic-table-pipeline](recipes/build-pipelines/dynamic-table-pipeline.md) |
| **Monitor quality** | [data-quality-monitoring](recipes/monitor-quality/data-quality-monitoring.md), [data-quality-suite-setup](recipes/monitor-quality/data-quality-suite-setup.md) |
| **Optimize costs** | [full-cost-governance-audit](recipes/optimize-costs/full-cost-governance-audit.md), [cost-optimization-sprint](recipes/optimize-costs/cost-optimization-sprint.md) |
| **Secure & govern** | [governance-hardening](recipes/secure-and-govern/governance-hardening.md), [network-security-lockdown](recipes/secure-and-govern/network-security-lockdown.md) |
| **Assess change impact** | [lineage-impact-analysis](recipes/assess-change-impact/lineage-impact-analysis.md), [migration-assessment](recipes/assess-change-impact/migration-assessment.md) |
| **Self-serve analytics** | [semantic-view-plus-agent](recipes/self-serve-analytics/semantic-view-plus-agent.md), [streamlit-sales-dashboard](recipes/self-serve-analytics/streamlit-sales-dashboard.md) |
| **AI enrichment** | [cortex-ai-ticket-enrichment](recipes/ai-enrichment/cortex-ai-ticket-enrichment.md), [ml-churn-prediction](recipes/ai-enrichment/ml-churn-prediction.md) |

## Repo Structure
build-with-coco/
├── images/ # All screenshots and assets
│ ├── cortex-code-badge.png
│ ├── 01_before_3am_alert.png
│ ├── 02_before_manual_triage.png
│ ├── 03_agent_sdk_code.png
│ ├── 04_after_autonomous_flow.png
│ ├── 05_agent_slack_output.png
│ └── 06_side_by_side_comparison.png
├── skills/
│ ├── dbt-model-generator/
│ │ ├── SKILL.md
│ │ ├── COMPASS.md # 25–35 line navigation guide
│ │ └── references/
│ │ └── workflow.md
│ ├── developer-voice/
│ │ ├── SKILL.md
│ │ ├── COMPASS.md
│ │ └── references/
│ │ └── html-styling.md
│ ├── know-your-data/
│ │ ├── SKILL.md
│ │ └── COMPASS.md
│ ├── aws-glue-iceberg-setup/
│ │ ├── SKILL.md
│ │ ├── COMPASS.md
│ │ └── references/
│ └── poc-builder/
│ ├── SKILL.md
│ ├── COMPASS.md
│ └── references/
│ ├── ACCOUNT_DISCOVERY.md
│ ├── BUILD_SUMMARY_TEMPLATE.md
│ ├── GUIDE_PARSING.md
│ ├── KNOWN_GOTCHAS.md
│ └── TEACHING_PATTERNS.md
├── recipes/
│ ├── by-role/ # prompts organized by job role
│ ├── connect-sources/
│ ├── build-pipelines/
│ ├── monitor-quality/
│ ├── optimize-costs/
│ ├── secure-and-govern/
│ ├── assess-change-impact/
│ ├── self-serve-analytics/
│ ├── ai-enrichment/
│ └── README.md
├── docs/
│ └── COMPASS_GUIDE.md # how to write COMPASS.md files
├── AGENTS.md # agent contribution guide
├── install.sh
└── hooks/ # session hooks (optional)
├── check-errors.py
├── session-start.sh
├── set-tab-title.sh
├── tab-title-helper.py
├── whats-new-helper.py
└── README.md


## What Are Cortex Code Skills?

Skills are markdown-based workflow definitions that teach Cortex Code how to perform complex, multi-step tasks. They provide structured guidance, tool references, decision logic, and user checkpoints.

## Installation

### Quick install (everything)

<---bash--->
./install.sh

Skills only or recipes only

./install.sh skills    # skills only
./install.sh recipes   # recipes only

Per-project install

./install.sh --project   # installs into .cortex/ in current directory

Manual

cp -r skills/dbt-model-generator ~/.snowflake/cortex/skills/dbt-model-generator

Usage

Once installed, invoke a skill in Cortex Code with:

$dbt-model-generator

$dbt-model-generator

Contributing
To add a new skill:

Create a directory under skills/<skill-name>/

Add a SKILL.md following the Cortex Code skill format

If SKILL.md exceeds 150 lines, add a COMPASS.md (see docs/COMPASS_GUIDE.md)

Update this README with the skill entry

Submit a PR

## 🎓 **Badge Earned**

![Northstar - Getting Started with Cortex Code](images/northstar-coco-foundations.png)

- **Badge:** Northstar - Snowflake CoCo Foundations - Getting Started with CoCo
- **Issued By:** Snowflake Education
- **Date:** July 2026

https://raw.githubusercontent.com/yourusername/build-with-coco/main/images/northstar-coco-foundations.png
- **Verification:** [Snowflake Developer Badges](https://developerbadges.snowflake.com)

https://developerbadges.snowflake.com/c71fffc1-0027-4080-a495-4f746b0a4b26#acc.JIXV54Hp

⭐ Star this repository if you found it helpful!


---

## 🖥️ **Git Commands to Update**

```bash
# Navigate to your repo
cd build-with-coco

# Create images folder
mkdir -p images

# Copy all your screenshots to images folder
# (Copy from your local machine to the repo)

# Add all files
git add images/
git add README.md

# Commit
git commit -m "docs: Added Cortex Code Agent SDK images and updated README"

# Push
git push origin main

📋 Files to Save in images/ Folder

Filename	Source
cortex-code-badge.png	Your Snowflake badge
01_before_3am_alert.png	Screenshot 2026-07-25 213506.png
02_before_manual_triage.png	Screenshot 2026-07-25 213516.png
03_agent_sdk_code.png	Screenshot 2026-07-25 213532.png
04_after_autonomous_flow.png	Screenshot 2026-07-25 213550.png
05_agent_slack_output.png	Screenshot 2026-07-25 213615.png
06_side_by_side_comparison.png	Screenshot 2026-07-25 213633.png OR 213656.png

