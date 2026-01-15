# Enterprise Access Validation System

## 📘 Overview
A nightly automation pipeline for validating enterprise user directory data, built to ensure only clean, accurate user records are ingested into critical business applications.

This system extracts user data from multiple regional Active Directory environments, applies hard-coded validation rules, outputs ingestible user lists, and generates detailed exception reports for business stakeholders. It significantly reduced support overhead, improved user access reliability, and enabled local teams to self-remediate issues without escalation.

---

## 💡 Business Problem
Prior to this system, daily user imports into a centralised application often failed due to corrupt or inconsistent Active Directory data. Consequences included:
- Application crashes or failed ingestions
- Users unable to access critical alerts or reporting dashboards
- Incorrect reporting hierarchies and store assignments
- Frequent manual cleanup work by the support team
- 30+ monthly support tickets related to user data issues

The previous system involved duplicated, poorly maintained scripts for each country, leading to unscalable and error-prone operations.

---

## 🎯 Goals
- Validate AD user entries before ingest to prevent crashes
- Flag and remove invalid users while capturing exact failure reasons
- Empower business stakeholders to resolve issues independently
- Unify fragmented scripts into a maintainable modular codebase

---

## 🔧 Architecture Overview

| Layer               | Description                                      |
|--------------------|--------------------------------------------------|
| **Source**         | Active Directory (regional per country)          |
| **Validation Engine** | PowerShell-based class/controller architecture |
| **Pipeline Trigger** | GitLab CI Nightly Jobs                         |
| **Outputs**        | JSON (ingestible user list), TXT exception reports |
| **Notifications**  | Automated email alerts to stakeholders           |
| **Hosting**        | Shared Git repo, deployed via GitLab CI         |

See `/architecture/` for diagrams (system context, component, and flow).

---

## 🛠 Example Validation Rules

Each user was passed through a series of hard-coded checks such as:

- Remove Area Managers not assigned to any stores
- Remove Store Managers without valid `EmployeeID`
- Reject users with multiple AD group memberships
- Detect and remove users with duplicate store assignments

Users failing any rule were excluded from the JSON ingest file and logged in a detailed TXT exception report.

---

## 📤 Output Artifacts

| File | Description |
|------|-------------|
| `users_valid.json` | Final list of valid users for ingestion |
| `exception_report.txt` | UserID, username, AD group, reason for failure |
| Email Notification | Sent to country stakeholders with report links |

The reports were uploaded to SharePoint and accessible to L1 support and national IT managers, enabling self-service debugging.

---

## 🧱 Modular Architecture

The system was refactored into:
- Separate classes for AD connection, user objects, and validations
- Reusable controllers for ingest/export logic
- Shared logging utility for reporting and alerts

This replaced a brittle if-else tree with a maintainable and testable structure that supported all countries through shared code.

---

## 🚀 Deployment & Automation

The pipeline:
1. Triggered nightly via GitLab CI
2. Queried AD per country
3. Ran validation engine
4. Generated reports and uploaded to SharePoint
5. Sent alert emails on success/failure

Environments: **Test**, **UAT**, **Production**

Code updates were made as new user types, AD groups, or business logic changes were introduced (e.g. trimming leading zeros from `EmployeeID` fields).

---

## 👥 Stakeholder Collaboration

- Collaborated with internal developers and external vendors on refactoring and modularisation
- Led stakeholder demos and trained L1 support and technical team members on how to interpret reports and perform basic script updates
- Authored a complete internal `README` to support onboarding and maintenance

---

## ✅ Outcomes

| Metric | Before | After |
|--------|--------|-------|
| Monthly access-related incidents | ~30 | <5 |
| Time spent on cleanup | High | Minimal |
| Time to resolve user issues | Days (with escalation) | Hours (self-service) |

Business units across countries praised the system for enabling fast issue diagnosis and smoother onboarding. The technical team benefited from reduced incident noise and clearer code ownership.

---

## 🗺 Future Improvements

- Move rules to external config for more flexible rule management
- Add Power BI dashboards to visualise exception trends
- Implement retry logic for transient AD query failures

---

## 🔒 Disclaimer

This project is a fictionalised representation based on real professional experience. It contains no proprietary code or confidential data. All names, systems, and data structures have been anonymised or recreated from scratch.
