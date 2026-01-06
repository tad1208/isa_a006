# A006 – Supplier Evaluation Process (Evaluación de Proveedores)

> Original document: **“Propuesta de Automatización A006 – EVALUACIÓN DE PROVEEDORES”** (Spanish).  
> This markdown rewrites the process description in English and adds short explanations for key Spanish terms.

---

## 1. Process description (As‑Is)

### 1.1 Objective of the process

The **A006 – Evaluación de Proveedores** (Supplier Evaluation) process aims to systematically evaluate suppliers that provide goods and services to the organisation, ensuring:

- Compliance with contractual conditions and corporate policies defined by the **casa matriz** (corporate HQ).  
- Periodic and traceable assessment of each supplier’s performance in **quality**, **timeliness**, and other criteria such as **safety/risk** and **environmental impact**, when applicable.  
- Proper recording of the final evaluation in **ServiceNow**, so that it can later be registered in **SAP** by the procurement area.

> **Assistant note:** The key idea is that every active contract with a supplier must be periodically reviewed, scored and approved according to corporate rules, and the result must end up recorded in ServiceNow/SAP.

---

### 1.2 Systems involved in the current process

Based on the original table of systems, the current As‑Is process uses at least the following tools:

- **Correo (Email / Outlook)** – Used to:  
  - Receive the report of current contracts (**informe de contratos vigentes**).  
  - Send and receive evaluation forms from evaluators.  
  - Communicate results and approvals.
- **Microsoft Excel (MS365)** – Used to:  
  - Consolidate information about contracts.  
  - Consolidate evaluation results and compute scores.
- **PDF forms** – A standard evaluation form in PDF format is used to collect scores and comments.  
  - The PDF is filled in and signed by the **Administrador técnico del contrato** (technical contract administrator) and, depending on the case, by **Prevención de Riesgos** (occupational risk/safety) and **Medio Ambiente** (environmental area).  
  - In some cases signatures may be handled digitally (for example via **DocuSign**).
- **ServiceNow** – Used to:  
  - Load the final evaluation result for each contract.  
  - Enable subsequent registration in **SAP** by the procurement area (**Abastecimiento**).
- **SAP** – Final back‑office system that uses the evaluation result registered in ServiceNow.

> **Assistant note:** For the To‑Be automation, these systems continue to exist, but data entry and consolidation will be handled mainly by UiPath robots and a Power App instead of manual work in Outlook and Excel.

---

### 1.3 Current workflow – step by step (As‑Is)

The current manual process can be summarised in the following steps:

1. **Reception of the report of active contracts (Informe de contratos vigentes)**  
   The **Abastecimiento** (Procurement) or responsible area sends an Excel report with all active contracts, including:  
   - Supplier, contract ID, description.  
   - Start date and end date (**fecha de inicio / fecha fin de vigencia**).  
   - Other attributes used to determine whether the contract should be evaluated.

2. **Filter contracts that must be evaluated**  
   From the report, the responsible analyst identifies which contracts must be evaluated in the current cycle, according to business rules such as:  
   - Contracts with **less than one year** of duration are evaluated at the end of the contract.  
   - Contracts with **more than one year** are evaluated every time they complete a year in force (annual evaluation).  
   - Some contracts may be excluded according to specific conditions defined by the **casa matriz** (corporate policy).

3. **Send evaluation request to the technical contract administrator**  
   For each contract that must be evaluated, an email is sent to the **Administrador técnico del contrato** (technical contract administrator) containing:  
   - The evaluation form in **PDF**.  
   - Instructions on how to fill in the criteria and scoring.  
   - Deadline and any additional documents (e.g. Code of Ethics).

4. **Evaluation by the technical contract administrator**  
   The administrator fills out the PDF form, assigning scores and comments for:  
   - **Quality** and **timeliness** of the supplier.  
   - Compliance with service levels, incidents, and other agreed aspects.  
   The completed and signed form is then sent back by email.

5. **Evaluation by Prevención de Riesgos (risk/safety) – if applicable**  
   - For contracts that involve operational or safety risk, the **Prevención de Riesgos** area (Occupational Risk Prevention) also evaluates the supplier, either on the same form or on a dedicated section.  
   - They may approve the evaluation or add observations.

6. **Evaluation by Medio Ambiente (environment) – if applicable**  
   - For contracts that have environmental impact, the **Medio Ambiente** (Environment) area performs its own evaluation.  
   - If there is no dedicated environmental evaluator, the same technical administrator might perform this part.

7. **Consolidation of evaluations and final score**  
   - The responsible analyst consolidates the partial evaluations (technical, risk, environment).  
   - Scores from all evaluators are summed or combined into a single **final score** for the supplier/contract (for example, a “Total 1” or “Total 2” score).  
   - The consolidation is usually done in Excel.

8. **Registration in ServiceNow and later in SAP**  
   - The final result (score and conclusion: approved/observed/rejected, etc.) is recorded in **ServiceNow**.  
   - From there, **Abastecimiento** (Procurement) performs the necessary registration or follow‑up in **SAP**.

> **Assistant note:** As‑Is, almost everything is manual: identifying contracts, sending emails, tracking who has answered, copying scores into Excel, calculating totals, and updating ServiceNow. This is error‑prone and difficult to audit.

---

### 1.4 Current operation metrics

From the original **Tabla 1: Métricas actuales de operación**:

- **Estimated number of contracts evaluated each time the process is executed:**  
  Approx. **50** contracts per batch, around **600 per year**.
- **Processing time per execution:** around **15 minutes** of active work (not counting waiting time for responses).  
- **Estimated FTE usage:** around **0.08 FTE** dedicated to this repetitive task.

By freeing the time currently used for this repetitive process, the team can focus on higher‑value activities such as analysing evaluation results and managing contractual/compliance aspects.

> **Assistant note:** Even though the FTE value looks small, the main benefit is standardisation, data quality, traceability and compliance (especially for audits).

---

## 2. Scope of the proposed solution (Alcances)

The proposal defines the scope and boundaries of the automation. In simplified terms, the solution will:

- **Identify which contracts must be evaluated** each period, based on the data in the **contract base** and corporate rules.  
- **Manage the evaluation through an app** (built in **Power Apps**) that allows data entry directly by:  
  - **Administradores de contrato** (contract administrators).  
  - Other responsible evaluators such as **Prevención de Riesgos** (Risk Prevention) and **Medio Ambiente** (Environment).  
- **Send automatic notifications** to contract administrators, their managers (**jefatura**) and other evaluators when an evaluation is required, or when approvals are pending.
- **Consolidate partial scores** and automatically compute:
  - Per‑criteria scores.  
  - Final **Total** values for each contract (e.g. “TOTAL 1”, “TOTAL 2”).  
- **Generate a CSV or Excel file automatically**, ready for bulk loading into **ServiceNow**. This file will be later used by the Procurement area to register the results in **SAP**.
- **Maintain digital traceability** of evaluations, scores, observations and approvals in a structured data source (e.g. SharePoint List).

Important clarifications mentioned in the original document:

- The app will not **instantaneously** update data like a real‑time transactional system. Updates are subject to the availability and schedule of the robot.  
- To have a fully real‑time, transactional app, a different integration method would be needed and would imply additional effort and cost (not included in this scope).

> **Assistant note:** In practice, Power Apps acts as a front‑end for users, while UiPath robots act as a back‑end engine that periodically refreshes data, creates evaluation tasks, and consolidates results.

---

## 3. To‑Be process design

### 3.1 High‑level architecture

The To‑Be process introduces the following main components:

- **Robot 1 – Generación y Activación del Proceso de Evaluación**  
  (Generation and activation of the evaluation process)  
  - Reads the contract report.  
  - Identifies which contracts must be evaluated.  
  - Creates or updates evaluation records in the app’s data source (e.g. SharePoint List).  
  - Sends initial notifications to contract administrators and evaluators.

- **Robot 2 – Evaluación, Consolidación y Carga a ServiceNow**  
  (Evaluation follow‑up, consolidation, and load to ServiceNow)  
  - Monitors the app to detect completed or partially completed evaluations.  
  - Validates that all required phases have been completed.  
  - Applies business rules (Code of Ethics, etc.).  
  - Consolidates scores.  
  - Generates the CSV/Excel file and registers results in ServiceNow.

- **Power Apps application**  
  - Serves as the **front‑end** (user interface) for administrators, evaluators and approvers.  
  - Stores data in a **SharePoint List** (recommended) or, optionally, **Dataverse** (requires more effort and licensing).

- **SharePoint List**  
  - Main structured data store for evaluation requests, scores and statuses.  
  - Power Apps reads/writes here, and robots also read/write here as part of their workflows.

- **External systems** remain the same:  
  - Email/Outlook for notifications.  
  - ServiceNow and SAP for final registration.

> **Assistant note:** In terms of RPA design, Robot 1 acts mostly as a **dispatcher** (builds a worktray of evaluation tasks), and Robot 2 as a **performer** (processes and closes those tasks). This fits well with the Sisua Standalone Framework.

---

### 3.2 Main flows in the To‑Be process

#### 3.2.1 Overall evaluation flow

At a high level, the new flow will:

1. Robot 1 reads the **informe de contratos vigentes** and identifies contracts that must be evaluated.  
2. Robot 1 creates evaluation records in the app’s data source and triggers notifications.  
3. Users (administrators, risk, environment) complete evaluations in the Power App.  
4. Robot 2 periodically reads the app’s data, validates completions, and consolidates scores.  
5. Robot 2 generates a CSV/Excel file and loads data into ServiceNow.  
6. Procurement uses ServiceNow to complete registration in SAP and manage any follow‑up actions.

> **Assistant note:** The diagrams “Ilustración 2_1”, “Ilustración 3_2”, etc., show this end‑to‑end flow separated into specific sub‑flows for each robot and for CSV generation.

---

#### 3.2.2 Ethics and approval management (Gestión Ética y Carta)

The document describes a flow called **“Gestión Ética y Carta”** which covers:

- Collection of confirmation that the supplier complies with the **Código de Ética** (Code of Ethics).  
- Application of business rules related to ethics and compliance.  
- Notification to management (**jefatura**) when there are observations or when an evaluation must be formally approved.  
- Monthly summary of executions in the app and via email (**RESUMEN MENSUAL DE EJECUCIONES EN APP Y CORREO**).

If an evaluation does not comply with the Code of Ethics, or if there are serious observations, Robot 2 must flag the contract and treat it as an exception for manual review.

> **Assistant note:** From the RPA perspective, the Code of Ethics is an extra validation layer on top of the numeric scores. Even if the numeric score is good, failure in ethics rules should block automatic approval and require manual intervention.

---

#### 3.2.3 CSV generation flow

The **“Diagrama generación CSV”** (Illustration 6) represents how Robot 2 will:

1. Take consolidated results from the app’s data source.  
2. Calculate level 1 and level 2 totals (for example, `TOTAL 1`, `TOTAL 2`, “Consolidado 1”, “Consolidado 2”).  
3. Apply adjustments or business rules (e.g. minimum score thresholds).  
4. Generate a **CSV** file in the format required by **ServiceNow/SAP**.  
5. Store the CSV in a designated folder and/or send it by email to Procurement.  
6. Optionally trigger a ServiceNow import job, depending on the integration design.

> **Assistant note:** When we implement Robot 2 in UiPath, we will need a dedicated workflow that maps the internal data model (SharePoint columns) to the exact CSV layout expected by ServiceNow, and handle validation/exception logging row by row.

---

#### 3.2.4 Robot 1 – Generación y Activación del Proceso de Evaluación

**Robot 1** is responsible for setting up each evaluation cycle. Its main functions are:

- **Read the contract report**  
  - Input: Excel file sent by Procurement (**informe de contratos vigentes**).  
  - Robot validates the structure and content of the file.

- **Identify contracts to evaluate**  
  - Based on rules defined in the contract base: duration, type of service, risk level, etc.  
  - Contracts that meet the criteria are marked as **“to be evaluated”** in the internal worktray.

- **Create evaluation requests**  
  - For each contract to evaluate, Robot 1 creates or updates a record in the SharePoint List that backs the Power App.  
  - The record includes information such as: contract ID, supplier, responsible administrator, type of evaluation required (with or without risk/environment), and due dates.

- **Notify stakeholders**  
  - Robot 1 sends automatic emails to:  
    - **Administrador técnico del contrato** – with a link to the Power App.  
    - Additional evaluators (e.g. **Prevención de Riesgos**, **Medio Ambiente**) when applicable.  
  - The email includes instructions and deadlines.

- **Log and traceability**  
  - Robot 1 registers its actions in log files (who was notified, which contracts were created, etc.).  
  - If there are errors in the input file or missing data, these are registered as **business exceptions** for manual correction.

> **Assistant note:** In the Sisua framework, most of this logic belongs to the dispatcher state(s): building the worktray from input files, creating app records and sending initial notifications.

---

#### 3.2.5 Robot 2 – Follow‑up, consolidation and ServiceNow load

**Robot 2** takes over once evaluations have been created and users start interacting with the app. Its main responsibilities are:

- **Monitor evaluation status in the app**  
  - Periodically reads the SharePoint List to detect:  
    - Evaluations completed by the technical administrator.  
    - Pending evaluations.  
    - Evaluations where risk or environment sections are still incomplete.

- **Validate phases and business rules**  
  Robot 2 must:  
  - Verify that all **required phases** have been completed:  
    - Technical evaluation.  
    - Risk/Safety (**Prevención de Riesgos**) evaluation (if required).  
    - Environmental (**Medio Ambiente**) evaluation (if required).  
    - Ethics/Code of Ethics confirmation.  
  - If any phase is missing, Robot 2:  
    - Marks the request as **incomplete**.  
    - Generates a notification or a business exception record for manual follow‑up.

- **Consolidate results**  
  - Combine partial scores into a final score per contract (TOTAL 1, TOTAL 2, consolidated values, etc.).  
  - Identify whether the contract is **approved**, **observed**, or requires further review.

- **Generate CSV/Excel for ServiceNow**  
  - Build a structured table with the fields required by ServiceNow (contract ID, supplier, final score, conclusion, dates, etc.).  
  - Save the file in the agreed location (e.g. `process_data` / `out` folders in the Sisua framework).

- **Register results / trigger load to ServiceNow**  
  - Depending on the integration method, Robot 2 can:  
    - Upload the CSV to a folder where ServiceNow will import it.  
    - Or directly create/update records in ServiceNow via UI/API automation.

- **Exception handling**  
  - Detect requests with incomplete phases and notify them as **exceptions in the load** for manual correction.  
  - Log system errors separately (timeouts, connectivity errors, etc.).

> **Assistant note:** Robot 2 behaves like a performer that cycles through pending items, validates them, and either completes them (load to ServiceNow) or flags them as exceptions. This maps nicely to a state like `process_worktray_items` in the framework.

---

### 3.3 Integrated application / form (Power Apps)

The process is supported by a **Power Apps** application that acts as a unified interface for evaluations.

Key points from the document:

- The app uses a **SharePoint List** as its main data source (recommended option).  
  - This keeps infrastructure simple and cost‑effective.  
  - If more advanced storage is required, **Dataverse** can be considered, but it implies higher effort and licensing cost.

- The app contains at least the following screens (see mockups in the original document):  
  - **Login screen** – Users authenticate, typically through corporate credentials / Azure AD.  
  - **Main screen / dashboard** – Shows the list of contracts assigned to the logged‑in user for evaluation.  
  - **Evaluation by contract screen** – Allows entry of scores, comments, ethics confirmation and sending to approval.

- The app can show **conditional sections** depending on the type of evaluation:  
  - If the contract requires environmental evaluation, the Medio Ambiente section is shown.  
  - If not, it is hidden or marked as not applicable.

- Security and access control are managed through:  
  - **Azure AD security groups**.  
  - Optional profile or permission control at SharePoint List level.

> **Assistant note:** For development, it is important that the column names in the SharePoint List are stable and well documented, since Robot 1 and Robot 2 will rely on them to read/write data. The Power App should be designed to use those same columns without renaming them later.

---

## 4. Business rule exceptions (Excepciones de Reglas de Negocio)

The original document describes a section **4.1 Excepciones de Reglas de Negocio**. Typical examples include:

- Missing or inconsistent data in the **informe de contratos vigentes** (missing identifiers, wrong dates, etc.).  
- Evaluations where one or more mandatory sections (technical, risk, environment, ethics) have not been completed.  
- Conflicts between the evaluation result and the contract data (for example, trying to evaluate a contract that is no longer active).  
- Ethics or compliance issues that prevent automatic approval.

When a business rule exception occurs, the robots must:

- Register the case in an exceptions log (e.g. an Excel file or specific SharePoint List).  
- Optionally notify responsible stakeholders by email.  
- Avoid loading incomplete/invalid data into ServiceNow.

> **Assistant note:** In the Sisua framework, these would usually be **Business Exceptions** handled at state level, with information captured in the worktray and in the execution report.

---

## 5. Non‑functional aspects and requirements (high level)

Although not all details are reproduced here, the original document also mentions:

- **Licensing requirements**  
  - Power Apps user licences.  
  - Access to SharePoint Online.  
  - UiPath Orchestrator and robot licences.

- **Infrastructure and access**  
  - Connectivity from robots to **ServiceNow**, **SharePoint Online**, **Outlook**, and corporate file shares.  
  - Configuration of Orchestrator API and access tokens when using API‑based integration.

- **Security**  
  - Use of Azure AD security groups to control access to the app and to SharePoint Lists.  
  - Proper handling of credentials and sensitive information by robots.

- **Project timeline and effort**  
  - Estimated duration of the full project is mentioned as **7–9 weeks**, from reception of all inputs to deployment in production.

> **Assistant note:** From a development perspective, these requirements impact how we configure the UiPath environments (DEV/QA/PRD), asset management, and Power Apps environments and connections.

---

## 6. Summary for development

For the purpose of implementing this process using the **Chile framework** and UiPath:

- Treat **Robot 1** as the **dispatcher** that:  
  - Reads contract input files.  
  - Builds the worktray of evaluation tasks.  
  - Creates app records and sends notifications.

- Treat **Robot 2** as the **performer** that:  
  - Reads evaluation progress from the app (SharePoint List).  
  - Validates phases and ethics rules.  
  - Consolidates scores.  
  - Generates the CSV/Excel file and updates ServiceNow.

- Use **Power Apps + SharePoint List** as the structured data entry and storage mechanism for users.

- Clearly document:  
  - SharePoint List columns.  
  - Business rules for when an evaluation is required.  
  - Mapping from internal data to ServiceNow CSV layout.  
  - Definitions of business exceptions vs system exceptions.

This markdown file can now be used as the English functional description of the A006 process, serving as a basis for designing the UiPath workflows and the Power App screens.
