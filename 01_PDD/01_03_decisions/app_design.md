# Power App – Design Specification (ISA Evaluaciones)

> Based on **Design.pdf** (mockups & guidelines). :contentReference[oaicite:0]{index=0}  

---

## 1. Global Design Concept

- The app visual style is inspired by the client’s website **“Inicio – ISA Vías”**.
- Main color palette:
  - Primary blue: **#003087**
  - Accent blue (hover): **#009AFE**
  - Accent orange: **#FF6A00**
  - Neutral gray (negative buttons): **#767676**
- The look & feel is clean, with lots of white space, rounded buttons and clear separation between sections.

---

## 2. Header (App-Level Navigation)

Appears on **all screens** (as seen in header mockups on page 1).

- **Layout**
  - Left: ISA logo (white background).
  - Right: navigation tabs / buttons.
- **Background color:** `#003087`
- **Font color:** `#FFFFFF`
- **Navigation buttons**
  - Example labels in design:
    - `Dashboard` (solo para admin del cliente)
    - `Evaluaciones`
  - In the real app, tabs must be replaced by actual navigation options.
  - Style: pill-like rounded rectangles, white text on blue background.

---

## 3. Typography

### 3.1 Titles & Section Labels

Used for big titles like “Evaluaciones”, “Calidad”, “Medioambiente”, “Resumen”, etc. (see pages 1, 4, 9, 12).

- **Font:** `AmsiPro, sans-serif` (or a visually similar font if not available).
- **Color:** `#003087`
- Typically large size, bold.

### 3.2 Body Text

Used for descriptions, question text, help text, etc.

- **Font:** `Roboto` (or similar).
- **Color:** `#003087`
- Regular weight, smaller size than titles.

---

## 4. Buttons

### 4.1 Primary Action Buttons

For positive actions such as:

- Submitting evaluation: **Enviar**
- Approving evaluation
- Confirming actions

**Visual (see blue “Visitar” and “Enviar” buttons in pages 2 & 12):**

- Background: `#003087`
- Font color: `#FFFFFF`
- Right-side decoration: small circular or pill accent with **#FF6A00** (contains an icon like ➕ or ✔).
- On **hover**:
  - Background: `#009AFE`
  - Font color: `#FFFFFF`

### 4.2 Negative / Secondary Action Buttons

For actions like:

- Cancel
- Reject evaluation
- Reset form: **Limpiar**

**Visual (see gray button “Visitar / Limpiar” on pages 2 & 12):**

- Background: `#767676`
- Font color: `#FFFFFF`
- Right-side accent decoration: `#FF6A00`
- Behavior on hover follows same logic as positive buttons but with gray base.

---

## 5. Evaluations Form – Overall Structure

The Power App form is a digital version of **“Anexo 1_ Formato_Evaluacion Continua_(Mod subcriterios borrador).xlsx”**, especially section **4. EVALUACIÓN DEL DESEMPEÑO**.  

Form has three big areas:

1. **Header** – contract & evaluation meta-data.
2. **Questions list** – multiple-choice evaluation criteria.
3. **Summary** – subtotal, total score and ethical code result.

---

## 6. Evaluation Header

On page 3 there is a table specifying fields and sources plus a mockup showing layout.

### 6.1 Data Shown (Read-only)

Each evaluation form header must display:

| Label (Spanish)                                        | Source (SharePoint list `ongoingEvaluations`) |
| ------------------------------------------------------ | --------------------------------------------- |
| **Fecha de evaluación** (current evaluation date)      | Calculated by app (`Today()`)                 |
| **Sociedad**                                           | Column `sociedad`                             |
| **Nombre proveedor**                                   | Column `nombre_proveedor`                     |
| **Documento de compra / N° contrato**                  | Column `documento_compras`                    |
| **Fecha inicio de validez**                            | Column `fecha_inicio_validez`                 |
| **Fecha fin de validez**                               | Column `fecha_fin_validez`                    |
| **Valor del contrato**                                 | Column `valor_contrato`                       |
| **Contador de las últimas evaluaciones…** (to confirm) | Likely `consecutivo_evaluacion`               |
| **Consecutivo evaluación**                             | Column `consecutivo_evaluacion`               |
| **Nombre evaluador (Responsable técnico)**             | Column `nombre_responsable_tecnico`           |

- **All fields are read-only in the app** (no editing by user).
- Fonts:
  - Column titles usually in `AmsiPro` or bold `Roboto`, color `#003087`.
  - Values in `Roboto`, color `#003087`.

### 6.2 Layout

From the header mockup (page 4):

- Top-left: Large title with contract number, e.g.  
  **“Contrato N° (Documento de compra/N° contrato)”** – AmsiPro, blue.
- Below: a two/three-column layout with labeled fields:
  - Left column: `Nombre proveedor`, `Sociedad`, `Valor total`, etc.
  - Right column: `Fecha inicio validez`, `Fecha fin validez`, `Consecutivo evaluación`, etc.

---

## 7. Questions Section

### 7.1 Structure

Based on pages 4–8:

- Sections:
  - **Calidad** – 5 questions
  - **Oportunidad** – 1 question
  - **Gestión** – 2 questions
  - **Requisitos en Salud Ocupacional (SST)** – 1 question
  - **Requisitos Ambientales (MA)** – 1 question + **extra Yes/No gating question**
  - **Código ética** – 1 checkbox-type question
- Each question = a **block** with:
  - Section title (`Calidad`, `Oportunidad`, etc.) – big AmsiPro, blue.
  - Question text (“Calidad de los bienes…”) – bold `Roboto`.
  - 2 or 3 possible answers (1 row each) – clickable areas.
  - At the right side: “puntos” indicator showing score for selected answer (or “-” if none).

### 7.2 Permissions and Visibility

- Questions not relevant for a specific user:
  - Must be **hidden and/or disabled**.
- During **review**:
  - All questions read-only.
  - Only **observations textbox** and **Accept/Reject buttons** are enabled.
- SST question:
  - Answered by `email_responsable_tecnico` **only if** `responsable_sst_responde = False`.
  - If `responsable_sst_responde = True`, SST question must be answered by `email_responsable_sst`.
- MA question:
  - There is a **Yes/No gate**:
    - This Yes/No question is answered by `email_responsable_tecnico`.
    - If **Yes** → main MA question **must be answered by MA responsible** (`email_responsable_ma`) and hidden/disabled for técnico.
    - If **No** → main MA question is answered by `email_responsable_tecnico` and not shown to MA user.

### 7.3 Options & Scoring Rules

- All answers are ordered **highest score → lowest**.
- Types:
  - Most questions: **3 answers**:
    - 1st: 100% of question points (achieved).
    - 2nd: 50% (partially achieved).
    - 3rd: 0% (not achieved).
  - `Gestión` first question: **2 answers** (100% or 0%).
  - `Código ética`: simple checkbox; no inherent points but can nullify total score.
- Section weights:
  - **Calidad**: 5 questions × 6 pts = **30 pts total**.
  - **Oportunidad**: 1 question × 30 pts = **30 pts**.
  - **Gestión**: 2 questions → 10 pts + 10 pts = **20 pts**.
  - **SST**: 1 question × 10 pts = **10 pts**.
  - **MA**: 1 question × 10 pts = **10 pts**.
  - **Total maximum score** = **100 pts**.
- Ethical code behavior:
  - **Subtotal** = sum of all question scores.
  - **Total** =
    - `Subtotal` if Código de ética = **No** (no breach).
    - `0` if Código de ética = **Sí** (breach of ethics).

---

## 8. Question Visual States

There are three main visual states illustrated across pages 6–8.

### 8.1 Unanswered Question

(Top example on page 6)

- All answer rows appear with:
  - Light blue/very pale background.
  - Blue text (`#003087`).
- Displayed score at right side = `"-"` (dash).
- Pale gray horizontal line separating questions in the same section.

### 8.2 Hover State

(Page 7 – middle mockup)

- When mouse hovers an answer row:
  - Background: **`#009AFE`** (bright blue).
  - Font color: **`#FFFFFF`**.
  - This applies to the whole row area, making it clearly clickable.

### 8.3 Selected Answer

(Page 8 – final mockup)

- When user clicks an answer:
  - Background: **`#003087`**.
  - Font color: **`#FFFFFF`**.
- At the right:
  - Score label updates to show numeric value (e.g., **“6.0 puntos”**).
  - It uses large `Roboto` numbers, colored `#003087` or white depending on mockup; annotation indicates score dynamically changes.
- If user selects a different answer:
  - Previously selected answer row returns to its **unselected** style.

### 8.4 Section Separators

- A solid horizontal rule in **#003087** is used between sections (e.g., between `Calidad` and `Oportunidad`, visible on pages 6 and 9).

---

## 9. Medioambiente – Yes/No Gating Question

Shown on page 9.

- Section title: **“Medioambiente”**.
- Prompt text: **“Responsable de criterio de medioambiente debe responder:”**  
  Followed by two buttons: **Sí / No**.
- Initial state:
  - Only técnico sees this question.
  - `Sí` / `No` styled similarly to other choices (blue/white toggles).
- Behavior:
  - If técnico selects **No**:
    - Main MA question is shown and enabled for técnico.
    - For MA user, gating question is disabled/hidden.
  - If técnico selects **Sí**:
    - Main MA question is **hidden/disabled** for técnico.
    - Main MA question is displayed and enabled for `email_responsable_ma`.

---

## 10. Código de Ética Question

Page 10 shows “Código de Ética” block.

- Title: **“Código de Ética”** (big blue AmsiPro).
- Statement: **“El contratista incurrió en faltas al código de ética”**.
- Answer type:
  - Simple **Yes / No** toggle (similar style to MA gate).
- Permissions:
  - Only the **responsable técnico** can answer.
  - Field is **disabled** for all other users.
- Impact on scores:
  - If **Yes** → **Total = 0**, Subtotal unchanged.
  - If **No** → Total = Subtotal.

---

## 11. Observation Text Areas

Pages 10–11 show “Observaciones” section.

- There are **4 text areas**:
  1. Observaciones Responsable Técnico
  2. Observaciones Responsable SST
  3. Observaciones Responsable MA
  4. Observaciones Revisor
- Each is a large rounded rectangle textarea:
  - White background.
  - Thin blue border.
  - Placeholder/title above each box.
- Rules:
  - **Max 500 characters** per textarea.
  - All **visible** to all involved users.
  - Only the textarea that matches the current user’s role is **editable**; others read-only.
- A gray horizontal line separates groups of text areas.

---

## 12. Summary Screen (Resumen)

Final mockup on page 12.

### 12.1 Layout

- Top big title: **“Resumen”**.
- Below, a horizontal line of per-section summary:

  | Section         | Example display       |
  | --------------- | --------------------- |
  | Calidad         | `18 / 30`             |
  | Oportunidad     | `0 / 30`              |
  | Gestión         | `20 / 20`             |
  | SST             | `- / 10` (if pending) |
  | Medioambiente   | `10 / 10`             |
  | Código de ética | `No`                  |

- Then:
  - **Subtotal** (e.g., `48 / 100`).
  - Big **Total** shown prominently (e.g., `48 / 100` in large AmsiPro font).

### 12.2 Actions

Two main buttons:

1. **Enviar** (primary – blue)
   - Validates that **all questions assigned to current user** are answered.
   - If validation passes → opens **Confirmation Modal** (see section 12.3).
   - Only after user confirms in modal → updates the record in `ongoingEvaluations`.
   - Optionally: button is disabled until all required answers exist.

2. **Limpiar** (secondary – gray)
   - Resets answers **only for questions assigned to current user**.
   - Answers from other evaluators **must stay unchanged**.

Summary layout may be implemented either as:
- A table, or
- A set of labels – design is flexible, but values must match described behavior.

### 12.3 Submit Confirmation Modal

**Trigger**: When user clicks **"Enviar"** button and validation passes.

#### 12.3.1 Modal Layout

- **Background overlay**: Semi-transparent dark (`rgba(0,0,0,0.5)`) covering the full screen.
- **Modal container**:
  - Centered white rounded rectangle.
  - Max width: 600px.
  - Shadow for depth.
  - Top-right corner: "✕" close button.

#### 12.3.2 Modal Content

**Title**: 
- Text: **"Confirmar Envío de Evaluación"**
- Font: `AmsiPro`, color `#003087`, bold, large size.
- Optional icon: ✅ or ℹ️ before title.

**Subtitle/Description**:
- Text: **"Por favor, revise el resumen de su evaluación antes de enviar:"**
- Font: `Roboto`, color `#003087`, regular weight.

**Evaluation Summary Table**:

Display all evaluation data in a clean table format:

| Campo | Valor |
|-------|-------|
| **Proveedor** | `{nombre_proveedor}` |
| **Documento** | `{documento_compras}` |
| **Fecha Evaluación** | `{fecha_evaluacion}` |
| **Su Rol** | `{Técnico / SST / MA}` |

**Scores Summary**:

| Criterio | Puntaje Obtenido | Puntaje Máximo |
|----------|------------------|----------------|
| Calidad | `{score_calidad}` | 30 |
| Oportunidad | `{score_oportunidad}` | 30 |
| Gestión | `{score_gestion}` | 20 |
| SST | `{score_sst}` | 10 |
| Medioambiente | `{score_ma}` | 10 |
| **Subtotal** | **`{subtotal}`** | **100** |
| Código de Ética | `{Sí / No}` | - |
| **TOTAL** | **`{total}`** | **100** |

**Visual styling**:
- Table rows alternating background (`#f2f2f2` / white).
- Borders: `1px solid #ddd`.
- Total row highlighted with bold font or colored background.
- If Código de Ética = "Sí" → show warning message in red:
  - ⚠️ **"Atención: Código de ética incumplido - Puntaje total = 0"**

**Observations Summary** (if any):

If user has entered observations, display them:

```
📝 Observaciones (Su Rol):
[Observation text entered by user]
```

#### 12.3.3 Modal Actions

Two buttons at bottom of modal:

1. **"Confirmar y Enviar"** (Primary button - blue `#003087`)
   - On click:
     - Close modal.
     - Submit evaluation data to SharePoint.
     - Set appropriate `submitted` flag to `true`:
       - `tecnico_submitted = true` if user is Técnico.
       - `sst_submitted = true` if user is SST.
       - `ma_submitted = true` if user is MA.
     - Show success message/notification.
     - Optionally: navigate to dashboard or refresh evaluation list.

2. **"Cancelar"** (Secondary button - gray `#767676`)
   - On click:
     - Close modal.
     - Return to evaluation form.
     - No data saved.

#### 12.3.4 Modal Behavior

**Open animation**:
- Fade in overlay (0.2s).
- Scale in modal from 90% to 100% (0.3s).
- Smooth easing.

**Close triggers**:
- Click "✕" button.
- Click "Cancelar" button.
- Click outside modal (on overlay).
- Press ESC key.

**Close animation**:
- Reverse of open animation.

**Focus management**:
- When modal opens, trap focus inside modal.
- First focusable element: "Confirmar y Enviar" button.
- Tab navigation cycles only within modal.

#### 12.3.5 Power Apps Implementation Notes

**Modal visibility control**:
```powerfx
// Variable to control modal visibility
Set(varShowSubmitModal, false);

// On "Enviar" button click
If(
    // Validation check
    IsBlank(varErrorMessage),
    Set(varShowSubmitModal, true),  // Show modal
    Notify(varErrorMessage, NotificationType.Error)
)
```

**Modal container**:
- Use a **Container** or **Group** control.
- Position: absolute, centered.
- Visible property: `varShowSubmitModal`.

**Background overlay**:
- Use a **Rectangle** control covering full screen.
- Fill: `RGBA(0, 0, 0, 0.5)`.
- ZIndex: high value.
- OnSelect: `Set(varShowSubmitModal, false)`.

**Data binding**:
- All values in summary table should be bound to current evaluation variables.
- Scores calculated dynamically from selected answers.

---

## 13. Behavior Summary

- All interactive elements follow the same color behavior:
  - **Normal:** blue text on light background.
  - **Hover:** `#009AFE` background, white text.
  - **Selected:** `#003087` background, white text.
- Role-based enable/disable:
  - Each user only edits their own questions and observation box.
  - Reviewer sees questions read-only and only can add review observations + approve/reject.
- Ethical code override:
  - Total score calculation always checks Código de Ética.  
  - This must be implemented at formula or logic level whenever total is recalculated.

---
