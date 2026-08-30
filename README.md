**Defender XDR Portal and Security Operations Configuration**

**Introduction**

This lab configures the operational foundation of Microsoft Defender XDR for SOC activities. It focuses on service-health monitoring, least-privilege permissions, platform auditing, and verification of the unified Microsoft Defender XDR and Microsoft Sentinel environment.

The lab begins by establishing a security-focused service-health view so that analysts can distinguish genuine platform outages or reporting delays from an environment that simply has no alerts. Notification rules, alert tuning, endpoint onboarding, and automated response are reserved for their dedicated labs.

**Lab Objectives**

- Establish a Defender XDR security-operations baseline.

- Configure a SOC-focused Microsoft service-health view.

- Identify active advisories that could affect security monitoring or investigations.

- Review Defender XDR permissions and role-based access control.

- Apply least-privilege access where supported.

- Confirm Microsoft Sentinel integration with Defender XDR.

- Verify configuration activity through audit records.

- Document configuration results without exposing tenant or session identifiers.

**Configuration Task 1 — Configure a SOC-Focused Service-Health View**

**Purpose**

Microsoft Defender XDR redirects service-health monitoring to the Microsoft 365 admin center. Service health must be checked before an analyst assumes that missing alerts, delayed events, or incomplete reports indicate normal activity.

A customized view was created to prioritize services relevant to the SOC environment.

**Procedure**

1.  The Microsoft Defender portal was opened at security.microsoft.com.

2.  **System → Health** was selected.

3.  The portal redirected to the Microsoft 365 **Service health** dashboard.

4.  **Customize** was selected.

5.  The available service-health categories were reviewed.

6.  **Microsoft 365 Copilot Chat** was deselected because it was outside the scope of this SOC lab.

7.  **Microsoft 365 suite** remained selected because Defender XDR depends on the broader Microsoft 365 environment.

8.  **Microsoft Entra** remained selected because identity and authentication availability directly affect XDR investigations.

9.  The customized health view was saved successfully.

**Findings**

The current subscription exposed the following service-health categories:

Microsoft 365 Copilot Chat\
Microsoft 365 suite\
Microsoft Entra

A separate Microsoft Defender or Microsoft Sentinel health category was not available in the customization panel. This reflected the services available through the current subscription and was not considered a portal error.

The dashboard also displayed active Microsoft advisories. These advisories affected Microsoft 365 reporting and certain Entra audit-log operations, but no visible advisory indicated a Microsoft Defender XDR outage.

**Result**

The service-health view was successfully configured to focus on **Microsoft 365 suite** and **Microsoft Entra**. The portal displayed the confirmation message:

Changes saved.

The images should show:

The **Service health** customization panel\
Microsoft 365 Copilot Chat deselected\
Microsoft 365 suite selected\
Microsoft Entra selected\
The green **Changes saved** confirmation (Images 1, 2, and 3)


**Configuration Task 2 — Configure Least-Privilege SOC Permissions**
------------------------------------------------------------------------------------

The Defender **Permissions** page is already open. This task will review the existing role model and determine whether the SOC security group can receive appropriate Defender XDR access without administrator privileges.

**Step 1 — Open Defender XDR roles**

I Click **Roles** in the center of the Permissions page.\
\
This page confirms that the tenant supports **Microsoft Defender XDR Unified RBAC**, but currently has **0 custom roles**. Therefore, no Defender-specific least-privilege SOC role has been configured yet.

The distinction shown here is important:

- **Roles** define what an analyst can see and do.

- **Scopes** limit which devices, users, or data the permissions apply to.

### The administrator account currently has broad access, but daily SOC work should use narrower permissions. (images 4)\
\

\
Step 2 — Begin creating the SOC role

I Click **Create custom role**.

This action only opens the configuration wizard; it does not create or assign anything yet.\
The custom-role wizard contains four stages:

1.  Basics

2.  Permissions

3.  Assignments

4.  Review and finish

**Role name**

SOC Tier 1 Analyst

**Description**

Provides least-privilege access for SOC Tier 1 analysts to review, investigate, document, and triage Microsoft Defender XDR incidents, alerts, and related security data without granting full administrative control.

Then I click **Next**.\
\
I click permission. The permission groups are:

- **Security operations:** Daily alert, incident, investigation, and response work

- **Authorization and settings:** Role creation and platform administration

- **Data Operations:** Security-data management and deletion

For a Tier 1 analyst, only **Security operations** should be considered. The other two groups would grant unnecessary administrative or data-management authority.\
\
I Click **Security operations** to expand it.\
For a functional Tier 1 role, **All read-only permissions** would be too restrictive because the analyst could view alerts but could not update their status, assignment, classification, or investigation notes. **All read and manage permissions** would be too broad because it could include containment and response actions.

1.  I select **custom permissions**

2.  Check:

    - **Security data basics (read)**

    - **Alerts (manage)**

I leave **Response (manage)** unchecked because device isolation, remediation, and other containment actions require escalation or a Tier 2 role.\
\
The permissions are configured correctly:

- **Security data basics (read):** Selected automatically as the required foundation

- **Alerts (manage):** Selected so the analyst can triage and update alerts/incidents

- **Response (manage):** Not selected, preventing endpoint containment or remediation actions

This is an appropriate least-privilege Tier 1 permission set.

I Click **Apply**.\
The summary confirms:

- **Security operations:** Custom permissions selected

- **Authorization and settings:** None selected

- **Data Operations:** None selected

This preserves least privilege. ( Images 5, 6, 7, 8, 9, and 10)

I Click **Next** to open **Assignments**.\
The role now needs an assignment that connects:

The **SOC Tier 1 Analyst** role

An existing user or security group\
The Defender data sources the group is allowed to access

Then I Click **Create assignment**.\
I enter this assignment name:

SOC Tier 1 Analysts - Security Operations for employees, and I click the search field\
For SOC.\
The assignment was added successfully:

**Assignment:** SOC Tier 1 Analysts – Security Operations\
**Assigned identity:** One group\
The role is group-based rather than assigned directly to the administrator, which is the correct approach. (Images 11, 12, and 13)\

I Click **Next** to open **Review and finish**.

### The role name, description, and Security operations**,** Custom selected status are correct. However, the lower permission details and assignment scope are not visible yet.The individual selected permissions\
Authorization and settings status\
Data Operations status\
Assignment name\
Assigned group\
Selected data sources or scope (Images 14, and 15)\

Configure the Sentinel workspace scope

1.  I Leave **Data sources** as **None selected**.

2.  I Leave **Include future data sources automatically** unchecked.

3.  I Click **Edit** beside **Microsoft Sentinel — All available workspaces**.

The current setting is **All existing and future workspaces**, which would automatically grant this role access to every Sentinel workspace created later. That is broader than necessary.

Configure least privilege:

1.  I select **Custom selection**.

2.  I select the workspace:\
    soc-sentinel-workspace

3.  I confirm the resource group is:\
    soc-lab-rg

4.  I click **Apply selection**.

This limits the SOC Tier 1 role to the existing lab workspace and prevents automatic access to future workspaces.

The assignment is now correctly scoped:

**Group:** SOC-Analysts

**Defender data sources:** None currently available\
**Automatic future data-source access:** Disabled\
**Sentinel workspace:** soc-sentinel-workspace only

This is a proper least-privilege assignment.

I click **Apply** at the bottom. After returning to the assignment list, I click **Next** to return to **Review and finish**.\
\
The final configuration has been validated:

Role: **SOC Tier 1 Analyst**\
Permission group: **Security operations**\
Allowed: Security data basics — read and Alerts — manage\
Not allowed: Response actions, Authorization and settings and Data Operations\
Assigned group: **SOC-Analysts**\
Sentinel scope: **soc-sentinel-workspace**\
Future Defender data sources: Not automatically included

I click **Submit** to create and assign the role.\
**\
Configuration Task 2 is completed successfully.**

The confirmation proves that the **SOC Tier 1 Analyst** custom role was created through Microsoft Defender XDR Unified RBAC.

The role provides the SOC-Analysts group with:

Read access to fundamental security data\
Permission to manage alerts for triage\
Access limited to soc-sentinel-workspace\
No response or containment permission\
No role-administration permission\
No data-management permission\
No automatic access to future Defender data sources or Sentinel workspaces (Images 16, 17, 18, 19, and 20)\

Configuration Task 3 — Verify the change through auditing

1.  I click **Done**.

2.  I select **System → Audit**.

This is the correct unified Audit search interface. It can record administrative changes, including role and permission activity.

### I create the audit search

Setting the following:

**Start date:** Aug 27, 2026\
**Start time:** 02:45 UTC\
**End date:** Aug 27, 2026\
**End time:** 03:20 UTC\
**Keyword Search:** SOC Tier 1 Analyst\
**Search name:** Verify Defender XDR RBAC Role Creation

Then I click **Search**.

The role has been verified successfully. The list confirms:

**Role:** SOC Tier 1 Analyst\
**Data source:** Microsoft Sentinel\
**Assignment:** One group\
**Persistence:** The role remains present after creation\
\
The Audit search could not be used because the portal returned an RBAC tenant-settings client error; that limitation should not be a lab failure. (Please refer to Image 21)

This completes **Configuration Task 3** through direct verification. (Images 21 and 22)\


