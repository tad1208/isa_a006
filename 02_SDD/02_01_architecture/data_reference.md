# Data Reference (Updated)

This document outlines the data sources, output requirements, and database structure for the project. It includes details on Excel/SQL sources, CSV output specifications, and SharePoint List definitions.

## 1. Excel files updated by SAP Scripting (Dynamic Data)
*Location: SharePoint*
*Update Frequency: Periodic (via external process)*

### 1.1. ME3L – CONTRATOS.xlsx
**Summary**: The primary source for contract data. It lists all contracts that require periodic evaluation.
**Columns:**
*   `Documento compras`: Contract ID / Purchasing Document.
*   `Cl.documento compras`: Purchasing Document Type.
*   `In.período validez`: Contract start date.
*   `Fin período validez`: Contract end date.
*   `N° necesidad`: Requirement ID (links to approvers).
*   `Sociedad`: Society/Company Code (Note: Marked as "NOT FOUND IN FILE" in source, verify availability).
*   `Val.prev.(niv.cab.)`: Estimated contract value.

### 1.2. ZMLISTPED – LISTADO PED.xlsx
**Summary**: Provides supplementary details for contracts, specifically supplier names and technical responsibilities.
**Columns:**
*   `Documento compras`: Contract ID (Key).
*   `Sociedad`: Society.
*   `Obj.Contrato`: Object of the contract.
*   `Nombre proveedor`: Supplier Name.
*   `Cod.Resp.Técnico`: Technical Responsible Code.
*   `Referencia`: Reference.

### 1.3. ZMREVALP – EVALUACION DE PROV.xlsx
**Summary**: Tracks the history of evaluations for each contract to determine the next evaluation sequence number.
**Columns:**
*   `Cons.Eval.Contrato`: Evaluation sequence number.
*   `Documento de compras`: Contract ID.
*   `Fecha evaluación`: Date of evaluation.

---

## 2. Excel 'static' files (Business Rules & Master Data)
*Location: SharePoint*
*Update Frequency: Low (Static/Manual updates)*

### 2.1. Lista para criterio de SST.xlsx
**Summary**: Determines if the **SST (Health & Safety)** section of the evaluation is required based on the contract's Warranty Type.
**Logic**:
*   If `Criterio...` = "SI" -> SST User fills out SST section.
*   If `Criterio...` = "NO" -> Contract Administrator fills out SST section.
**Columns:**
*   `IDTipoGarantia`: Warranty Type ID.
*   `NombreTipoGarantia`: Warranty Type Name.
*   `Criterio de seguridad y salud en el trabajo`: Flag (SI/NO) for SST criterion.

### 2.2. Tabla evaluadores criterio de SST.xlsx
**Summary**: Maps the **Society** to the specific **SST Evaluator** (Name & Email).
**Columns:**
*   `Sociedad`: Society Code.
*   `nombre evaluador criterio SST`: Name of the SST Evaluator.
*   `correo`: Email of the SST Evaluator.

### 2.3. Flujo aprobadores power app.xlsx
**Summary**: Maps the **Requirement ID (N-necesidad)** to the designated **Approver/Signer**.
**Columns:**
*   `Sociedad`: Society Code.
*   `N-necesidad`: Requirement ID.
*   `ÁREA`: Area name.
*   `APROBADOR Y FIRMANTE DE EVALUACIÓN`: Name of the Approver/Signer.
*   `CORREO1`: Email of the Approver.

### 2.4. Maestro colaboradores.xlsx
**Summary**: The master list of employees. It identifies the **Contract Administrator** and their details. It is also used to determine if the **Environment (Medio Ambiente)** criterion applies.
**Columns:**
*   `Empresa`: Company.
*   `RUT`: Tax ID / National ID.
*   `Apellido Paterno`: Paternal Last Name.
*   `Apellido Materno`: Maternal Last Name.
*   `Nombres`: First Names.
*   `Mail corporativo`: Corporate Email.

### 2.5. Listado responsables medio ambiente.xlsx
**Summary**: Maps the **Society** to the specific **Environment (Medio Ambiente) Evaluator**.
**Columns:**
*   `Sociedad`: Society Code.
*   `Nombre evaluador criterio SST`: Name of the Environment Evaluator.
*   `correo`: Email.

---

## 3. SQL Server Database Tables

### 3.1. documentoGarantia
**Summary**: Links the SAP Contract Number to its Warranty Type ID.
*Note: For testing, use `documentoGarantia.csv`.*
**Columns:**
*   `numero_contrato_sap`: SAP Contract Number.
*   `id_tipoGarantia`: Warranty Type ID.

---

## 4. Relationships Diagram
The data sources' relationships can be visualized with the following diagram (cross_reference_diagram.drawio):

[*(Diagram)*](data_reference_tables.png)

These relationships should be used to retrieve all the relevant data required for a contract evaluation. This data should make up a single row in the project database (the corresponding SharePoint list).

---

## 5. Data to display in the application's evaluation form
The client has requested that, for each contract being evaluated, the following data must be displayed in the application's evaluation form (as header or initial section):

*   Current date
*   The contract's society (ME3L – CONTRATOS 'Sociedad' column value)
*   Supplier name (ZMLISTPED – LISTADO PED 'Nombre proveedor' column value)
*   Contract's number or ID (ME3L – CONTRATOS 'Documento compras' column value)
*   Contract's validity period start date (ME3L – CONTRATOS 'In.período validez' column value)
*   Contract's validity period end date (ME3L – CONTRATOS 'Fin período validez' column value)
*   Contract's value (ME3L – CONTRATOS 'Val.prev.(niv.cab.)' column value)
*   Counter of evaluations the contract has undergone (CALCULATED) (TO BE CONFIRMED)
*   "Grupo de artículo (Subcategoría)" (UNKNOWN ASK CLIENT)
*   Consecutivo evaluación (ZMREVALP – EVALUACION DE PROV 'Cons.Eval.Contrato' column value)
*   Name of user that fills out the evaluation form (Full name by joining the Maestro de colaboradores 'Nombres', 'Apellido Paterno', 'Apellido Materno' column values)

---

## 6. Data that output CSV file must include
The following table describes the data the CSV file (an output of an approved evaluation) must include.
**File Name Format**: `{DOCUMENT NUMBER}_{NUMBER OF EVALUATIONS}_{YEAR OF EVALUATION}.csv`

| CSV Column | Source |
| :--- | :--- |
| Sociedad | |
| Documento de compra/n° contrato | |
| Cod grupo de artículo | |
| Objeto del contrato | |
| Nombre proveedor | |
| Nombre jefe del área | |
| Nombre área | |
| Correo jefe del área | |
| Nombre de responsable técnico | |
| Correo del responsable técnico | |
| Calificación criterio de Calidad | |
| Calificación criterio de oportunidad | |
| Calificación criterio de gestion | |
| Calificación criterio de prevención de riesgo SST | |
| Calificación medio ambiente. | |
| Código de conducta | |
| Observaciones responsable técnico | |
| Observaciones responsable SST | |
| Observaciones responsables medio ambiente | |
| Nota Sub Total | |
| Nota total | |
| Plan de mejoramiento | |

---

## 7. SharePoint Lists Database
The project's database is comprised of 3 SharePoint Lists.

### 7.1. ongoingEvaluations
**Description**: Each row is an ongoing evaluation. Robot 1 populates this table.
**Columns**:

| Column name | Source | Type |
| :--- | :--- | :--- |
| `documento_compras` | ME3L – CONTRATOS | string (ID) |
| `fecha_evaluacion` | ROBOT 1 | date |
| `fecha_inicio_validez` | ME3L – CONTRATOS | date |
| `fecha_fin_validez` | ME3L – CONTRATOS | date |
| `sociedad` | ZMLISTPED – LISTADO PED | string |
| `nombre_proveedor` | ZMLISTPED – LISTADO PED | string |
| `objeto_contrato` | ZMLISTPED – LISTADO PED | string |
| `nombre_responsable_tecnico` | Maestro colaboradores | string |
| `email_responsable_tecnico` | Maestro colaboradores | string |
| `nombre_responsable_sst` | Tabla evaluadores criterio de SST | string |
| `email_responsable_sst` | Tabla evaluadores criterio de SST | string |
| `nombre_responsable_ma` | Listado responsables medio ambiente | string |
| `email_resopnsable_ma` | Listado responsables medio ambiente | string |
| `nombre_jefe_area` | Flujo aprobadores power app | string |
| `email_jefe_area` | Flujo aprobadores power app | string |
| `nombre_area` | Flujo aprobadores power app | string |
| `responsable_sst_responde` | Lista para criterio de SST | bool |
| `responsable_ma_responde` | App user input | bool |
| `calificacion_criterio_calidad` | App user input | int |
| `calificacion_criterio_oportunidad` | App user input | int |
| `calificacion_criterio_gestion` | App user input | int |
| `calificacion_criterio_sst` | App user input | int |
| `calificacion_criterio_ma` | App user input | int |
| `cumple_codigo_etica` | App user input | bool |
| `observaciones_tecnico` | App user input | string |
| `observaciones_sst` | App user input | string |
| `observaciones_ma` | App user input | string |
| `observaciones_jefe_area` | App user input | string |
| `nota_subtotal` | App user input | int |
| `nota_total` | App user input | int |
| `plan_mejoramiento` | | |
| `aprobada` | App user input | bool |
| `rechazada` | App user input | bool |
| `numero_rechazos` | App user input | int |
| `csv_generado` | ROBOT 2 | bool |
| `url_csv` | ROBOT 2 | string |
| `carta_generada` | ROBOT 2 | bool |
| `url_carta` | ROBOT 2 | string |
| `firma_responsable_tecnico` | ROBOT 2 | bool |
| `firma_jefe_area` | ROBOT 2 | bool |
| `url_carta_firmada` | ROBOT 2 | string |

### 7.2. completedEvaluations
**Description**: Same structure as `ongoingEvaluations`. Rows are moved here by Robot 2 upon completion.

### 7.3. evaluationsCounter
**Description**: Tracks the number of evaluations per contract.
**Columns**:

| Column name | Source | Type |
| :--- | :--- | :--- |
| `documento_compras` | ME3L – CONTRATOS | string (ID) |
| `numero_evaluaciones` | ROBOT 1 (Calculated) | int (starts at 1) |

---

## 8. Glossary (Spanish to English)

| Spanish Term | English Translation | Description |
| :--- | :--- | :--- |
| **Aprobador** | Approver | The person responsible for approving the evaluation. |
| **Apellido Materno** | Maternal Last Name | Second part of the surname. |
| **Apellido Paterno** | Paternal Last Name | First part of the surname. |
| **Calificación** | Qualification/Score | The score assigned to a specific criterion. |
| **Cl.documento compras** | Purchasing Doc Type | Classification of the purchasing document. |
| **Cons.Eval.Contrato** | Eval. Sequence | A running number indicating the count (e.g., Evaluation #1, #2). |
| **Contrato** | Contract | The legal agreement being evaluated. |
| **Correo** | Email | Email address. |
| **Criterio** | Criterion | A section or category of the evaluation. |
| **Documento compras** | Purchasing Document | In this context, usually refers to the **Contract ID**. |
| **Fecha inicio/fin** | Start/End Date | Validity period of the contract. |
| **Firmante** | Signer | The person who digitally signs the evaluation letter. |
| **Jefe del área** | Area Chief/Manager | The manager responsible for the area. |
| **Medio Ambiente** | Environment | Refers to the environmental compliance criterion. |
| **N° necesidad** | Requirement No. | Internal requisition ID used to route approvals. |
| **Nombre proveedor** | Supplier Name | The vendor being evaluated. |
| **Observaciones** | Observations | Comments or notes added by evaluators. |
| **Plan de mejoramiento** | Improvement Plan | Action plan if the evaluation score is low. |
| **Responsable técnico** | Technical Responsible | The main person responsible for the technical evaluation. |
| **RUT** | Tax ID | *Rol Único Tributario* - Unique Tax/ID number in Chile. |
| **Sociedad** | Society / Company | The specific legal entity within the group. |
| **SST** | OHS | *Seguridad y Salud en el Trabajo* (Occupational Health and Safety). |
| **Val.prev.(niv.cab.)** | Est. Value (Header) | The estimated total value of the contract. |
