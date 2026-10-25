# HRAgent

### Business Context

TechCorp is a mid-sized IT services company with about 2,500 employees across three office locations. Its six-person HR team receives around 80 to 100 employee queries each day through email and a shared HR helpdesk inbox. Most of these queries are routine, covering topics such as leave balances, leave policies, holiday calendars, notice periods, and reimbursement rules.

Although these questions are generally straightforward, answering them often requires the HR team to gather information from multiple sources. An executive may need to check the HRMS for employee-specific details, refer to the relevant policy document, and perform a small calculation before responding. As a result, each query typically takes 10 to 20 minutes, adding up to a significant portion of the team's daily workload.

TechCorp wants to reduce this dependency on HR for routine queries by enabling employees to receive accurate answers to questions that can be resolved from available employee data, policies, and calculations. This would allow the HR team to spend more time on higher-value activities such as onboarding, employee engagement, and performance-related initiatives.

### Objective

Build an **HR Employee Support Agent** that independently handles employee queries by deciding what information and tools it needs to answer each query reliably. The agent should reason over the employee’s request, retrieve relevant employee-specific information from the HR database, consult HR policies when required, perform any necessary calculations, and use the gathered information to formulate an accurate response.

The goal is not to make the process faster. It is to make the process correct enough that HR can trust the agent to answer on its own for a meaningful share of queries. Speed and cost are not part of this goal; only correctness and reliability are measured.

A query the agent cannot answer reliably should not be forced through. It should be handed off to the HR team, the same as today.


### Dataset Description

The agent uses two types of data sources to answer employee queries: a structured SQLite database and unstructured PDF policy documents.

**Structured Data (SQLite Database: `hr_database.db`)**

| Table Name | Rows | Description |
| -| -| -|
| `employees` | 100 | Master employee profiles containing ID, name, email, department, designation, level, manager, date of joining, employment type, and office location |
| `leave_records` | 538 | Leave applications (casual, sick, earned) with dates, duration, approval status, and approver |
| `attendance_logs` | 17,268 | Daily attendance records for 2026 with check-in/check-out times, status (present, WFH, absent, holiday, half_day), and total hours worked |
| `payroll` | 799 | Monthly salary records (January to August 2026) broken down into basic salary, HRA, special allowance, bonus, tax deducted, PF/401(k) deducted, and net salary |
| `performance_reviews` | 189 | Half-yearly performance reviews (H2 2025 and H1 2026) with ratings, promotion recommendations, training hours, and review dates |

**Unstructured Data (PDF Policy Documents)**

| Document | Pages | Description |
| -| -| -|
| `employee_handbook.pdf` | 4 | General company policies covering code of conduct, flexible hours, notice periods, dress code, workplace safety, and remote work guidelines |
| `leave_policy.pdf` | 4 | Leave entitlements (casual, sick, earned), WFH limits, carry-forward rules, approval workflows, and holiday calendar |
| `benefits_guide.pdf` | 4 | Health insurance plans, 401(k) matching, loyalty bonus, tax declaration process, wellness programs, and family benefits |
| `ld_policy.pdf` | 5 | Performance review process, rating criteria, promotion eligibility, certification sponsorship, and mandatory training requirements |

**Additional Data Files**

| File | Description |
| -| -|
| `sample_data.csv` | 8 sample queries (2 per category) with expected responses, used for validation during development |
| `test.csv` | 20 held-out test queries (5 per category) with reference responses, used for final evaluation |
| `config.json` | API configuration containing the OpenAI API key and base URL |


### Success Criteria

The agent is evaluated on a **held-out test set of 20 queries** with reference responses. Each response is scored by an LLM judge on **correctness, completeness, and guardrail compliance**.

| Evaluation Metric | Criterion |
| -| -|
| **Test Set** | 20 held-out queries with reference responses |
| **Scoring** | Each response is scored from **0.0 to 1.0** |
| **Evaluation Dimensions** | Correctness, completeness, and guardrail compliance |
| **Pass Threshold (per query)** | Score **>= 0.7** |
| **Overall Pass Criterion** | At least **60% of test queries** must pass |
| **Decision** | If the overall criterion is met, queries from the **passing categories** can be routed to the agent; remaining categories stay under manual HR review |