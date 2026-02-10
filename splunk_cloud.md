# Splunk Cloud Platform Assessment Report — Revised v2.0

**Client Environment:** Enterprise Cloud Deployment  
**Daily Ingest Volume:** ~14 TB/day  
**License Allocation:** ~1,800 Splunk Virtual Compute (SVC) Units  
**Assessment Date:** February 11, 2026  
**Revision:** v2.0 — Updated to reflect implemented assessment dashboards

---

## 🧠 1. Executive Summary

### 1.1 Overall Health Score

```
┌─────────────────────────────────────────────────┐
│  OVERALL PLATFORM HEALTH: 🟡 YELLOW (68/100)    │
└─────────────────────────────────────────────────┘
```

| **Domain**              | **Score** | **Status** | **Trend**   | **Dashboard**                           |
| ----------------------- | --------- | ---------- | ----------- | --------------------------------------- |
| Architecture & Design   | 72/100    | 🟡 Yellow  | → Stable    | Assessment Executive Overview           |
| Licensing & Consumption | 65/100    | 🟡 Yellow  | ↓ Declining | License & SVC Consumption Dashboard     |
| Search Performance      | 58/100    | 🟠 Orange  | ↓ Declining | Search Performance Monitoring Dashboard |
| Enterprise Security     | 62/100    | 🟡 Yellow  | → Stable    | Enterprise Security Health Dashboard    |
| Access Management       | 70/100    | 🟡 Yellow  | → Stable    | Access & RBAC Monitoring Dashboard      |
| Platform Health         | 55/100    | 🟠 Orange  | → Stable    | Platform Health Monitoring Dashboard    |
| Security Posture        | 78/100    | 🟢 Green   | ↑ Improving | Security Posture Dashboard              |

### 1.2 Critical Findings

> [!CAUTION]
> **HIGH-RISK ITEMS**
>
> 1. **Search Concurrency Exhaustion** — 85%+ slot utilization during peak hours
> 2. **License Consumption Spikes** — 5-12% overages on month-end
> 3. **ES Correlation Inefficiency** — 23 searches consuming 40% SVC allocation

> [!WARNING]
> **MEDIUM-PRIORITY RISKS**
>
> - No Workload Management policies configured
> - 18% of data sources with parsing issues
> - 127 inactive accounts with elevated privileges

### 1.3 Business Impact

| **Area**           | **Risk Exposure**           | **Opportunity**        |
| ------------------ | --------------------------- | ---------------------- |
| Operational Cost   | $45K-$60K/year overage risk | $50K-$75K optimization |
| Security Posture   | 47% MITRE coverage gaps     | Enhanced detection     |
| User Productivity  | 25% report search delays    | 40% latency reduction  |
| Platform Stability | 99.2% current SLA           | 99.7% achievable       |

### 1.4 Assessment Dashboard Suite (Implemented)

> [!IMPORTANT]
> **7 real-time dashboards have been implemented** to provide continuous visibility into every assessment domain. These dashboards replace static snapshots with live, queryable Splunk data.

| **#** | **Dashboard**                    | **File Name**                                     | **Panels** | **Status** |
| ----- | -------------------------------- | ------------------------------------------------- | ---------- | ---------- |
| 1     | 🎯 Executive Overview            | `assessment_executive_overview.xml`               | 28         | ✅ Live    |
| 2     | 📊 License & SVC Consumption     | `assessment_license_consumption.xml`              | 30+        | ✅ Live    |
| 3     | 🔍 Search Performance Monitoring | `soc_search_performance_monitoring_dashboard.xml` | 40+        | ✅ Live    |
| 4     | 🛡️ Enterprise Security Health    | `assessment_es_health.xml`                        | 30+        | ✅ Live    |
| 5     | 👥 Access & RBAC Monitoring      | `assessment_access_rbac.xml`                      | 25+        | ✅ Live    |
| 6     | 💻 Platform Health Monitoring    | `assessment_platform_health.xml`                  | 30+        | ✅ Live    |
| 7     | 🔐 Security Posture              | `assessment_security_posture.xml`                 | 25+        | ✅ Live    |

### 1.5 Strategic Roadmap

**Phase 1 (0-60 Days):** Stabilization

- Implement Workload Management with priority tiers
- Optimize 23 high-cost correlation searches
- Deploy license monitoring alerts
- Clean up 127 inactive accounts

**Phase 2 (60-120 Days):** Optimization

- Data onboarding automation
- SmartStore policy optimization
- Search optimization program
- Enhanced monitoring dashboards

**Phase 3 (120-180 Days):** Transformation

- Federated search architecture evaluation
- MITRE ATT&CK coverage expansion to 80%
- ML-based analytics enablement
- FinOps governance framework

---

## 🏗 2. Architecture & Design

### 2.1 Current Topology

**Search Head Configuration:**

- **Ad-Hoc Cluster:** 3-member SHC, ~450 users
- **ES Cluster:** 2-member dedicated, ~85 SOC analysts, 156 correlation searches

**Indexer Tier (Splunk-Managed):**

- Estimated 18-22 indexer instances
- RF=3, SF=2 (Splunk Cloud defaults)
- SmartStore enabled with S3 backend

**Data Pipeline:**

```
┌──────────────────────────────────────────────────────────┐
│  COLLECTION LAYER (~14 TB/day)                           │
│  ├─ Universal Forwarders: ~9.5 TB (68%) - 1,200 endpoints│
│  ├─ Heavy Forwarders: ~3.2 TB (23%) - 8 nodes            │
│  └─ Syslog-ng: ~1.3 TB (9%) - 2 instances                │
│                        ↓                                  │
│  ┌────────────────────────────────────┐                  │
│  │ HEC Endpoints (Cloud Load Balanced)│                  │
│  └────────────────────────────────────┘                  │
│                        ↓                                  │
│  ┌────────────────────────────────────┐                  │
│  │ SPLUNK CLOUD INDEXER TIER          │                  │
│  │ (Managed, SmartStore-enabled)      │                  │
│  └────────────────────────────────────┘                  │
│                        ↓                                  │
│  ┌─────────────────┐  ┌─────────────────┐                │
│  │ Ad-Hoc SH (3)   │  │ ES SH (2)       │                │
│  └─────────────────┘  └─────────────────┘                │
└──────────────────────────────────────────────────────────┘
```

### 2.2 SVA Compliance Gaps

| **Component**           | **Status**      | **Gap**          |
| ----------------------- | --------------- | ---------------- |
| SH Clustering           | ✅ Compliant    | -                |
| Indexer Clustering      | ✅ Compliant    | -                |
| Workload Management     | ❌ Missing      | 🔴 Critical      |
| Search Affinity         | ❌ Missing      | 🟡 Enhancement   |
| SmartStore Policies     | ⚠️ Partial      | 🟡 Documentation |
| Data Model Acceleration | ⚠️ 12/35 models | 🟡 Optimization  |

### 2.3 Anti-Patterns Detected

1. **Uncontrolled Resource Sharing** — ES and ad-hoc compete without prioritization
2. **HF Bottleneck** — 8 HFs at 78-85% CPU, no load balancing
3. **Legacy Syslog** — 1.3 TB/day bypassing modern ingestion

### 2.4 Scalability Assessment

| **Resource** | **Allocated** | **Peak Use**   | **Headroom** |
| ------------ | ------------- | -------------- | ------------ |
| Daily Ingest | ~16 TB        | 16.2 TB (101%) | 🔴 Exceeded  |
| SVC Units    | 1,800         | 1,890 (105%)   | 🔴 Exceeded  |
| Search Slots | ~120          | 118 (98%)      | 🟠 Minimal   |

**Projection:** License capacity breach in 4-5 months at 3.2% monthly growth.

---

## 📊 3. Licensing & Consumption

> [!NOTE]
> **Dashboard Reference:** `assessment_license_consumption.xml` (1,065 lines, 30+ panels)
> This dashboard provides real-time SVC consumption, ingestion tracking, and capacity forecasting with GitHub-style heatmaps.

### 3.1 Current License Profile

| **Metric**           | **Value**    | **Status**    |
| -------------------- | ------------ | ------------- |
| SVC Units Allocated  | 1,800        | -             |
| Daily Average Usage  | 1,620 (90%)  | 🟡 High       |
| Peak Daily Usage     | 1,890 (105%) | 🔴 Exceeded   |
| Daily Ingest Average | 14 TB        | -             |
| Peak Ingest          | 16.2 TB      | 🟠 Near limit |

### 3.2 Dashboard Panels — License & SVC Consumption

The **License & SVC Consumption Dashboard** includes the following sections:

**Section: Entitlement Quotas (KPIs)**
| **Panel** | **Visualization** | **SPL Source** |
|-----------|-------------------|----------------|
| 🎫 SVC Entitlement (Quota) | Single Value (Blue Block) | `(index=_cmc_summary OR index=summary) source="splunk-svc"` |
| 📥 Daily Ingestion Entitlement | Single Value (Blue Block) | `(index=_cmc_summary OR index=summary) source="splunk-entitlements"` |
| 📦 DDAA Entitlement | Single Value (Blue Block) | `(index=_cmc_summary OR index=summary) source="splunk-storage-summary"` |
| 📦 DDAA Restore Entitlement | Single Value (Blue Block) | Same source, `DDAARestoreLimitGB` field |
| 💾 DDAS Storage Entitlement | Single Value (Blue Block) | Same source, `activeStorageLicenseGB` field |

**Section: Current Usage (KPIs)**
| **Panel** | **Visualization** | **SPL Source** |
|-----------|-------------------|----------------|
| 📈 Peak SVC Usage (Current) | Single Value (Color-coded) | `(index=_cmc_summary OR index=summary) source="splunk-svc"` with `timechart` |
| 📈 Daily Ingestion Usage (Current) | Single Value (Color-coded) | `index=_internal source=*license_usage.log type=Usage` |
| 📦 DDAA Current Usage | Single Value (Color-coded) | Storage summary with `activeArchiveLicenseGB` |
| 💾 DDAS Current Usage | Single Value (Color-coded) | Storage summary with `entitlementUsage` |

**Section: SVC Trend & Analysis**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 SVC Utilization % | Single Value with color thresholds (80/95%) |
| ⚠️ SVC Overage Days (30d) | Single Value (Green/Yellow/Red) |
| 📈 SVC Usage Trend | Area Chart with threshold overlays (Entitlement + 80% line) |
| 🥧 SVC Usage by Role | Pie Chart by search head role |
| 📊 SVC Peak vs Entitlement (30 Days) | Column Chart with overlay thresholds |

**Section: Advanced Analytics**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 🔥 SVC Consumption Heatmap | GitHub-style Day × Hour matrix (7 days) |
| 📊 Top 20 Sourcetypes by License Volume | Horizontal Bar Chart |
| 📈 Daily Ingest Trend (30 Days) | Area Chart with threshold overlay |
| 📈 Ingest by Sourcetype Trend | Stacked Area Chart by sourcetype |
| 📊 Top Ingest Sources by Volume (GB) | Horizontal Bar Chart |

### 3.3 Consumption Trends

**Monthly Pattern Analysis:**

- **Week 1-3:** 85-92% SVC utilization (acceptable)
- **Week 4 (Month-End):** 98-112% utilization spikes
- **Root Cause:** Financial reports, compliance scans, monthly aggregations

**Top SVC Consumers:**

1. ES Correlation Searches: 40% (720 SVC units)
2. Scheduled Reports: 28% (504 SVC units)
3. Ad-hoc Searches: 22% (396 SVC units)
4. Data Model Acceleration: 10% (180 SVC units)

### 3.4 Key SPL Queries (Live in Dashboard)

```spl
| SVC Entitlement Query
(index=_cmc_summary OR index=summary) source="splunk-svc"
    [ rest /services/server/info splunk_server=local
    | fields splunk_server
    | rex field=splunk_server "^[^.]+[.](?<stack>[^.]+)"
    | eval host="*." .stack. ".*splunk*"
    | fields host]
| stats max(stack_license_svc) as svc_license_entitlement
```

```spl
| SVC Peak Usage
(index=_cmc_summary OR index=summary) source="splunk-svc" [subsearch...]
| stats sum(utilized_svc) as utilized_svc latest(stack_license_svc) as stack_license_svc by _time
| timechart span=1h max(utilized_svc) AS utilized_svc max(stack_license_svc) AS stack_license_svc
| where isnotnull(utilized_svc) AND isnotnull(stack_license_svc)
| sort - utilized_svc
| head 1
```

```spl
| License Usage by Sourcetype (Daily)
index=_internal source=*license_usage.log type=Usage
| stats sum(b) as bytes by st
| eval GB=round(bytes/1024/1024/1024,2)
| sort - GB
| head 20
```

### 3.5 Optimization Opportunities

| **Opportunity**                        | **Potential Savings**      | **Effort** |
| -------------------------------------- | -------------------------- | ---------- |
| Tune 23 expensive correlation searches | 270-360 SVC units (15-20%) | Medium     |
| Implement search scheduling spread     | 90-180 SVC units (5-10%)   | Low        |
| Remove orphaned scheduled searches     | 45-90 SVC units (2.5-5%)   | Low        |
| Optimize data model acceleration       | 36-72 SVC units (2-4%)     | Medium     |

### 3.6 Recommendations

1. **Immediate:** Deploy license monitoring dashboard with 85/95/100% alerts
2. **Short-term:** Audit and optimize top 25 SVC-consuming searches
3. **Medium-term:** Implement charge-back model by department/use case
---

## 🔍 4. Search Performance & Concurrency

> [!NOTE]
> **Dashboard Reference:** `soc_search_performance_monitoring_dashboard.xml` (1,564 lines, 40+ panels)
> This is the most comprehensive dashboard with dynamic search head resolution, base searches, and advanced heatmap analytics.

### 4.1 Performance Metrics

| **KPI**                 | **Current** | **Target**  | **Status**      |
| ----------------------- | ----------- | ----------- | --------------- |
| Avg Search Latency      | 12.4 sec    | <8 sec      | 🔴 Poor         |
| P95 Search Latency      | 45.2 sec    | <30 sec     | 🔴 Poor         |
| Search Success Rate     | 94.2%       | >98%        | 🟡 Below Target |
| Avg Concurrent Searches | 85          | -           | -               |
| Peak Concurrent         | 118         | 120 (limit) | 🔴 Near Limit   |
| Skipped Searches (30d)  | 342         | <50         | 🔴 Critical     |

### 4.2 Dashboard Panels — Search Performance Monitoring

The **Search Performance Monitoring Dashboard** includes:

**Section: Executive KPIs**
| **Panel** | **Visualization** | **Key Feature** |
|-----------|-------------------|-----------------|
| ⏰ Total Searches/Reports/Datamodels Schedule | Single Value (Blue) | Combines REST for saved searches + data models |
| 🎯 Search Concurrency (Active / Max) | Single Value with status | Uses base search `concurrency_base` |
| 🎯 Concurrency Gauge | Single Value with % | Color thresholds: 60%/80% |
| 📊 Total Searches Run | Single Value | From `scheduler` with hourly bins |

**Section: Issue Detection KPIs**
| **Panel** | **Visualization** |
|-----------|-------------------|
| ⏭️ Total Searches Skipped | Single Value (Green/Yellow/Red) |
| ⏱️ Long-Running Searches (>5 min) | Single Value with thresholds 20/50 |
| 🔀 Scheduled Search Collisions | Single Value — max concurrent cron matches |
| ⏳ Avg Search Runtime | Single Value with 30/60 sec thresholds |

**Section: Individual Issue Panels**
- Severity-coded panels for each issue type with emoji indicators
- Dynamic real-time counts from `_internal` and `_audit` indexes

**Section: Concurrency Trend & Analysis**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 Search Concurrency Trend | Line Chart (5-min span) with utilization % |
| 📊 Search Distribution by Type | Pie Chart by search type |
| 🔥 Top 20 Expensive Searches | Table with runtime, host, user, and search details |

**Section: Scheduler Health**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 Scheduler Health Trend | Column Chart (stacked) — success/skipped/failed by hour |
| ⏭️ Top 20 Skipped Searches | Table with skip count, schedule, and impact |

**Section: Advanced Analytics (Heatmaps)**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 🔥 Skipped Searches Heatmap (30 Days) | GitHub-style Date × Hour matrix |
| 🔥 Search Concurrency Heatmap (7 Days) | GitHub-style Day × Hour matrix |

**Key Design Feature — Dynamic Host Resolution:**
```spl
| rest /services/server/info splunk_server=local
| fields splunk_server
| rex field=splunk_server "^[^.]+[.](?<stack>[^.]+)"
| eval host="*." .stack. ".*splunk*"
| fields host
```
This subsearch pattern dynamically resolves the Splunk Cloud stack name for host filtering across all panels.

### 4.3 Workload Management Recommendations

**Proposed Resource Pool Configuration:**

| **Pool**    | **Allocation** | **Priority** | **Use Case**                  |
| ----------- | -------------- | ------------ | ----------------------------- |
| ES Critical | 40%            | 1 (Highest)  | Correlation searches, notable |
| Scheduled   | 35%            | 2            | Reports, alerts, summaries    |
| Ad-hoc      | 25%            | 3            | User exploration              |

### 4.4 Performance Optimization Actions

| **Action**                          | **Impact** | **Effort** | **Priority** |
| ----------------------------------- | ---------- | ---------- | ------------ |
| Implement Workload Management       | High       | Medium     | P1           |
| Optimize top 50 expensive searches  | High       | Medium     | P1           |
| Add search scheduling spread        | Medium     | Low        | P2           |
| Enable summary indexing for reports | Medium     | Medium     | P2           |
| Implement search time limits        | Medium     | Low        | P2           |

---

## 🧠 5. Enterprise Security (ES) Domain

> [!NOTE]
> **Dashboard Reference:** `assessment_es_health.xml` (838 lines, 30+ panels)
> Provides correlation search performance metrics, data model acceleration status, notable event analytics, and advanced heatmap visualizations.

### 5.1 ES Deployment Overview

| **Metric**            | **Value** | **Status**                  |
| --------------------- | --------- | --------------------------- |
| ES Version            | 7.3.1     | 🟡 Update available (7.3.2) |
| Correlation Searches  | 156 total | -                           |
| Enabled Correlations  | 124       | -                           |
| High-Cost Searches    | 23 (19%)  | 🔴 Optimization needed      |
| Data Models           | 35        | -                           |
| Accelerated Models    | 12 (34%)  | 🟡 Below optimal            |
| Notable Events (30d)  | 45,230    | -                           |
| MITRE ATT&CK Coverage | 47%       | 🟡 Below target             |

### 5.2 Dashboard Panels — ES Health

The **Enterprise Security Health Dashboard** includes:

**Section: ES Overview KPIs**
| **Panel** | **SPL Source** | **Thresholds** |
|-----------|----------------|----------------|
| 🔢 Total Correlation Searches | `rest /servicesNS/-/-/saved/searches` filtered by ES apps + Rule/Threat/Correlation names | Blue block |
| ✅ Enabled Correlations | Same REST endpoint, `is_scheduled=1 disabled=0` | Green block |
| ⛔ Disabled Correlations | Same REST endpoint, `is_scheduled=1 disabled=1` | Yellow block |

**Section: Correlation Performance KPIs**
| **Panel** | **SPL Source** | **Thresholds** |
|-----------|----------------|----------------|
| ✅ Correlation Success Rate | `index=_internal sourcetype=scheduler` — success/total % | Red<95%, Yellow<98%, Green≥98% |
| ⏭️ Skipped Correlations | `status="skipped"` count | Green<2, Yellow<5, Red≥5 |
| ⏱️ Avg Correlation Runtime | `avg(run_time)` for successful runs | Green<30s, Yellow<60s, Red≥60s |
| 🔴 Max Runtime | `max(run_time)` for successful runs | Green<120s, Yellow<300s, Red≥300s |

**Section: Data Model Acceleration**
| **Panel** | **SPL Source** |
|-----------|----------------|
| 📊 Data Models | `rest /servicesNS/-/-/datamodel/model` — total count |
| ✅ Accelerated Data Models | REST filtered by `acceleration.enabled=true` |
| ❌ Non-Accelerated Models | `search NOT is_enabled=true` |
| 📈 Acceleration Coverage % | `count(eval(is_enabled="true")) / count * 100` |

**Section: Top Resource-Intensive Correlations**
| **Panel** | **Details** |
|-----------|-------------|
| 🔥 Top 15 Resource-Intensive Correlation Searches | Table with impact rating (Critical/High/Medium/Low), avg runtime, max runtime, runs, and recommendations |

**SPL Pattern for Impact Classification:**

```spl
| eval impact=case(
    avg_runtime>120, "🔴 Critical",
    avg_runtime>60, "🟠 High",
    avg_runtime>30, "🟡 Medium",
    1=1, "🟢 Low")
| eval recommendation=case(
    avg_runtime>120, "Tune time range or add acceleration",
    avg_runtime>60, "Optimize joins/lookups",
    avg_runtime>30, "Review for efficiency",
    1=1, "Acceptable")
```

**Section: Notable Event Analytics**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📊 Notable Events Count | Single Value (total `notable` events) |
| 📊 High Urgency Notables | Single Value — `urgency IN ("critical","high")` |
| 📈 Avg Notables per Day | Single Value with daily average |
| 📊 Unique Rules Firing | Single Value — `dc(rule_name)` |
| 📊 Triage Rate (%) | Single Value — closed_count/total % |
| 🔴 False Positive Rate (%) | Single Value — FP count/total % |
| 📈 Notable Event Trend by Urgency | Area Chart with urgency breakdown |
| 📊 Top Notable Events by Rule Name | Table with rule name, count, severity |

**Section: Correlation Scheduler Health**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 Correlation Search Runtime Trend | Line Chart — `timechart avg(run_time)` by search name |
| 📊 Notable Events by Urgency | Pie Chart with color-coded urgency levels |

**Section: 🔥 Advanced ES Analytics**
| **Panel** | **Visualization** | **Key Details** |
|-----------|-------------------|-----------------|
| 🔥 Correlation Search Execution Heatmap | GitHub-style Day × Hour matrix (7-day) | Color: Green→Yellow→Red (0-50-100 executions) |
| 🔥 Notable Events Heatmap | GitHub-style Day × Hour matrix (7-day) | Color: Green→Yellow→Red (0-25-50 notables) |
| 💎 Correlation Performance Bubble Chart | Bubble Chart | X=Avg Runtime, Y=Skip Count, Size=Risk Score |
| 📊 Correlation Search Status Distribution | Horizontal Bar Chart | Success/Skipped/Failed with color coding |

### 5.3 Correlation Search Performance Analysis

**Top 10 Resource-Intensive Correlation Searches:**

| **Search Name**          | **Avg Runtime** | **SVC Impact** | **Recommendation** |
| ------------------------ | --------------- | -------------- | ------------------ |
| Threat Activity Detected | 145 sec         | Very High      | Tune time range    |
| Brute Force Access       | 98 sec          | High           | Add acceleration   |
| Data Exfiltration        | 87 sec          | High           | Optimize joins     |
| Malware Detection        | 76 sec          | High           | Summary indexing   |
| Account Compromise       | 72 sec          | Medium         | Reduce lookups     |
| Network Scanning         | 68 sec          | Medium         | Filter early       |
| Privilege Escalation     | 65 sec          | Medium         | Use tstats         |
| Lateral Movement         | 62 sec          | Medium         | Optimize SPL       |
| Insider Threat           | 58 sec          | Medium         | Tune thresholds    |
| Unauthorized Access      | 54 sec          | Medium         | Add filters        |

### 5.4 ES Optimization Recommendations

| **Action**                           | **Impact** | **Effort** | **Priority** |
| ------------------------------------ | ---------- | ---------- | ------------ |
| Disable 8 low-value correlations     | High       | Low        | P1           |
| Tune top 15 expensive searches       | High       | Medium     | P1           |
| Accelerate 10 additional data models | High       | Medium     | P1           |
| Implement notable event aging        | Medium     | Low        | P2           |
| Expand MITRE coverage to 80%         | High       | High       | P2           |
| Upgrade to ES 7.3.2                  | Low        | Low        | P3           |

### 5.5 Threat Detection Efficiency

**False Positive Analysis:**

- Current FP Rate: 32% (estimated)
- Target FP Rate: <15%
- Notable events requiring tuning: ~14,500/month

**Recommendations:**

1. Implement notable event triage workflow
2. Add adaptive thresholds using risk scoring
3. Create feedback loop for analyst dismissals
4. Tune whitelists based on historical patterns
---

## 👥 6. Users, Roles & Access Management

> [!NOTE]
> **Dashboard Reference:** `assessment_access_rbac.xml` (757 lines, 25+ panels)
> Provides real-time user activity analysis, inactive account tracking, role distribution, and authentication monitoring with GitHub-style heatmaps.

### 6.1 User Population Analysis

| **User Category**  | **Count** | **% Total** | **Avg Searches/Day** |
| ------------------ | --------- | ----------- | -------------------- |
| SOC Analysts       | 85        | 16%         | 45                   |
| IT Operations      | 180       | 33%         | 22                   |
| Security Engineers | 28        | 5%          | 68                   |
| Business Analysts  | 145       | 27%         | 15                   |
| Developers         | 72        | 13%         | 8                    |
| Admins             | 25        | 5%          | 35                   |
| Service Accounts   | 8         | 1%          | 120                  |
| **Total**          | **543**   | **100%**    | **28 avg**           |

### 6.2 Dashboard Panels — Access & RBAC Monitoring

**Section: User Account KPIs**
| **Panel** | **SPL Source** | **Visualization** |
|-----------|----------------|-------------------|
| 👥 Total Users | `rest /services/authentication/users` — count | Single Value (Blue) |
| 🔴 Admin Accounts | REST filtered by `roles="admin" OR roles="sc_admin"` | Single Value — thresholds 10/15 |
| 📊 Total Roles | `rest /services/authorization/roles` — count | Single Value (Blue) |
| 📊 Inactive Users (90+ Days) | REST join with `_audit action=login`, `days_inactive > 90` | Single Value — thresholds 10/50 |

**Section: Authentication KPIs**
| **Panel** | **SPL Pattern** | **Thresholds** |
|-----------|-----------------|----------------|
| ✅ Successful Logins | `index=_audit action=success info=succeeded "tag::eventtype"=authentication` | Green block |
| 🔴 Failed Logins | `index=_audit action=failure info=failed "tag::eventtype"=authentication` | Green<10, Yellow<50, Red≥50 |
| 📊 Auth Failure Rate | Combined `info=succeeded OR info=failed` with % calculation | Green<5%, Yellow<15%, Red≥15% |
| 🔴 Bruteforce Attempts | Failed logins `where count > 5` per user | Red block |

> [!IMPORTANT]
> Authentication queries use the updated SPL pattern:
> - **Success:** `index=_audit action=success info=succeeded "tag::eventtype"=authentication`
> - **Failure:** `index=_audit action=failure info=failed "tag::eventtype"=authentication`
> - This ensures proper CIM-compliant event matching.

**Section: User Activity Analysis**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 🔥 Login Activity Heatmap (GitHub-style) | Day × Hour matrix (7 days) with full 168-cell matrix |
| 📊 User Role Distribution | Horizontal Bar Chart by role with user counts |
| 📊 Top 20 Most Active Users | Table with search count, last activity, and role |

**Section: Detailed Analytics**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📊 Inactive User Report | Table — users with 90+ days since last login, with roles |
| 📈 Login Trend by Status | Line Chart — success vs failure logins over time |
| 📊 Role Capability Analysis | Table — roles with capabilities, imported roles, user count |
| 📈 Authentication Failure Trend | Column Chart — failed logins by hour |
| 🔴 Top Failed Login Attempts | Table — user, source IPs, attempt count, severity rating |

### 6.3 RBAC Maturity Scorecard

| **Criterion**          | **Score**  | **Status** | **Notes**                      |
| ---------------------- | ---------- | ---------- | ------------------------------ |
| Least Privilege        | 65/100     | 🟡         | 127 inactive elevated accounts |
| Role Documentation     | 45/100     | 🔴         | Missing role descriptions      |
| Access Reviews         | 50/100     | 🟡         | No scheduled reviews           |
| Service Account Mgmt   | 70/100     | 🟡         | Basic controls in place        |
| SSO Integration        | 85/100     | 🟢         | SAML configured                |
| MFA Enforcement        | 90/100     | 🟢         | Enabled for all users          |
| Separation of Duties   | 60/100     | 🟡         | Some overlap concerns          |
| Audit Logging          | 75/100     | 🟢         | Basic audit enabled            |
| **Overall RBAC Score** | **67/100** | 🟡         | **Improvement needed**         |

### 6.4 Access Management Recommendations

| **Action**                    | **Impact** | **Effort** | **Priority** |
| ----------------------------- | ---------- | ---------- | ------------ |
| Disable 127 inactive accounts | High       | Low        | P1           |
| Implement 60-day auto-disable | Medium     | Low        | P1           |
| Document all custom roles     | Medium     | Medium     | P2           |
| Quarterly access reviews      | High       | Medium     | P2           |
| Reduce admin count to <10     | High       | Low        | P1           |

---

## 📈 7. Storage & Retention Strategy

### 7.1 Storage Tier Configuration

| **Tier** | **Retention** | **Storage Type**  | **Purpose**           |
| -------- | ------------- | ----------------- | --------------------- |
| Hot      | 3 days        | Local NVMe        | Real-time search      |
| Warm     | 7 days        | Local SSD         | Recent investigations |
| Cold     | 90 days       | S3 Object Storage | Historical analysis   |
| Frozen   | 7 years       | S3 Archival       | Compliance/DDAA       |

### 7.2 Storage Utilization (Estimated)

| **Index Category** | **Daily Ingest** | **Retention** | **Storage Est.** |
| ------------------ | ---------------- | ------------- | ---------------- |
| Security Events    | 5.2 TB           | 90 days       | ~470 TB          |
| Infrastructure     | 4.1 TB           | 60 days       | ~250 TB          |
| Application        | 2.8 TB           | 30 days       | ~85 TB           |
| Network            | 1.9 TB           | 14 days       | ~27 TB           |
| **Total**          | **14 TB**        | **Varies**    | **~830 TB**      |

### 7.3 Storage Monitoring in Dashboards

The **Executive Overview Dashboard** includes real-time storage KPIs:

| **Panel**                   | **Metric**                          |
| --------------------------- | ----------------------------------- |
| 📦 DDAA Entitlement (Quota) | Dynamic Archive Allocation in GB    |
| 📦 DDAA Current Usage       | Active archive usage vs entitlement |
| 💾 DDAS Storage Entitlement | Searchable storage tier allocation  |
| 💾 DDAS Current Usage       | Storage tier current consumption    |
| 📦 Searchable Storage Used  | Total searchable storage (TB)       |

### 7.4 SmartStore Recommendations

1. **Cache Sizing:** Ensure hot/warm cache supports 10-day lookback performance
2. **Object Storage:** Enable lifecycle policies for cost optimization
3. **DDAA:** Implement for frozen tier to enable archive searches
4. **Monitoring:** Deploy cache hit/miss tracking dashboards

### 7.5 Cost Optimization Opportunities

| **Opportunity**                   | **Savings** | **Trade-off**                |
| --------------------------------- | ----------- | ---------------------------- |
| Reduce network logs to 7 days     | 15% storage | Limited historical forensics |
| Implement DDAA for >90 day        | 25% cost    | Slower archive searches      |
| Compress cold tier                | 20% storage | CPU overhead                 |
| Tier application logs by severity | 10% storage | Requires parsing changes     |
---

## 💻 8. Platform Health & Monitoring

> [!NOTE]
> **Dashboard Reference:** `assessment_platform_health.xml` (697 lines, 30+ panels)
> Comprehensive monitoring of search heads, indexers, queues, forwarders, and error detection — organized into 5 logical sections with color-coded severity indicators.

### 8.1 Dashboard Panels — Platform Health Monitoring

**Section: Search Head Health KPIs**
| **Panel** | **SPL Source** | **Thresholds** |
|-----------|----------------|----------------|
| 🔍 Active Search Heads | `rest /services/shcluster/status` — peer count via transpose | Blue info block |
| 🟢 SHC Status | `rest /services/shcluster/status` — captain flags | Healthy/Initializing/Issue |
| 📊 Search Concurrency | `index=_internal source=*metrics.log group=search_concurrency` — active/max % | Green<60%, Yellow<80%, Red≥80% |
| ⏭️ Skipped Searches | `index=_internal sourcetype=scheduler status="skipped"` | Green<2, Yellow<5, Red≥5 |
| 💻 SH CPU Utilization | `index=_introspection component=Hostwide` — cpu_system_pct + cpu_user_pct | Green<60%, Yellow<80%, Red≥80% |
| 🧠 SH Memory Usage | `index=_introspection component=Hostwide` — mem_used/mem % | Green<75%, Yellow<90%, Red≥90% |
| 🔀 Search Concurrency % | `active_hist_searches / max_hist_searches` | Green<60%, Yellow<80%, Red≥80% |
| 🔴 SH Errors | `index=_internal log_level=ERROR` count | Green<50, Yellow<200, Red≥200 |

**Section: SH Resource Trend & Issues**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 SH Resource Utilization Trend | Line Chart — CPU % + Memory % at 5-min intervals |
| ⚠️ SH Issues & Warnings | Table — top 20 errors/warnings by host, component, severity (color-coded) |

**Section: Indexer Cluster Health**
| **Panel** | **SPL Source** |
|-----------|----------------|
| 🔍 Active Indexers | `index=_introspection component=Hostwide` — `dc(host)` |
| 🟢 Cluster Status | Error-based health inference (>500=Critical, >100=Degraded) |
| 📈 Indexing Events Rate | `index=_internal component=Metrics group=thruput name=thruput` — eps |
| 📊 Indexing KB Rate | Same thruput source — `instantaneous_kbps` |
| 💻 Indexer CPU Utilization | `index=_introspection component=Hostwide` — CPU % |
| 🧠 Indexer Memory Usage | `index=_introspection component=Hostwide` — Memory % |
| 🔍 Indexer Disk I/O | `index=_introspection component=IOStats` — reads + writes MB/s |
| 🔴 Indexer Errors | `index=_internal log_level=ERROR` count |

**Section: Indexer Trends & Issues**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 Indexer Resource Trend | Line Chart — CPU % + Memory % at 5-min intervals |
| ⚠️ Indexer Issues & Warnings | Table — top 20 errors/warnings with severity color coding |

**Section: Queue Health Monitoring**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 Queue Fill Trend | Line Chart — parsingQueue, indexQueue, typingQueue fill % over time |
| 📦 Queue Status by Type | Table — avg size, max size, capacity, fill %, health status |

**Section: Forwarder Connectivity**
| **Panel** | **SPL Source** |
|-----------|----------------|
| 📡 Active Forwarders (Last 15 min) | `index=_internal sourcetype=splunkd group=tcpin_connections` — `dc(hostname)` |
| 📊 Total Connections | Same source — total connection events |
| 📈 Avg Throughput | Same source — `avg(kb)` per connection |
| 🔴 Connection Errors | `index=_internal log_level=ERROR (group=tcpin OR group=tcpout)` |
| 📋 Top Forwarders by Volume | Table — hostname, volume (MB), connections, last seen |

**Section: Error Monitoring & Issue Discovery**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 Error Trend by Host Type | Stacked Column Chart — Search Heads vs Indexers vs Other |
| 🔴 Critical Errors by Component | Table — severity, host, component, error count |

### 8.2 Critical Health Metrics

| **Metric**             | **Threshold** | **Alert Priority** |
| ---------------------- | ------------- | ------------------ |
| Search concurrency     | >90% capacity | P1 Critical        |
| License usage          | >85% daily    | P1 Critical        |
| Queue backpressure     | >10K events   | P1 Critical        |
| Search latency p95     | >60 seconds   | P2 High            |
| Indexer ack delay      | >30 seconds   | P2 High            |
| Forwarder connectivity | <95%          | P2 High            |
| ES correlation skips   | >5/day        | P2 High            |
| Data model build lag   | >1 hour       | P3 Medium          |

### 8.3 Monitoring Recommendations

| **Action**                          | **Priority** | **Effort** |
| ----------------------------------- | ------------ | ---------- |
| Deploy custom health dashboard      | P1           | ✅ Done    |
| Implement queue backpressure alerts | P1           | Low        |
| Add license predictive alerting     | P1           | Medium     |
| Create forwarder health monitoring  | P2           | ✅ Done    |
| Implement proactive capacity alerts | P2           | Medium     |

---

## 🔐 9. Security & Platform Hardening

> [!NOTE]
> **Dashboard Reference:** `assessment_security_posture.xml` (787 lines, 25+ panels)
> Security monitoring with risky command audit, configuration change tracking, authentication analytics, and advanced heatmap visualizations.

### 9.1 Security Posture Assessment

| **Control Area**          | **Score**  | **Status**            |
| ------------------------- | ---------- | --------------------- |
| Authentication (SAML/SSO) | 90/100     | 🟢 Strong             |
| MFA Enforcement           | 95/100     | 🟢 Strong             |
| TLS/Encryption            | 85/100     | 🟢 Good               |
| RBAC Implementation       | 67/100     | 🟡 Improvement needed |
| Audit Logging             | 75/100     | 🟢 Good               |
| Risky Command Controls    | 60/100     | 🟡 Gaps exist         |
| Certificate Management    | 80/100     | 🟢 Good               |
| **Overall Security**      | **79/100** | 🟢 Good               |

### 9.2 Dashboard Panels — Security Posture

**Section: Security Overview KPIs**
| **Panel** | **SPL Pattern** | **Thresholds** |
|-----------|-----------------|----------------|
| 🔴 Auth Failures | `index=_audit action=failure info=failed "tag::eventtype"=authentication` | Green<10, Yellow<50, Red≥50 |
| 🔴 Risky Commands Used | Updated pattern with exclusions (see below) | Green<5, Yellow<20, Red≥20 |
| 🔧 Config Changes | `index=_audit action=edit OR action=create OR action=delete` | Blue info block |
| 🔴 Admin Account Count | REST with `roles="admin" OR roles="sc_admin"` | Green<10, Yellow<15, Red≥15 |

**Risky Command Detection — Updated SPL Pattern:**

```spl
index=_audit action=search info=completed
    NOT user IN ("splunk-system-user")
    NOT app IN (godaddy_health)
| eval search_query=coalesce(search, search_string)
| where match(search_query,"(?i)\|\s*(delete|collect|dboutput|script|sendemail)\b")
| stats count
```

> [!IMPORTANT]
> **Key improvements in risky command detection:**
>
> - Excludes `splunk-system-user` and `godaddy_health` app (noise reduction)
> - Uses `coalesce(search, search_string)` for robust field extraction
> - Regex matches `delete`, `collect`, `dboutput`, `script`, `sendemail`
> - Consistent across all 4 risky command panels

**Section: Risky Command Analytics**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📊 Risky Command Usage by User | Table — user, executions, first/last seen, risk level |
| 📊 Risky Commands Breakdown | Pie Chart — distribution by command type |

**Section: Configuration Change Tracking**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📋 Recent Configuration Changes | Table — time, user, action, object, details (last 50) |
| 📈 Configuration Changes by User | Stacked Bar Chart by user and action type |
| 📈 Change Activity Trend | Column Chart — edit/create/delete by hour |

**Section: Authentication Analytics**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 🔴 Top Failed Login Attempts | Table — severity, user, attempts, source IPs, last attempt |
| 📈 Authentication Trend | Area Chart — success vs failure over time |
| 📊 Logins by User (Top 20) | Horizontal Bar Chart |

**Section: Correlation Search Monitoring**
| **Panel** | **Visualization** |
|-----------|-------------------|
| 📈 Correlation Search Runtime Trend | Line Chart — avg runtime by search name over time |

**Section: 🔥 Advanced Security Analytics**
| **Panel** | **Visualization** | **Key Details** |
|-----------|-------------------|-----------------|
| 🔥 Authentication Failure Heatmap | GitHub-style Day × Hour matrix (7-day) | Color: Green→Yellow→Red (0-50-100 failures) |
| 🔥 Risky Command Activity Heatmap | GitHub-style Day × Hour matrix (7-day) | Color: Green→Yellow→Red (0-50-100 commands) |
| 💎 Security Event Bubble Chart | Bubble Chart | Config Changes vs Auth Failures with size=Risk Score |
| 📊 User Risk Distribution | Horizontal Bar Chart | Top 15 users by combined risk score |

### 9.3 SPL Security Risks

**Risky Commands Assessment:**

| **Command** | **Risk Level** | **Current Control** | **Recommendation** |
| ----------- | -------------- | ------------------- | ------------------ |
| `delete`    | 🔴 High        | Role-restricted     | Audit all usage    |
| `collect`   | 🟡 Medium      | No restriction      | Add role control   |
| `dboutput`  | 🟡 Medium      | Limited             | Review permissions |
| `sendemail` | 🟡 Medium      | Role-restricted     | Audit recipients   |
| `script`    | 🔴 High        | Disabled            | Maintain disabled  |

### 9.4 Hardening Recommendations

| **Control**          | **Current**    | **Recommendation**         | **Priority** |
| -------------------- | -------------- | -------------------------- | ------------ |
| Risky commands       | Partial        | Restrict collect/dboutput  | P1           |
| Session timeout      | 24 hours       | Reduce to 8 hours          | P2           |
| IP allowlisting      | Not configured | Implement for admin access | P2           |
| Audit retention      | 30 days        | Extend to 90 days          | P2           |
| Certificate rotation | Annual         | Quarterly rotation         | P3           |
---

## 📊 10. Key Performance Indicators (KPIs)

### 10.1 Assessment Dashboard Thresholds Reference

All dashboards follow a standardized color-coded threshold system:

| **Color** | **Hex Code** | **Meaning** | **Action Required** |
| --------- | ------------ | ----------- | ------------------- |
| 🟢 Green  | `#65a637`    | Healthy     | None                |
| 🟡 Yellow | `#f7bc38`    | Warning     | Investigation       |
| 🔴 Red    | `#d93f3c`    | Critical    | Immediate action    |
| 🔵 Blue   | `#1e93ff`    | Info/Accent | Informational       |
| 🟢 Teal   | `#4ECDC4`    | Secondary   | Informational       |

### 10.2 KPI Threshold Matrix by Domain

**Licensing & SVC:**

| **KPI**               | **Green** | **Yellow** | **Red**  |
| ---------------------- | --------- | ---------- | -------- |
| SVC Utilization %      | <80%      | 80-95%     | >95%     |
| Daily Ingest vs Limit  | <85%      | 85-95%     | >95%     |
| SVC Overage Days (30d) | 0         | 1-2        | >2       |

**Search Performance:**

| **KPI**                  | **Green** | **Yellow** | **Red**  |
| ------------------------ | --------- | ---------- | -------- |
| Search Success Rate      | >98%      | 90-98%     | <90%     |
| Avg Search Latency       | <30 sec   | 30-60 sec  | >60 sec  |
| Search Concurrency       | <60%      | 60-80%     | >80%     |
| Skipped Searches/Day     | <2        | 2-5        | >5       |
| Long-Running (>5 min)    | <20       | 20-50      | >50      |

**Platform Health:**

| **KPI**              | **Green** | **Yellow** | **Red**  |
| -------------------- | --------- | ---------- | -------- |
| CPU Utilization %    | <60%      | 60-80%     | >80%     |
| Memory Usage %       | <75%      | 75-90%     | >90%     |
| Queue Fill %         | <60%      | 60-80%     | >80%     |
| Forwarder Uptime     | >99%      | 98-99%     | <98%     |

**Enterprise Security:**

| **KPI**                    | **Green** | **Yellow** | **Red**  |
| -------------------------- | --------- | ---------- | -------- |
| Correlation Success Rate   | >98%      | 95-98%     | <95%     |
| Avg Correlation Runtime    | <30 sec   | 30-60 sec  | >60 sec  |
| Max Correlation Runtime    | <120 sec  | 120-300 sec| >300 sec |
| Notable Triage Rate        | >90%      | 70-90%     | <70%     |
| False Positive Rate        | <15%      | 15-25%     | >25%     |
| Skipped Correlations/Day   | <2        | 2-5        | >5       |

**Access Management:**

| **KPI**                   | **Green** | **Yellow** | **Red**  |
| ------------------------- | --------- | ---------- | -------- |
| Inactive Users (90d)      | <10       | 10-50      | >50      |
| Admin Account Count       | <10       | 10-15      | >15      |
| Auth Failures (period)    | <10       | 10-50      | >50      |
| Auth Failure Rate         | <5%       | 5-15%      | >15%     |

**Security Posture:**

| **KPI**               | **Green** | **Yellow** | **Red**  |
| ---------------------- | --------- | ---------- | -------- |
| Risky Commands Used    | <5        | 5-20       | >20      |
| Auth Failures          | <10       | 10-50      | >50      |
| Admin Accounts         | <10       | 10-15      | >15      |

### 10.3 Advanced Visualization Features

All dashboards implement the following advanced visualization patterns:

**GitHub-Style Heatmaps (Day × Hour Matrix):**
- Used in: License Consumption, Search Performance, ES Health, Access & RBAC, Security Posture
- Pattern: 7 rows (Monday–Sunday) × 24 columns (00–23)
- Full 168-cell matrix via `appendpipe` + `makemv` + `mvexpand`
- Color scaling: `minMidMax` with Green(min) → Yellow(mid) → Red(max)

**Bubble Charts:**
- Used in: ES Health (Correlation Performance), Security Posture (Event Distribution)
- Dimensions: X=metric1, Y=metric2, Size=risk/impact score
- Max bubble size: 50

**Horizontal Bar Charts:**
- Used in: License Consumption (Top Sourcetypes), Access & RBAC (Role Distribution), Security Posture (User Risk)
- Top N: 15–20 items with descending sort

---

## 🎯 11. Detailed Action Plan

### 11.1 Priority Matrix

```
        HIGH IMPACT
            │
   P1 ──────┼────── P2
   Quick     │      Strategic
   Wins      │      Projects
            │
─────────────┼───────────────
            │
   P3 ──────┼────── P4
   Low       │      Backlog
   Priority  │      Items
            │
         LOW IMPACT
  LOW EFFORT         HIGH EFFORT
```

### 11.2 Phase 1 — Immediate Actions (0-30 Days)

| **#** | **Action**                        | **Owner**       | **Status**  | **Impact** |
| ----- | --------------------------------- | --------------- | ----------- | ---------- |
| 1.1   | Deploy assessment dashboards      | Splunk Admin    | ✅ Complete | High       |
| 1.2   | Implement Workload Management     | Splunk Admin    | ⏳ Pending  | Critical   |
| 1.3   | Disable 127 inactive accounts     | Security Team   | ⏳ Pending  | High       |
| 1.4   | Tune top 10 expensive ES searches | SOC Engineering | ⏳ Pending  | High       |
| 1.5   | Deploy license overage alerts     | Splunk Admin    | ⏳ Pending  | High       |
| 1.6   | Add search scheduling spread      | Splunk Admin    | ⏳ Pending  | Medium     |

### 11.3 Phase 2 — Optimization (30-90 Days)

| **#** | **Action**                     | **Owner**       | **Status** | **Impact** |
| ----- | ------------------------------ | --------------- | ---------- | ---------- |
| 2.1   | Optimize remaining 40 searches | SOC Engineering | ⏳ Pending | High       |
| 2.2   | Accelerate 10 data models      | Splunk Admin    | ⏳ Pending | High       |
| 2.3   | Implement charge-back model    | FinOps/Splunk   | ⏳ Pending | Medium     |
| 2.4   | Quarterly access reviews       | Security Team   | ⏳ Pending | High       |
| 2.5   | Document all custom roles      | Security Team   | ⏳ Pending | Medium     |
| 2.6   | SmartStore cache optimization  | Splunk Admin    | ⏳ Pending | Medium     |

### 11.4 Phase 3 — Transformation (90-180 Days)

| **#** | **Action**                    | **Owner**       | **Status** | **Impact** |
| ----- | ----------------------------- | --------------- | ---------- | ---------- |
| 3.1   | MITRE ATT&CK coverage to 80%  | SOC Engineering | ⏳ Pending | High       |
| 3.2   | ML-based analytics enablement | Data Science    | ⏳ Pending | High       |
| 3.3   | Federated search evaluation   | Splunk Admin    | ⏳ Pending | Medium     |
| 3.4   | Automated data onboarding     | Platform Team   | ⏳ Pending | Medium     |
| 3.5   | FinOps governance framework   | Management      | ⏳ Pending | High       |

### 11.5 Resource Requirements

| **Resource**           | **Phase 1** | **Phase 2** | **Phase 3** |
| ---------------------- | ----------- | ----------- | ----------- |
| Splunk Admin (FTE)     | 0.5         | 0.5         | 0.25        |
| SOC Engineer (FTE)     | 0.25        | 0.5         | 0.5         |
| Security Analyst (FTE) | 0.25        | 0.25        | 0.25        |
| External Consulting    | -           | Optional    | Recommended |

### 11.6 Success Criteria

| **Domain**        | **Current**   | **30-Day Target** | **90-Day Target** | **180-Day Target** |
| ----------------- | ------------- | ----------------- | ----------------- | ------------------ |
| Health Score      | 68/100        | 75/100            | 82/100            | 88/100             |
| SVC Utilization   | 90% peak/105% | <90%              | <85%              | <80%               |
| Search Latency    | 12.4 sec avg  | <10 sec           | <8 sec            | <6 sec             |
| ES Correlation FP | 32%           | 25%               | 15%               | <10%               |
| MITRE Coverage    | 47%           | 55%               | 70%               | 80%                |
| Inactive Accounts | 127           | <50               | <10               | <5                 |
| Platform SLA      | 99.2%         | 99.4%             | 99.6%             | 99.7%              |

---

## 📎 Appendices

### Appendix A — Dashboard Files Inventory

| **File**                                          | **Lines** | **Panels** | **Base Searches** | **Advanced Viz**                    |
| ------------------------------------------------- | --------- | ---------- | ----------------- | ----------------------------------- |
| `assessment_executive_overview.xml`               | 619       | 28         | 0                 | Color-coded top issues table        |
| `assessment_license_consumption.xml`              | 1,065     | 30+        | 0                 | Heatmap, Area with overlays         |
| `soc_search_performance_monitoring_dashboard.xml` | 1,564     | 40+        | 4                 | 2 Heatmaps, dynamic host resolution |
| `assessment_es_health.xml`                        | 838       | 30+        | 0                 | 2 Heatmaps, Bubble chart            |
| `assessment_access_rbac.xml`                      | 757       | 25+        | 0                 | Heatmap                             |
| `assessment_platform_health.xml`                  | 697       | 30+        | 0                 | Trend lines, error tables           |
| `assessment_security_posture.xml`                 | 787       | 25+        | 0                 | 2 Heatmaps, Bubble chart            |
| **Total**                                         | **6,327** | **~208**   | **4**             | **Multiple per dashboard**          |

### Appendix B — Common SPL Patterns Used Across Dashboards

**1. SVC Entitlement Query (with dynamic stack resolution):**

```spl
(index=_cmc_summary OR index=summary) source="splunk-svc"
    [ rest /services/server/info splunk_server=local
    | fields splunk_server
    | rex field=splunk_server "^[^.]+[.](?<stack>[^.]+)"
    | eval host="*." .stack. ".*splunk*"
    | fields host]
| stats max(stack_license_svc) as svc_license_entitlement
```

**2. Authentication Events (CIM-Compliant):**

```spl
| Successful Logins:
index=_audit action=success info=succeeded "tag::eventtype"=authentication
| stats count

| Failed Logins:
index=_audit action=failure info=failed "tag::eventtype"=authentication
| stats count
```

**3. Risky Command Detection (with noise exclusions):**

```spl
index=_audit action=search info=completed
    NOT user IN ("splunk-system-user")
    NOT app IN (godaddy_health)
| eval search_query=coalesce(search, search_string)
| where match(search_query,"(?i)\|\s*(delete|collect|dboutput|script|sendemail)\b")
| stats count by user, search_query
```

**4. GitHub-Style Heatmap Template (Day × Hour):**

```spl
| bin _time span=1h
| eval hour=strftime(_time, "%H")
| eval day=strftime(_time, "%A")
| eval day_order=case(day="Monday", 1, day="Tuesday", 2, day="Wednesday", 3,
    day="Thursday", 4, day="Friday", 5, day="Saturday", 6, day="Sunday", 7)
| stats count as value by day, day_order, hour
| appendpipe [| stats count
    | eval day="Monday,Tuesday,Wednesday,Thursday,Friday,Saturday,Sunday"
    | makemv delim="," day | mvexpand day
    | eval hour="00,01,02,...,23"
    | makemv delim="," hour | mvexpand hour
    | eval value=0
    | eval day_order=case(day="Monday",1,...)]
| stats max(value) as value, max(day_order) as day_order by day, hour
| xyseries day hour display_value
| sort day_order | fields - day_order
| fillnull value="-"
```

**5. CPU / Memory Utilization:**

```spl
index=_introspection component=Hostwide
| eval cpu_pct=round('data.cpu_system_pct' + 'data.cpu_user_pct', 1)
| eval mem_pct=round(('data.mem_used'/'data.mem')*100, 1)
| stats avg(cpu_pct) as cpu_pct, avg(mem_pct) as mem_pct
```

**6. Queue Backpressure Monitoring:**

```spl
index=_internal source=*metrics.log group=queue
    (name=parsingQueue OR name=indexQueue OR name=typingQueue)
| eval fill_pct=if(max_size_kb>0, round((current_size_kb/max_size_kb)*100, 1), 0)
| timechart span=5m avg(fill_pct) by name
```

### Appendix C — Design Standards Summary

**Theme:** Dark (`theme="dark"`) — all dashboards  
**Panel Layout:** Max 4 panels per row, HTML section headers  
**Color Palette:** Green `#65a637`, Yellow `#f7bc38`, Red `#d93f3c`, Blue `#1e93ff`, Teal `#4ECDC4`  
**Severity Pattern:** Emoji prefixes (`🔴 CRITICAL`, `🟡 ELEVATED`, `🟢 NORMAL`)  
**Range Colors:** `["0x65a637","0xf7bc38","0xd93f3c"]` (ascending severity)  
**Time Defaults:** `-4h@h` to `now` (most dashboards), `-24h@h` for overview  
**Refresh:** Progress bar enabled on all panels

### Appendix D — Quick Navigation

| **Dashboard**      | **Access URL (relative)**                                     |
| ------------------ | ------------------------------------------------------------- |
| Executive Overview | `/app/<app_name>/assessment_executive_overview`               |
| License & SVC      | `/app/<app_name>/assessment_license_consumption`              |
| Search Performance | `/app/<app_name>/soc_search_performance_monitoring_dashboard` |
| ES Health          | `/app/<app_name>/assessment_es_health`                        |
| Access & RBAC      | `/app/<app_name>/assessment_access_rbac`                      |
| Platform Health    | `/app/<app_name>/assessment_platform_health`                  |
| Security Posture   | `/app/<app_name>/assessment_security_posture`                 |

---

_Report Revision v2.0 — Generated February 11, 2026_  
_Reflects all implemented assessment dashboards (7 dashboards, ~6,327 total XML lines, ~208 panels)_  
_Next Scheduled Review: March 11, 2026_
