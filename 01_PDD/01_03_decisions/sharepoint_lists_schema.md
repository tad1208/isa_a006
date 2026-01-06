# SharePoint Lists Schema - A006 Provider Evaluation

## Tổng quan (Overview)

Hệ thống sử dụng **1 SharePoint List chính** cho evaluation workflow:

**List Name**: `ongoingEvaluations` (hoặc tên tương tự trong SharePoint)

**Workflow stages**:
1. Draft → Evaluation in progress
2. Submitted → Pending approval
3. Approved → Create CSV and Carta (Robot 2)
4. Signed → Complete and archive

---

## Cấu trúc SharePoint List (Complete Schema)

### 1. Basic Identification

| Column Name | Source (External Table/System Column) | SharePoint Type | Required | Description |
|------------|--------------------------------------|-----------------|----------|-------------|
| `Title` | Built-in | String | Yes | Item ID in worktray |
| `documento_compras` | MEIL - CONTRATOS (column 'Documento Compras') | String | Yes | Contract ID |
| `fecha_evaluacion` | Calculated from validity period and service type | Date | Yes | Evaluation date (primary key with documento_compras) |

**Primary Key Logic**:
```
documento_compras + "_" + Text(fecha_evaluacion, "yyyyMMdd")
```
Example: `C12345_20251203`

---

### 2. Contract Information (From MEIL - CONTRATOS Table)

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `fecha_inicio_validez` | MEIL - CONTRATOS (column 'In.periodo validez') | Date | Start of validity period |
| `fecha_fin_validez` | MEIL - CONTRATOS (column 'Fin.periodo validez') | Date | End of validity period |
| `sociedad` | ZMLISTPED - LISTADO PED (column 'Sociedad') | String | Company code |
| `nombre_proveedor` | ZMLISTPED - LISTADO PED (column 'Nombre Proveedor') | String | Supplier name |
| `objeto_contrato` | ZMLISTPED - LISTADO PED (column 'Obj.Contrato') | String | Contract objective |
| `valor_contrato` | MEIL - CONTRATOS (concatenation of 'Val.contrato' (Div.cab.) and 'M.Contrato') | String | Contract value |
| `consecutivo_evaluacion` | ZMRVALP - EVALUACIÓN DE PROV (column 'Cons.Eval.Contrato') | String | Sequential evaluation number |

---

### 3. Responsible Persons & Assignments

#### 3.1. Técnico (Technical Responsible)

| Column Name | Source (External Table/System Column) | SharePoint Type | Required | Description |
|------------|--------------------------------------|-----------------|----------|-------------|
| `nombre_responsable_tecnico` | Maestro colaboradores (concatenation of 'Nombres', 'Apellido Paterno', 'Apellido Materno') | String | Yes | Técnico evaluator name |
| `email_responsable_tecnico` | Maestro colaboradores (column 'Mail corporativo') | String | Yes | Técnico email |

#### 3.2. SST (Safety & Health)

| Column Name | Source (External Table/System Column) | SharePoint Type | Required | Description |
|------------|--------------------------------------|-----------------|----------|-------------|
| `nombre_responsable_sst` | Tabla evaluadores criterio de SST (column 'nombre_evaluador_criterio_sst') | String | No | SST evaluator name |
| `email_responsable_sst` | Tabla evaluadores criterio de SST (column 'correo') | String | No | SST evaluator email |

#### 3.3. MA (Environment)

| Column Name | Source (External Table/System Column) | SharePoint Type | Required | Description |
|------------|--------------------------------------|-----------------|----------|-------------|
| `nombre_responsable_ma` | Listado responsables medio ambiente (column 'nombre_evaluador_criterio_ma') | String | No | MA evaluator name |
| `email_responsable_ma` | Listado responsables medio ambiente (column 'correo') | String | No | MA evaluator email |

#### 3.4. Jefe de Área (Approver)

| Column Name | Source (External Table/System Column) | SharePoint Type | Required | Description |
|------------|--------------------------------------|-----------------|----------|-------------|
| `nombre_jefe_area` | Flujo aprobadores power app (column 'APROBADOR Y FIRMANTE') | String | Yes | Approver name |
| `email_jefe_area` | Flujo aprobadores power app (column 'CORREO1') | String | Yes | Approver email |
| `cargo_jefe_area` | Flujo aprobadores power app (column 'CARGO') | String | No | Approver position/role |
| `nombre_area` | Flujo aprobadores power app (column 'ÁREA') | String | No | Area of jefe |

---

### 4. Responsibility Flags (Dynamic Permissions)

| Column Name | Source (External Table/System Column) | SharePoint Type | Default | Description |
|------------|--------------------------------------|-----------------|---------|-------------|
| `responsable_sst_responde` | Flujo aprobadores power app (column 'Criterio de seguridad y salud en el trabajo') | Boolean | False | False = evaluate by técnico<br>True = dedicated SST evaluator |
| `responsable_ma_responde` | App user input (chosen answer) | Boolean | False | False = evaluate by técnico<br>True = dedicated MA evaluator |

> [!NOTE]
> **Permission Logic**:
> - If flag = `false`: Técnico evaluates that section
> - If flag = `true`: Dedicated specialist evaluates (SST or MA)

### 4.1. Submission Status Flags (New)

| Column Name | Source (External Table/System Column) | SharePoint Type | Default | Description |
|------------|--------------------------------------|-----------------|---------|-------------|
| `tecnico_submitted` | App user input | Boolean | False | True when Técnico clicks "Finalizar" |
| `sst_submitted` | App user input | Boolean | False | True when SST clicks "Finalizar" |
| `ma_submitted` | App user input | Boolean | False | True when MA clicks "Finalizar" |

---

### 5. Detailed Evaluation Questions

#### 5.1. Calidad (Quality Questions) - 5 Questions

| Column Name | Source (External Table/System Column) | SharePoint Type | Range | Description |
|------------|--------------------------------------|-----------------|-------|-------------|
| `calidad_pregunta_1` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | Client satisfaction option selected |
| `calidad_pregunta_2` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | Quality question 2 |
| `calidad_pregunta_3` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | Quality question 3 |
| `calidad_pregunta_4` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | Quality question 4 |
| `calidad_pregunta_5` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | Quality question 5 |

**Answer Options**: Each question has 3 choices (1, 2, or 3 corresponding to different quality levels)

#### 5.2. Oportunidad (Timeliness Question) - 1 Question

| Column Name | Source (External Table/System Column) | SharePoint Type | Range | Description |
|------------|--------------------------------------|-----------------|-------|-------------|
| `oportunidad_pregunta_1` | App user input (chosen answer) | Int (1 or 2) | 1-2 | Timeliness evaluation |

#### 5.3. Gestión (Management Questions) - 2 Questions

| Column Name | Source (External Table/System Column) | SharePoint Type | Range | Description |
|------------|--------------------------------------|-----------------|-------|-------------|
| `gestion_pregunta_1` | App user input (chosen answer) | Int (1 or 2) | 1-2 | Management question 1 |
| `gestion_pregunta_2` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | Management question 2 |

#### 5.4. SST (Safety & Health Question) - 1 Question

| Column Name | Source (External Table/System Column) | SharePoint Type | Range | Description |
|------------|--------------------------------------|-----------------|-------|-------------|
| `sst_pregunta_1` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | SST evaluation |

#### 5.5. MA (Environment Question) - 1 Question

| Column Name | Source (External Table/System Column) | SharePoint Type | Range | Description |
|------------|--------------------------------------|-----------------|-------|-------------|
| `ma_pregunta_1` | App user input (chosen answer) | Int (1, 2, or 3) | 1-3 | MA evaluation |

---

### 6. Calculated Scores (From Questions)

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `calificacion_criterio_calidad` | App user input | Int | Score for Calidad criteria |
| `calificacion_criterio_oportunidad` | App user input | Int | Score for Oportunidad |
| `calificacion_criterio_gestion` | App user input | Int | Score for Gestión |
| `calificacion_criterio_sst` | App user input | Int | Score for SST |
| `calificacion_criterio_ma` | App user input | Int | Score for MA |

> [!NOTE]
> These scores are calculated in the app based on the question answers above, using a scoring matrix/formula.

---

### 7. Ethics Compliance

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `cumple_codigo_etica` | App user input | Boolean | Complies with code of ethics |

**Impact**: If `false`, total score = 0 (major violation)

---

### 8. Observations (Comments)

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `observaciones_tecnico` | App user input (Observations given by app user associated to 'Técnico') | Multi-line text | Técnico comments |
| `observaciones_sst` | App user input (Observations given by app user associated to 'SST') | Multi-line text | SST comments |
| `observaciones_ma` | App user input (Observations given by app user associated to 'MA') | Multi-line text | MA comments |
| `observaciones_jefe_area` | App user input (Observations given by app user associated to 'Jefe de Área') | Multi-line text | Approver comments |

---

### 9. Total Score Calculations

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `puntaje_subtotal` | App logic (The sum of all 'calificacion_criterio_x' columns) | Int | Subtotal score |
| `puntaje_total` | App logic (If 'cumple_codigo_etica' = false, 'puntaje_subtotal', otherwise 0) | Int | Final total score |

**Calculation Logic**:
```powerfx
puntaje_subtotal = calidad + oportunidad + gestion + sst + ma

puntaje_total = If(cumple_codigo_etica, puntaje_subtotal, 0)
```

---

### 10. Improvement Plan

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `plan_mejoramiento` | App user input (True if 'puntaje_total' is less than 80, false otherwise) | Multi-line text | Improvement plan if score is low |

---

### 11. Approval Workflow

| Column Name | Source (External Table/System Column) | SharePoint Type | Default | Description |
|------------|--------------------------------------|-----------------|---------|-------------|
| `aprobada` | App user input | Boolean | False | Approved. True → create CSV and carta |
| `rechazada` | App user input | Boolean | False | Rejected. True → reset status |
| `numero_rechazos` | App user input | Int | 0 | Reject counter |

---

### 12. Robot 2 Outputs (CSV & Letter Generation)

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `csv_generado` | ROBOT 2 | Boolean | CSV generated flag |
| `url_csv` | ROBOT 2 | String | CSV file URL |
| `carta_generada` | ROBOT 2 | Boolean | Carta generated flag |
| `url_carta` | ROBOT 2 | String | Unsigned carta URL |

---

### 13. Digital Signatures

| Column Name | Source (External Table/System Column) | SharePoint Type | Description |
|------------|--------------------------------------|-----------------|-------------|
| `firma_responsable_tecnico` | ROBOT 2 | Boolean | Técnico signed |
| `firma_jefe_area` | ROBOT 2 | Boolean | Jefe signed |
| `url_carta_firmada` | ROBOT 2 | String | Signed carta URL |

---


---

## SharePoint List Settings

### Indexing (Recommended)
Index these columns for performance:
- `documento_compras`
- `fecha_evaluacion`
- `email_responsable_tecnico`
- `email_responsable_sst`
- `email_responsable_ma`
- `email_jefe_area`
- `aprobada`

### Versioning
- Major versions: 10+
- Enable for audit trail

### Permissions
- Inherit from site
- Or custom per-item if needed

---

## Data Sources Reference

### External Tables (SAP/MEIL)

| Table Name | Purpose | Key Columns |
|------------|---------|-------------|
| MEIL - CONTRATOS | Contract master data | documento_compras, fecha_validez, valor_contrato |
| ZMLISTPED - LISTADO PED | Provider master | sociedad, nombre_proveedor, objeto_contrato |
| ZMRVALP - EVALUACIÓN DE PROV | Evaluation sequence | consecutivo_evaluacion |

### Internal Tables (SharePoint/App)

| Table/Source | Purpose | Key Columns |
|--------------|---------|-------------|
| Maestro colaboradores | Employee directory | email, nombre |
| Tabla evaluadores SST | SST evaluators | email_sst, nombre_sst |
| Listado responsables MA | MA evaluators | email_ma, nombre_ma |
| Flujo aprobadores power app | Approver assignments | email_jefe, cargo, area |

---

## Validation Rules

### Required Fields
- `documento_compras`
- `fecha_evaluacion`
- `nombre_proveedor`
- `email_responsable_tecnico`
- `email_jefe_area`

### Business Logic
1. **Email format**: All email fields must be valid
2. **Question ranges**: Answers must be within allowed ranges
3. **Flag consistency**:
   - If `responsable_sst_responde = true` → `email_responsable_sst` required
   - If `responsable_ma_responde = true` → `email_responsable_ma` required
4. **Score calculation**: Must match question answers
5. **Ethics check**: If `cumple_codigo_etica = false` → `puntaje_total = 0`

---

## 14. Logic & Status Definitions

### 14.1 Ready for Approval Logic
**Definition**: An evaluation is considered "Ready for Approval" when all required sections are completed and it hasn't been approved or rejected yet.

**Logic Expression**:
```powerfx
IsReadyForApproval = 
    !aprobada && !rechazada &&
    tecnico_submitted &&
    sst_submitted &&
    ma_submitted

### 14.2 IsCompleted_Tecnico Logic
**Definition**: Checks if the Responsable Técnico has completed *their* assigned sections.

**Logic Expression**:
```powerfx
IsCompleted_Tecnico = tecnico_submitted
```

### 14.3 Filter Logic: "My Pending Evaluations"
**Goal**: Show evaluations where the *current user* needs to take action.
**Logic**: The user sees an item if **ANY** of the following conditions are true (OR logic handles users with multiple roles):
Filter(
    ongoingEvaluations,
    (
        // As Técnico: sees rejected items or items they haven't submitted
        (email_responsable_tecnico = varUserEmail && (rechazada || !tecnico_submitted)) ||
        // As SST: sees non-rejected items where they are responsible and haven't submitted
        (email_responsable_sst = varUserEmail && responsable_sst_responde && !rechazada && !sst_submitted) ||
        // As MA: sees non-rejected items where they are responsible and haven't submitted
        (email_responsable_ma = varUserEmail && responsable_ma_responde && !rechazada && !ma_submitted) ||
        // As Approver: sees items ready for approval
        (email_jefe_area = varUserEmail && IsReadyForApproval)
    ) &&
    // Overall filter: only show items that are not yet approved, or are rejected (for Técnico to action)
    (!aprobada || rechazada)
)

**Note**: This logic correctly handles cases where one user email plays multiple roles (e.g., Técnico + SST). If *any* role requires action, the item appears.

### 14.4 Count Evaluations
**Goal**: Count the number of evaluations where the *current user* needs to take action.
**Logic**: The user sees an item if **ANY** of the following conditions are true (OR logic handles users with multiple roles):
CountIf(
    ongoingEvaluations,
    (
        // As Técnico: sees rejected items or items they haven't submitted
        (email_responsable_tecnico = varUserEmail && !tecnico_submitted) ||
        // As SST: sees items where they are responsible and haven't submitted (includes Rejected)
        (email_responsable_sst = varUserEmail && responsable_sst_responde && !sst_submitted) ||
        // As MA: sees items where they are responsible and haven't submitted (includes Rejected)
        (email_responsable_ma = varUserEmail && responsable_ma_responde && !ma_submitted) ||
        // As Approver: sees items ready for approval
        (email_jefe_area = varUserEmail && IsReadyForApproval)
    )
)
---

## Notes & Best Practices

1. **Primary Key**: Use combination of `documento_compras` + `fecha_evaluacion`
2. **Email normalization**: Store all emails in lowercase
3. **Date format**: ISO 8601 (YYYY-MM-DD)
4. **Backup strategy**: Regular backups recommended
5. **Archive old data**: Move completed evaluations older than 2 years to archive list
