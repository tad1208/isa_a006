# ISA A006 – Provider Evaluation App  
Screen specification (draft)

> Note: Visible labels from mockups stay in **Spanish** with a short English hint.

---

## 0. Common layout

All screens share the same top header.

### 0.1. App header

- **Logo area (left)**
  - Static ISA / INTERVIAL logo.

- **Navigation buttons (right)**
  - **Evaluar Proveedores** – *Evaluate providers (evaluator view)*  
    Navigates to “Provider Evaluation” screen.
  - **Aprobar evaluaciones** – *Approve evaluations (approver view)*  
    Navigates to “Approver” screen.
  - **Firmar cartas** – *Sign letters*  
    Navigates to “Sign Letters” screen.
  - **Dashboard? (Solo admin)** – *Dashboard (admin only)*  
    Placeholder for an admin-only view (not defined in mockups).

- The active section is highlighted (green in mockups).

---

## 1. Landing screen

> File: `1_landing.jpg`

### 1.1. Purpose

Entry point of the app.  
Uses evaluation data + `User()` to decide what the current user can see/do.

### 1.2. Layout

- **Header**  
  - Reuses the *Common layout* header.
- **Body**
  - Initially empty or minimal, just a message panel.

### 1.3. Main components

- **Info message**
  - Text example:
    - “Welcome, you have **X pending evaluations** to complete.”
  - `X` is calculated from evaluation data filtered by current `User()`.

### 1.4. Behaviour

- When user opens the app:
  - Show the header.
  - Show a dynamic summary based on:
    - Count of pending evaluations as evaluator.
    - Count of pending approvals as approver (optional additional text).
- No forms or filters here – only navigation and information.

---

## 2. Provider Evaluation screen – Evaluator

> Files: `2.1_provider_evaluation.jpg` + `2.2_provider_evaluation.jpg`  
> This section describes a **single screen** whose content is conditionally shown based on the current user’s role and evaluation type.

### 2.1. Purpose

Allow the **Administrador de contrato / Evaluador** to complete the evaluation form for a provider, including general criteria, SST (Seguridad y Salud en el Trabajo – *Safety & Health*) and potentially Medio Ambiente (*Environment*).

### 2.2. Layout

- **Header**
  - Common app header, with **Evaluar Proveedores** active.
- **Filter / selection area (left)**
  - **Dropdown – “select evaluation”**
    - Items:  
      - `(Date) Provider name`  
      - `(Date) Provider name`  
      - `…`
    - Data source: list of **pending evaluations** for current user, including contract/provider + date.
- **Evaluation form area (center)**  
  - Hidden or disabled until an evaluation is selected.
  - When no selection:
    - Show message like:  
      *“Select an evaluation to continue”*.
  - When an evaluation is selected:
    - **Header block**
      - General info:
        - Provider name
        - Contract ID / description
        - Period / dates
        - Evaluation type and responsible roles
    - **Form block**
      - Organized in sections:
        - **GENERAL SECTION**  
          - Multiple **General criteria** (Criterion 1, Criterion 2, …)
          - Each criterion has one or more **Subcriteria**.
        - **SST SECTION** (if applicable)  
          - Multiple **SST criteria** and **Subcriteria**.
        - (Optionally) **Medio ambiente SECTION** if required by flags.
      - Each **Subcriterion row** contains:
        - Text **description**:
          - “Description of criterion met”
          - “Description of criterion partially met”
          - “Description of criterion not met”
        - **Score inputs**:
          - 3 radio buttons (example: 0 / 1 / 2 or similar scale).
          - One calculated **Item score** per subcriterion.
      - **Overall score area**
        - Subtotal per section (General, SST, Environment).
        - Global total score.
      - **Buttons**
        - **Submit** – save evaluation and mark it as completed for this role.
        - **Reset** – clear current form inputs for this evaluation.

### 2.3. Behaviour / Logic

- **Form visibility & enablement**
  - Form is **hidden** or **disabled** when no evaluation is selected.
  - Once an evaluation is selected, form becomes visible and enabled according to the user role.

- **Sections by role (from `2.2_provider_evaluation`)**
  - Logic examples based on user & flags:
    - If **admin user and no SST** responsible:
      - Display **General section** only.
    - If **admin user and SST responsible**:
      - Admin completes **General section**.  
      - SST completes **SST section**.
    - If **SST user** is responsible:
      - Display **SST section** only (or SST section enabled, rest read-only).
  - There is a **boolean field** (e.g. `RequiresEnvironmentEvaluation`) to flag if “Medio Ambiente” must also evaluate.
  - There is a **boolean field** (e.g. `EthicalCodeNotRespected`) to mark if provider does **not** follow ethical code (used later by Robot 2).

- **Alternative UX option (mentioned in mockup)**
  - Entire form visible, but sections the current user should not fill are **disabled**, while still showing the **overall score**.

- **Input validation**
  - All required subcriteria must be scored before allowing **Submit**.
  - If any mandatory section is incomplete, show error and keep form open.

---

## 3. Approver screen

> File: `3_approver.jpg`

### 3.1. Purpose

Allow a **Jefe de área / Approver** to review a completed evaluation, see all scores, write observations, and **Approve / Reject**.

### 3.2. Layout

- **Header**
  - Common header with **Aprobar evaluaciones** active.
- **Header block (form header)**
  - Same general information as the evaluator form:
    - Provider name
    - Contract ID / description
    - Period / dates
    - Overall evaluation summary.

- **Form block**
  - Visual layout similar to evaluator form:
    - **Criteria / Subcriteria table** with descriptions and item scores.
  - **All scoring inputs are disabled (read-only)**.
    - Only display the results, no editing.

- **Bottom block**
  - **OVERALL SCORE (left)**
    - Read-only total score.
  - **OBSERVATIONS (right)**
    - **Multiline text area** for approver comments (only enabled input in the form).
  - **Action buttons (bottom center)**
    - **Approve** – confirms the evaluation as approved.
    - **Reject** – rejects the evaluation, keeping or sending it back to previous step, and storing observations.

### 3.3. Behaviour / Logic

- Approver selects pending evaluation (either from same dropdown pattern as evaluator or from a specific **approvals list**, not fully defined in mockups).
- When an evaluation is loaded:
  - Display all sections and scores **disabled**.
  - Enable **Observations** field and **Approve / Reject** buttons.
- **Approve action**
  - Saves approver decision + observations.
  - Triggers Robot 2 logic to continue with CSV generation and letter creation.
- **Reject action**
  - Saves rejection decision + observations.
  - Notifies relevant users (administrator/evaluator) via email / status (handled by Robot 2).

---

## 4. Sign Letters screen

> File: `4_sign.jpg`

### 4.1. Purpose

Enable users responsible for signing to view evaluation letters and provide their **digital signature** (two signatures per letter if required).

### 4.2. Layout

- **Header**
  - Common header with **Firmar cartas** active.
- **Selection area (top-left)**
  - **Dropdown – “select letter/evaluation”**
    - Items:
      - `(Date) Provider name`
      - `(Date) Provider name`
      - `…`
    - Data source: list of **letters pending signature** for current user.
- **Letter display area (center)**
  - Large panel to:
    - Either **display the letter preview** (PDF or HTML viewer)  
    - Or show a **link to open the letter** in an external viewer.
- **Signature area (bottom)**
  - **Option A – Pen input**
    - Canvas to **draw signature with mouse / touch**.
  - **Option B – Upload image**
    - File upload control to **attach an image of the user’s signature**.

### 4.3. Behaviour / Logic

- Only show letters to **relevant users** according to database / evaluation data.
  - E.g. `Signer 1`, `Signer 2` defined for each letter.
- When user selects a letter:
  - Load & display the letter (preview or link).
  - Enable signature controls.
- Signature capture and integration:
  - User either:
    - Draws signature; or  
    - Uploads signature image.
  - App stores signature data (image / link) for Robot 2.
  - **UiPath Robot** may handle:
    - Inserting signature in the correct place in the PDF.
    - Managing both signatures when two signers are required.
- After signature is successfully registered:
  - Mark letter as **signed by this user**.
  - When all required signatures are completed, mark letter as **fully signed** and finalize process for that evaluation.

---

## 5. (Optional) Dashboard screen – Admin only

> Label in header: **Dashboard? (Solo admin)** – not detailed in mockups.

High-level idea (to be refined):

- KPIs such as:
  - Number of evaluations due / completed.
  - Number of evaluations with **ethical code breach**.
  - Average scores per provider, per period.
- Filters by date, provider, contract, evaluator.

---
