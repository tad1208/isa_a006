# Sisua Enhancement Protocol  
## SEP1 – On good development practices

**Date:** 02.01.2025  
**Author:** Sisua Digital SpA  
**Purpose:** Standardize configuration and development of UiPath processes so they are **Robust, Efficient and Maintainable (REM)**.

---

## 1. Introduction

This guide defines:

- **Syntax rules**: naming of variables, arguments, workflows, etc.  
- **Development practices**: activity naming, comments, logging, etc.  
- **Input/Output standards**: folder structure, configuration files, worktray, memory, etc.  
- **Recommendations & tips**: for efficient, stable robot design.

The document assumes usage of **UiPath** and especially the **Sisua Standalone Framework**.

---

## 2. Syntax

### 2.1 General syntax

- Configuration and naming should be in **English** for compatibility and easier support.
- When naming anything (variables, arguments, sequences, workflows, activities), always prioritize:
  - **Economy** – short names  
  - **Clarity** – precise wording  
  - **Consistency** – same style across the process  
  - **Universality** – no accents or special characters

#### 2.1.1 Variables

- Prefix the name with a **type abbreviation** (see table below).  
  - Example: `strCustomerName` for a string, `intCounter` for an integer.
- Use **camelCase**: `typePrefixMyVariableName`.
- Keep the **scope as small as possible** (innermost scope).
- Do **not** overload variable names between different scopes.
- Use **correct types**:
  - numbers → `int` / `double`
  - text → `string`
  - dates → `DateTime`
  - tabular data → `DataTable`, `DataRow`, etc.

#### 2.1.2 Arguments

- Follow the same convention as variables but **add a leading underscore**:
  - Examples: `_strFilePath`, `_dtaInput`, `_dicConfig`
- Use camelCase with type abbreviation: `_<typePrefix>MyArgumentName`.
- Do **not** rely on default argument values for testing:
  - Instead: at the beginning of the workflow, use an `If` → “if _mainArgument Is Nothing” then assign test values.

#### 2.1.3 Constants & configuration

- Constants should generally be stored in:
  - `configuration.xlsx`, or  
  - dictionary/parameter files.
- Use **upper-case with underscores** for keys:
  - Example: `PROCESS_MAIN_FOLDER`, `FRAMEWORK_VERSION`.
- Use `String.Format` for dynamic pattern values.  
  - Example configuration row:
    - `MONTH_REPORT_FILE` → `{0}_{1}_month_report.xlsx`
    - description: `0: yyyyMMdd, 1: strSocietyId`

#### 2.1.4 Admitted abbreviations (type prefixes)

Use these prefixes for variable names:

| Type                      | Prefix  | Example                 |
|---------------------------|---------|-------------------------|
| String                    | `str`   | `strFilePath`          |
| Integer                   | `int`   | `intCounter`           |
| Double                    | `dbl`   | `dblAmount`            |
| Boolean                   | `bool`  | `boolIsValid`          |
| DateTime                  | `dti`   | `dtiExecutionDate`     |
| DataTable                 | `dta`   | `dtaWorktray`          |
| DataRow                   | `drow`  | `drowCurrent`          |
| Dictionary (any)          | `dic`   | `dicConfig`            |
| Exception                 | `exc`   | `excSystemException`   |
| Array (generic)           | `arr`   | `arrFiles`             |
| Array of Int              | `arrint`| `arrintSelectedRows`   |
| Array of Double           | `arrdbl`| `arrdblScores`         |
| List (string or generic)  | `list`  | `listrowsToProcess`    |
| Excel Application Scope   | `eas`   | `easExcel`             |

Arrays: `arr<TypePrefix>` with lower-case `arr`.  
Example: `arrstrEmails`, `arrdtiDates`.

---

## 2.2 Activities (UiPath)

- **Always rename activities** (never keep default names).
- Use **lower-case with underscores**: `verb_object_extraInfo`.
  - Example: `click_login_button`, `log_starting_workflow`.

### 2.2.1 For Each / For Each Row

- Naming pattern:  
  `fe_<iterativeElement>_<action>`  
  Examples:
  - `fe_rows_process_invoices`
  - `fe_files_upload_to_sharepoint`
- The **nested Sequence** inside the loop should reuse the same name (`fe_rows_process_invoices`).

### 2.2.2 Try/Catch

- Use: `tc_<action>` for the Try/Catch container.
- Inner sequences:
  - Try sequence: `try_<action>`
  - Catch sequences: named clearly according to behavior, e.g. `log_exception`, `handle_business_exception`.

### 2.2.3 Send Hotkey

- Naming pattern:  
  `sh_<keys>_<action>`  
  Examples:
  - `sh_ctrl_c_copy`
  - `sh_ctrl_v_paste_value`

### 2.2.4 GUI activities

- Make activity names **descriptive**:
  - Example: `click_submit_button`, `type_email_in_login_field`.
- Extensively use **annotations** to document:
  - Key properties changed (timeout, selectors, simulate click, etc.).
- Ensure selectors are:
  - **Robust** (avoid `idx='N'` where possible),
  - **Relative** instead of absolute (easier maintenance).

### 2.2.5 Invoke Workflow

- Names must start with `inv_`:
  - Examples:
    - `inv_utils_read_config_dict`
    - `inv_p001_build_worktray`
- This clearly distinguishes invoked workflows from default activity names.

### 2.2.6 Sequences

- Use sequences heavily to **group small steps** and keep workflows readable.
- Group logically related actions into named sequences:
  - Example: `seq_login`, `seq_download_reports`, `seq_update_memory`.

---

## 2.3 Workflows

### 2.3.1 Naming convention

Pattern:  
`<AppOrProcess>_<VerbOrAction>_<SpecificItemAndOrAdditionalInfo>`

- **AppOrProcess**
  - Name of app, system, or process code:
    - `sap`, `service_now`, `p001`, `utils`, etc.
- **VerbOrAction**
  - Main purpose (1–2 verbs):
    - `login`, `download`, `upload`, `navigate`, `transform`, etc.
- **SpecificItemAndOrAdditionalInfo**
  - Object/entity or any extra detail:
    - `invoices`, `evaluation_report`, `dta_columns`.

Examples:

- `sap_navigate_to_transaction.xaml`
- `p001_download_invoices.xaml`
- `utils_transform_dta_columns.xaml`
- `p001_main_state_wf_2.0.xaml`

### 2.3.2 Workflow metadata

Every workflow main sequence must have an annotation like:

```text
*** DESCRIPTION ***
- Short description of the purpose and logic.
- Mention main business rules and special conditions.

*** META ***
Created on:    YYYY-MM-DD ; sisua.developer
Modified on:   YYYY-MM-DD ; sisua.developer ; <modification description>
```

This metadata will be used in official documentation (SDD).

---

## 3. Development practices

These practices are especially relevant when using the **UiPath Standalone Framework**.

---

## 3.1 Project structure

Each UiPath project (one framework instance) must follow:

```text
<process_code>_<process_name>  framework    inputs    wfs    .gitignore
    configuration.xlsx
    main.xaml
    main.xaml.json
    project.json
  out    _exceptions    _latest_process_data    _process_logs  process_data```

If the process uses **more than one framework instance**:

```text
<process_code>_<process_name>  <process_code>.1_<subprocess_name>  <process_code>.2_<subprocess_name>  ...
  <process_code>_data```

- Each `<process_code>.<#>_<subprocess_name>\` is a full standalone framework.
- `<process_code>_data\` contains shared files used for communication between subprocesses.

---

### 3.1.1 Input folder (`framework/inputs`)

- Contains **essential**, semi-static files needed for execution, e.g.:
  - Templates (`_worktray_template.xlsx`, email templates, etc.)
  - Recipients list (`_recipients.xlsx`)
  - Rename headers files
  - Dictionaries
- All input files must be referenced in `configuration.xlsx` with **relative paths**:
  - Example: `inputs	emplates\_worktray_template.xlsx`
- `_recipients.xlsx`:
  - Holds email addresses of all people involved in the process.
  - Default columns: execution report, business exception, system exception.
  - Add extra columns for additional reports if needed (e.g. `HES_REPORTS`).

---

### 3.1.2 Process files (`process_data` and `out`)

- **All input, temp and output files** must be processed inside `process_data` and then moved if needed.
- Rules:
  - User-provided files must be **validated**; raise business exceptions if invalid.
  - Copy input file into `process_data` before using.
  - All downloads should be stored in `process_data`.
  - Output files:
    - First generate locally under `process_data`.
    - Then move/export results to `out` or shared locations.

---

### 3.1.3 Workflows folder (`framework/wfs`)

- Only `.xaml` (and rare `.cs`) files inside `wfs`.
- Organize in **representative folders**, for example:
  - By state: `state_1\`, `state_2\`, …
  - By app: `sap\`, `service_now\`, `sharepoint\`, `utils\`, etc.

---

### 3.1.4 `configuration.xlsx`

- Centralizes process parameters: folders, templates, timeouts, flags, etc.
- Benefits:
  - Change execution behavior without editing workflows.
  - Separate DEV/QA/PRD parameters via `env` column.

Typical structure:

| key                   | env | value                                | item_class     | description                                                                                   |
|-----------------------|-----|--------------------------------------|----------------|-----------------------------------------------------------------------------------------------|
| FRAMEWORK_VERSION     |     | `standalone_v4.1.0`                  | string         | Framework version                                                                            |
| PROCESS_NAME_ID       |     | `p001_process_name`                  | string         | Process name                                                                                 |
| PROCESS_MAIN_FOLDER   |     | `C:\DATA\_process_main_folder`       | absolute_path  | Root local folder created at start                                                            |
| WORKTRAY_TEMPLATE     |     | `inputs	emplates\_worktray_template.xlsx` | relative_path | Worktray template                                                                            |
| ENVIRONMENT           |     | `10`                                 | int            | 10=DEV/NP, 20=QA, 30=PRD                                                                      |
| SEND_EMAILS           |     | `TRUE`                               | boolean        | Enable / disable emails (document default in description)                                     |
| PARAMETER_TEST        | 10  | `This is DEV value`                  | string         | Example environment-dependent parameter                                                       |

Guidelines:

- **Keys**: upper-case with underscores.
- **Input files**: always **relative paths**.
- **Temp/output paths**: typically **absolute paths** (using `PROCESS_MAIN_FOLDER`).
- Use `env` to define environment-specific values:
  - For QA (20): process prefers `env=20` row; otherwise `env=10`.
  - For PRD (30): must have dedicated `env=30` row when needed.
- For dynamic parameters with `String.Format`, document the argument meaning:
  - Example: `EXECUTION_REPORT` description → `0: date (yyyyMMdd), 1: client_id`.

---

## 3.2 Workflows development

### 3.2.1 Structure

- Workflows should be **Sequences**, not Flowcharts (unless extremely justified).
- Each workflow must be **independently testable**:
  - Add an `If` at the start:
    - Condition: `_dicConfig Is Nothing` or main argument `Is Nothing`.
    - Then: assign test values.
- At the top of each workflow:
  1. `Log Message`: start log (`--- Starting: <workflow_name> ---`).
  2. `Multiple Assign`: assign config parameters and constants.
- Avoid hardcoded values in activities:
  - Use variables and configuration parameters.
  - Use `Multiple Assign` for grouped initial assignments (max ~12 variables).
- Use sequences and annotations to keep complex logic readable.

### 3.2.2 Arguments

- Prefer **few arguments** per workflow:
  - Typical: 1–4; **maximum: 8**.
- If many parameters are needed, encapsulate them:
  - Dictionary (`_dicConfig`)
  - `DataRow`, `DataTable`, arrays, or input file.
- For testing, **do not** use default values:
  - Use `If _dicConfig Is Nothing Then` → assign test values.
- In `Invoke Workflow`:
  - Always pass **variables**, not functions or hardcoded expressions.
  - Examples to avoid directly in the `Invoke` panel:
    - `strFile.Split(""c).Last`
    - `_dicConfig("INPUT_FILE").ToString`
    - `"process_data\worktray.xlsx"`
    - `Now.AddDays(-3).ToString("dd-MM-yy")`

### 3.2.3 Logging

- **Never** use `Write Line`.  
  Always use `Log Message`.
- Always log at:
  - Start and end of each workflow.
  - Start and end of significant operations (filters, long-running steps, etc.).
- Use appropriate log levels:
  - `Info` – main trace of execution.
  - `Warn` – unusual but not fatal conditions.
  - `Error` – exceptions or critical failures.
- Log messages should be **clear, text + variables**:
  - Good: `String.Format("[{0}/{1}] Processing row {2}. HES: {3}", intCurrentIdx, intRowsToProcess, intRowIdx+1, strHES)`
  - Bad: logging just `strHES` with no context.
- In loops, log iteration progress:
  - If very large loops, log every N rows (e.g. every 100).

### 3.2.4 GUI activities (detailed)

- Prefer **modern** UI activities when possible.
- Always review and adjust:
  - `Timeout` (e.g. larger when first loading a page, otherwise around 3 seconds).
  - `WaitForReady` if needed.
- Use **robust selectors**:
  - Avoid fixed indexes (`idx=`) whenever possible.
  - Prefer relative, stable attributes (e.g. `aaname`, `id`).
- Prefer:
  - `SimulateClick` / `SimulateType` → fastest & most stable.
  - Fallback to `SendWindowMessages`, then **hardware** only if needed.
- Avoid `ContinueOnError=True`:
  - Instead wrap with `Try/Catch` and log exceptions.
- For **login** workflows:
  - Validate login success.
  - If login fails → throw **System Exception**.
  - If credentials invalid/expired → throw **Business Rule Exception**.
- For **logout**:
  - Wrap in `Try/Catch`:
    - In Catch, log the problem and call `utils_close_apps.xaml`.

### 3.2.5 TryCatch usage

- Use Try/Catch to:
  - Protect critical sections (logins, writes, external system calls).
  - Distinguish between:
    - **Business exceptions** (known, rule-based)
    - **System exceptions** (unexpected, technical).
- Catch blocks should:
  - Log with level `Error` or `Warn`.
  - Assign exception objects to `excBusinessException` / `excSystemException`.
  - Let framework error state handle retries and screenshots when applicable.

### 3.2.6 If activities

- Use `If` to implement **simple**, readable conditions:
  - Prefer short conditions in single `If` vs. deeply nested logic where possible.
- For complex logic:
  - Consider breaking into small sequences or separate workflows.
  - Use well-named Boolean variables (`boolIsValidRow`, `boolHasPendingItems`).

### 3.2.7 Iterative activities (ForEach, While)

- **ForEach** or **ForEach Row**:
  - Use for collections and tables.
  - Keep the body short and structured.
  - Log index or key information.
- **While/Do While**:
  - Prefer clear loop exit conditions to avoid infinite loops.
- If complex row-level logic:
  - Consider a dedicated workflow per row and call it from the loop.

### 3.2.8 LINQ functions

- LINQ is powerful but can reduce readability.
- Use LINQ when it:
  - Significantly simplifies filtering or projection.
  - Is still understandable by other developers.
- Avoid long one-line LINQ expressions:
  - Prefer intermediate variables, or comments explaining the logic.

---

## 3.3 Process design

- Always design processes around:
  - **Worktray model** (input data table).
  - **State machine** for main execution flow (as in the standalone framework).
- Clearly define:
  - States and transitions.
  - Which state builds worktray, which processes rows, which sends report, etc.
- Use configuration and memory to:
  - Control slicing of data (daily/monthly/yearly).
  - Avoid reprocessing the same items.

---

## 4 Worktray design

- Worktray is the **central input data structure** for processing.
- Common columns:
  - `processing_date`
  - `item_id`
  - `observation`
  - `outcome_state_1`
  - `outcome_state_2`
- Worktray rules:
  - Each row corresponds to **one unit of work** (contract, invoice, evaluation, etc.).
  - Must be built from input sources and/or memory.
  - Columns should be:
    - Self-descriptive.
    - Sufficient to process the case without additional lookups where possible.

---

## 5 Execution report

- Execution report is generated from the final worktray.
- Typical steps:
  - Transform worktray into a report table (rename columns, add derived info).
  - Save using a template:
    - Path often defined in `configuration.xlsx`: e.g. `EXECUTION_REPORT_TEMPLATE`.
  - Use email body and templates to send the report to stakeholders.

---

## 6 Process memory

- Memory stores **history of processed items** to avoid duplicates and allow incremental processing.
- Config keys:
  - `MEMORY_TYPE` → `daily`, `monthly`, `yearly`, or `standalone`.
  - `MEMORY_KEY_COLUMNS` → columns that uniquely identify an item.
  - `MEMORY_FILES_TO_MERGE` → max historical files to include.
- Workflows involved:
  - `fmw_concat_process_memory.xaml`
  - `fmw_update_process_memory.xaml`
  - `fmw_download_process_memory_orch.xaml`
  - `fmw_upload_process_memory.xaml`
- Pattern:
  - At start: download/concat memory (optional).
  - At end: update and optionally upload memory to Orchestrator Storage Bucket.

---

## 7 Exceptions handling

- Distinguish **business** vs **system** exceptions:
  - Business: violates rule, expected scenario (e.g. invalid input, missing data).
  - System: technical failure (timeout, system unavailable, selector failure).
- For each exception:
  - Log clearly with `Error` level and meaningful message.
  - Save exception screenshot if configured (`EXCEPTION_ROW_FILE_PATH`).
- Framework has an `error_state`:
  - Manages retry logic per state.
  - Decides when to continue, retry, or move to final state.
- Emails:
  - Business exception summary to business stakeholders.
  - System exception alerts to support/developers.

---

## 8 Security

- Store **credentials** in Orchestrator assets or secure stores, never hardcoded.
- Do not log:
  - Passwords, tokens, or sensitive personal data.
- Use:
  - Incognito / private sessions when interacting with web systems.
  - Proper logout and session closure.
- Make sure:
  - Access permissions to shared folders, SharePoint, SQL, etc. are least-privilege.

---

## 9 Process backup and version control

- Use **Git** (or equivalent) for all UiPath projects:
  - Commit early, commit often with clear messages.
  - Use branches for features / fixes.
- Before major changes:
  - Tag versions or create branches to preserve a stable base.
- Keep:
  - `project.json`, `main.xaml`, `configuration.xlsx`, `inputs`, `wfs` in repo.
  - `process_data` and `out` are typically ignored (via `.gitignore`).

---

## 10 Publish a process in Orchestrator

- Before publishing:
  - Clean up test code and comments marked as temporary.
  - Ensure configuration for `ENVIRONMENT` and file paths is correct for target env.
  - Confirm that emails, recipients, and memory settings are correct for QA/PRD.
- Publish:
  - With a clear version number and description.
- After publishing:
  - Configure:
    - Schedules
    - Assets (credentials, parameters)
    - Storage Buckets for memory/worktray if defined.
  - Monitor first runs and logs carefully (hypercare period).

---

*End of SEP1 summary in markdown format.*
