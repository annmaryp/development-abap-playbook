[development-abap-playbook (https://annmaryp.github.io/development-abap-playbook/)]

# SAP Application Job Scheduling (SAPJ)
## Step-by-Step Cookbook

A practical recipe-style guide for creating Job Catalogs, Job Templates, and scheduling Application Jobs.   

---

|||
|---|---|
| **Document Type** | Cookbook (Step-by-Step Procedural Guide) |
| **Audience** | Developers (Recipes A, B) + Functional Users (Recipe C) |
| **System** | SAP S/4HANA \| SAP BTP ABAP Environment |
| **Version** | 1.0 |

---

## How to Use This Cookbook

This cookbook contains three recipes, each representing a distinct task in the SAP Application Job Scheduling lifecycle. Each recipe is self-contained and includes prerequisites, exact steps, field values, and post-completion checks.

| Recipe | Task | Tool / App | Who |
|---|---|---|---|
| **A** | Create a Job Catalog Entry | Transaction SYCM | Developer |
| **B** | Create a Job Template | Fiori App F2058 | Developer |
| **C** | Schedule an Application Job | Fiori App F1240 | Functional User |

---

## Recipe A — Create a Job Catalog Entry

> **Tool:** Transaction SYCM &nbsp;|&nbsp; **System:** DEV &nbsp;|&nbsp; **Who:** Developer

### Prerequisites

- You have an ABAP class that implements `IF_APJ_DT_EXEC_OBJECT` (methods `EXECUTE` and `GET_PARAMETERS` are implemented).
- The class is activated and syntactically correct.
- You have authorisation for transaction `SYCM` (role `S_BTCH_ADM` or equivalent).
- A transport request is open or you are authorised to create one.
- Naming convention for the Catalog ID has been agreed (see guidelines document).

---

### Steps

#### Step 1 — Launch Transaction SYCM

| Sub-step | Action |
|---|---|
| 1.1 | Log on to the DEV SAP system. |
| 1.2 | In the command field (transaction bar), type: `SYCM` and press **Enter**. |
| 1.3 | The *Maintain Job Catalog* screen opens, showing existing catalog entries in a list. |

---

#### Step 2 — Create a New Catalog Entry

| Sub-step | Action |
|---|---|
| 2.1 | Click the **New** button (or press **F5**) in the application toolbar. |
| 2.2 | A new row appears in the catalog list, or a dialog box opens depending on your SAP release. |
| 2.3 | If prompted, confirm that you want to create a new entry. |

---

#### Step 3 — Enter the Catalog ID and Basic Details

| Sub-step | Action |
|---|---|
| 3.1 | **Catalog ID:** Enter your ID following the naming convention, e.g. `ZFI_PAYMENT_RUN_JC` |
| 3.2 | **Description:** Enter a clear, business-readable description in English, e.g. *FI Payment Run - Automatic Payment Program* |
| 3.3 | **Class Name:** Enter the fully qualified ABAP class name, e.g. `ZCL_FI_PAYMENT_RUN_BATCH` |
| 3.4 | Verify the class name resolves (no error indicator) — the system validates the class exists. |

> ⚠️ **Note:** The Class Name must be the exact global class name in SE24, including the namespace prefix.

---

#### Step 4 — Configure Parameter Visibility

| Sub-step | Action |
|---|---|
| 4.1 | The system reads the parameters defined in your `GET_PARAMETERS` method and lists them. |
| 4.2 | For each parameter, set the **Visibility** flag: *Visible* = functional user can see and edit; *Hidden* = internal/technical parameter, not shown to users. |
| 4.3 | Set the **Required** flag for parameters that the functional user must provide before scheduling. |
| 4.4 | Set the **Locked** flag for parameters whose default value must not be changed by the functional user. |
| 4.5 | Assign **F4 Value Help** for parameters that have restricted allowed values (link to existing search helps or fixed value lists). |

> ⚠️ **Note:** At minimum, any parameter that directly affects data selection or output scope must be visible to the user.

---

#### Step 5 — Assign to a Package and Transport

| Sub-step | Action |
|---|---|
| 5.1 | Click the **Package** assignment button (or navigate to the transport assignment dialog). |
| 5.2 | Enter or select your development package, e.g. `Z_FI_BATCH`. |
| 5.3 | When prompted for a transport request, enter your existing transport number or click **Create Request** to generate a new one. |
| 5.4 | Provide a meaningful transport description, e.g. *SAPJ Job Catalog - FI Payment Run*. |

---

#### Step 6 — Save and Activate

| Sub-step | Action |
|---|---|
| 6.1 | Click **Save** (`Ctrl+S`). |
| 6.2 | The catalog entry is saved. The status indicator should turn green or show *Active*. |
| 6.3 | If the system shows a consistency check warning, review and resolve before proceeding. |

---

### ✅ Post-Completion Check — Recipe A

- [ ] In SYCM, locate your new entry in the list by searching for your Catalog ID.
- [ ] Confirm the Status is **Active**.
- [ ] Confirm the Class Name resolves without error (no red indicator).
- [ ] Confirm all parameters are listed and visibility flags are correct.
- [ ] Check that the entry is assigned to the correct package and transport request.
- [ ] Proceed to **Recipe B** to create the first Job Template for this catalog entry.

---

## Recipe B — Create a Job Template

> **Tool:** Fiori App F2058 (Application Job Templates) &nbsp;|&nbsp; **System:** DEV &nbsp;|&nbsp; **Who:** Developer

### Prerequisites

- Recipe A is complete: the Job Catalog entry exists and is Active.
- You have access to the Fiori Launchpad in the DEV system.
- The Fiori App **F2058** *Application Job Templates* is assigned to your role.
- A transport request is open for the same package as the catalog entry.

---

### Steps

#### Step 1 — Open Fiori App F2058

| Sub-step | Action |
|---|---|
| 1.1 | Log on to the SAP Fiori Launchpad in the DEV system. |
| 1.2 | Search for *Application Job Templates* in the search bar, or navigate to the tile in your assigned group. |
| 1.3 | Click the tile to open F2058. The template list screen opens. |

---

#### Step 2 — Create a New Template

| Sub-step | Action |
|---|---|
| 2.1 | Click the **+** (Create) button in the upper right of the template list. |
| 2.2 | A new template creation dialog or form opens. |

---

#### Step 3 — Enter Template Identification

| Sub-step | Action |
|---|---|
| 3.1 | **Template Name:** Enter a unique, descriptive name. Example: *FI Payment Run - Domestic Weekly* |
| 3.2 | **Description:** Enter a longer description explaining the purpose and intended use case. |
| 3.3 | **Job Catalog Entry:** Click the value help (F4) and select your catalog entry, e.g. `ZFI_PAYMENT_RUN_JC` |
| 3.4 | After selecting the catalog entry, the system loads the parameter list defined in that catalog entry. |

> ⚠️ **Note:** The Template Name must be unique across all templates for the same catalog entry. Use names that describe the specific scenario, not just the job.

---

#### Step 4 — Set Default Parameter Values

| Sub-step | Action |
|---|---|
| 4.1 | The parameter list from the catalog entry is displayed. For each visible parameter: |
| 4.2 | Enter the default value that should be pre-filled when a functional user uses this template. |
| 4.3 | For **Locked** parameters: enter the fixed value. The functional user will see this value but cannot change it. |
| 4.4 | For **Required** parameters without a default: leave blank — the user must supply a value at scheduling time. |
| 4.5 | For **Optional** parameters: entering a default makes scheduling faster; the user can still override unless locked. |
| 4.6 | *Example:* Company Code: `1000` (locked); Payment Method: `C` (default, not locked); Posting Date: blank (required, user fills at runtime). |

---

#### Step 5 — Assign Authorisation Object *(Optional but Recommended)*

| Sub-step | Action |
|---|---|
| 5.1 | In the **Authorisation** section of the template form, assign the relevant authorisation object. |
| 5.2 | This controls which functional users can see and use this template in F1240. |
| 5.3 | Example: assign the template to the role `Z_FI_PAYMENT_SCHEDULING`. |
| 5.4 | If left blank, all users with access to F1240 will see the template *(not recommended for production)*. |

---

#### Step 6 — Configure Step Details *(for multi-step jobs, if applicable)*

| Sub-step | Action |
|---|---|
| 6.1 | If your ABAP class supports multiple steps (rare for standard implementations), navigate to the **Steps** tab. |
| 6.2 | For single-class implementations, the default single step is automatically populated. |
| 6.3 | Verify the class assignment is correct in the Steps view. |

---

#### Step 7 — Save and Assign to Transport

| Sub-step | Action |
|---|---|
| 7.1 | Click **Save** (or the Save icon). |
| 7.2 | The system prompts for a transport request (Workbench or Customising). |
| 7.3 | Select the same transport request used for the catalog entry, or create a new linked request. |
| 7.4 | Confirm. The template status changes to **Active**. |

---

### ✅ Post-Completion Check — Recipe B

- [ ] In F2058, search for your template name. It should appear in the list with status **Active**.
- [ ] Open the template and verify all parameter values and lock/required settings are correct.
- [ ] Confirm the template is linked to the correct catalog entry.
- [ ] Confirm transport assignment is present.
- [ ] *Optional:* Use the **Schedule** function within F2058 to perform a test run in DEV.
- [ ] Communicate template readiness to the functional team for testing in QAS post-transport.

---

## Recipe C — Schedule an Application Job

> **Tool:** Fiori App F1240 (Application Jobs) &nbsp;|&nbsp; **System:** PRD &nbsp;|&nbsp; **Who:** Functional User

> ⚠️ **Important: Production Scheduling Rules**
>
> - This recipe is performed in the **PRODUCTIVE** system by a functional user or key user.
> - **NEVER** create or modify Job Catalog entries or Job Templates in this system.
> - Always use a Job Template as the basis for scheduling — do not schedule jobs without a template.
> - Obtain necessary approvals before scheduling jobs that process large data volumes or run during business hours.

### Prerequisites

- The Job Template has been transported to the productive system (Recipe A and B are complete and transported).
- You have access to the Fiori Launchpad in the productive system.
- The Fiori App **F1240** *Application Jobs* is assigned to your role.
- You have the authorisation to use the specific job template (assigned authorisation object).
- You know the runtime parameters required for this specific execution (e.g., company code, date range).

---

### Steps

#### Step 1 — Open Fiori App F1240

| Sub-step | Action |
|---|---|
| 1.1 | Log on to the SAP Fiori Launchpad in the PRODUCTIVE system. |
| 1.2 | Search for *Application Jobs* in the Fiori search bar, or navigate to the tile in your group. |
| 1.3 | Click the tile. The Application Jobs list opens showing all existing scheduled and completed jobs visible to you. |

---

#### Step 2 — Create a New Application Job

| Sub-step | Action |
|---|---|
| 2.1 | Click the **+** (Create or Schedule New Job) button. |
| 2.2 | A scheduling dialog or wizard opens. You will be guided through template selection and parameter entry. |

---

#### Step 3 — Select the Job Template

| Sub-step | Action |
|---|---|
| 3.1 | In the **Job Template** field, click the value help (F4 / search icon). |
| 3.2 | In the search dialog, type the template name or a keyword from the description. |
| 3.3 | Locate and click your template, e.g. *FI Payment Run - Domestic Weekly*. |
| 3.4 | Click **OK** or **Select**. The system loads all pre-configured parameters from the template. |

> ⚠️ **Note:** If the template you need does not appear in the search results, contact the development team to verify transport status and your authorisation assignment.

---

#### Step 4 — Enter the Job Name

| Sub-step | Action |
|---|---|
| 4.1 | In the **Job Name** field, enter a meaningful name for this specific run. |
| 4.2 | Recommended format: `<TEMPLATE_ABBREV>_<DATE>_<DESCRIPTION>`, e.g. `FI_PAY_DOM_20241215_MONTH_END` |
| 4.3 | A clear job name is essential for monitoring and audit trail identification. |

---

#### Step 5 — Fill in Runtime Parameters

| Sub-step | Action |
|---|---|
| 5.1 | Review the pre-filled parameter values loaded from the template. |
| 5.2 | **Locked** parameters are displayed but cannot be edited — verify their values are correct. |
| 5.3 | **Required** parameters (marked with `*`) must be filled before you can save. Enter the appropriate values. |
| 5.4 | **Optional** parameters can be adjusted if the default does not match your specific run requirements. |
| 5.5 | *Example:* confirm Company Code = `1000` (locked); enter Posting Date = `15.12.2024` (required); Payment Methods = `C` (default, can change if needed). |

---

#### Step 6 — Define the Schedule / Recurrence

| Sub-step | Action |
|---|---|
| 6.1 | In the **Schedule** section, define when the job should run: |
| 6.2 | **Immediate:** select *Start Immediately* if the job should run as soon as you save. |
| 6.3 | **Once (Future):** select *Start On* and enter the specific date and time. |
| 6.4 | **Periodic — Daily:** select *Recurrence* > *Daily* > specify the time and the date range (From / To). |
| 6.5 | **Periodic — Weekly:** select *Recurrence* > *Weekly* > check the days of the week and specify the time. |
| 6.6 | **Periodic — Monthly:** select *Recurrence* > *Monthly* > specify day of month and time. |
| 6.7 | **Periodic — Custom:** use *Custom* to define cron-like expressions if the system supports it. |

> ⚠️ **Note:** For jobs processing large data volumes (e.g. month-end runs), schedule during off-peak hours (e.g. 02:00–05:00). Coordinate with the Basis team for resource-intensive jobs.

---

#### Step 7 — Set Dependencies *(Optional)*

| Sub-step | Action |
|---|---|
| 7.1 | If this job should only start after another specific job completes, use the **Start Condition** or **Dependency** feature. |
| 7.2 | Click **Add Dependency** and search for the predecessor job by name. |
| 7.3 | Select the completion status that triggers this job (e.g., *Finished Successfully*). |
| 7.4 | Dependencies ensure correct execution sequence without manual monitoring. |

---

#### Step 8 — Review and Confirm

| Sub-step | Action |
|---|---|
| 8.1 | Review all entries in the summary screen: template name, job name, parameters, schedule. |
| 8.2 | Click **Schedule** or **Save** to confirm the job scheduling. |
| 8.3 | The job appears in the Application Jobs list with status **Scheduled**. |

---

### ✅ Post-Completion: Monitoring Your Job in F1240

- [ ] In F1240, locate your job by name or filter by status. Status flow: **Scheduled → Running → Finished / Error**.
- [ ] When status is **Finished**, click the job row and select **Show Log** to verify execution messages.
- [ ] Review the number of records processed and confirm expected outcomes.
- [ ] If status is **Error**: click **Show Log** to read the error message. Raise an incident to the development team if the error is in the ABAP logic.
- [ ] If status stays **Scheduled** but does not run: check the schedule time and system background work process availability.

---

## Troubleshooting Quick Reference

| Problem | Likely Cause | Resolution |
|---|---|---|
| Class name not found in SYCM | Class not activated or typo in name | Verify class in SE24 is Active; check name spelling |
| Template not visible in F1240 | Transport not imported or authorisation not assigned | Verify transport status; check role assignment for template auth object |
| Required parameter missing error on scheduling | User did not fill a required field | User must provide a value for all fields marked with `*` |
| Job stays in *Scheduled* status and never runs | No free background work processes or incorrect start time | Check SM50/SM66 for free processes; verify scheduled time is correct |
| Job ends with status *Error* | Business data issue or ABAP runtime error | Read the job log in F1240; escalate to the development team with log details |
| Cannot create catalog entry — no transport found | No open transport request in DEV | Create a new Workbench transport request in SE09 and assign the package |

---

*Internal Use | SAP Center of Excellence | v1.0*
