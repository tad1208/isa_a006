# scrEvaluateProviders - Màn hình Đánh giá Nhà cung cấp

## 1. Thông tin Chung (General Information)

**Tên màn hình**: `scrEvaluateProviders`  
**Mục đích**: Cho phép Evaluators thực hiện đánh giá nhà cung cấp theo các tiêu chí Técnico, SST, và MA  
**Người dùng**: Responsable Técnico, Responsable SST, Responsable MA

---

## 2. Giao Diện UI (User Interface)

### 2.1. Layout Tổng Quan

```
┌───────────────────────────────────────────────────────────────┐
│ [LOGO]  [Evaluar*] [Aprobar] [Firmar] [Dashboard]           │ ← Header
├───────┬───────────────────────────────────────────────────────┤
│       │  HEADER BLOCK (Form Header)                          │
│Select │  ┌─────────────────────────────────────────────┐     │
│Eval   │  │ Proveedor: ABC Company S.A.                 │     │
│       │  │ Contrato: C12345                             │     │
│[v]    │  │ Período: 2025-Q1                             │     │
│Eval 1 │  │ Responsable Técnico: juan@isa.com            │     │
│Eval 2 │  └─────────────────────────────────────────────┘     │
│Eval 3 │                                                        │
│       ├───────────────────────────────────────────────────────┤
│       │  TECHNICO SECTION                                     │
│       │  ┌─────────────────────────────────────────────┐     │
│       │  │ Calificación - Calidad: [85__]              │     │
│       │  │ Calificación - Oportunidad: [90__]          │     │
│       │  │ Calificación - Gestión: [88__]              │     │
│       │  │ [✓] Cumple código ética                     │     │
│       │  │ [ ] Requiere evaluación MA ← Toggle (RT)    │     │
│       │  │ Observaciones Técnico:                       │     │
│       │  │ [_____________________________________]      │     │
│       │  └─────────────────────────────────────────────┘     │
│       ├───────────────────────────────────────────────────────┤
│       │  SST SECTION (Seguridad, Salud y Trabajo)            │
│       │  ┌─────────────────────────────────────────────┐     │
│       │  │ Calificación SST: [92__]                    │     │
│       │  │ Observaciones SST:                           │     │
│       │  │ [_____________________________________]      │     │
│       │  └─────────────────────────────────────────────┘     │
│       ├───────────────────────────────────────────────────────┤
│       │  MA SECTION (Medio Ambiente)                          │
│       │  ┌─────────────────────────────────────────────┐     │
│       │  │ Calificación MA: [87__]                     │     │
│       │  │ Observaciones MA:                            │     │
│       │  │ [_____________________________________]      │     │
│       │  └─────────────────────────────────────────────┘     │
│       ├───────────────────────────────────────────────────────┤
│       │  OVERALL SCORE & BUTTONS                              │
│       │  ┌─────────────────────────────────────────────┐     │
│       │  │ Nota Subtotal: 442                          │     │
│       │  │ Nota Total: 442 (✓ Cumple código ética)     │     │
│       │  │                                              │     │
│       │  │                    [Cancelar] [Guardar]      │     │
│       │  └─────────────────────────────────────────────┘     │
└───────┴───────────────────────────────────────────────────────┘

Notas:
- Sidebar (trái): Chọn evaluation để đánh giá
- Main Form (phải): Scroll vertical nếu nội dung dài
- Sections visibility: Tùy thuộc role và responsibility flags
- Toggle "Requiere evaluación MA": Chỉ visible cho Responsable Técnico
```

### 2.2. Chi Tiết Components

#### 2.2.1. Header (con_Header)
- Tái sử dụng từ scrLanding
- Nút "Evaluar Proveedores" ở trạng thái **Active**

#### 2.2.2. Main Container (con_Main)
- **Type**: Horizontal Container
- **Position**: X=0, Y=80 (sau header)
- **Dimensions**: Width=Parent.Width, Height=Parent.Height - 80
- **LayoutMode**: Auto
- **LayoutGap**: 0

#### 2.2.3. Sidebar (con_Sidebar)
**Position**: Bên trái con_Main  
**Width**: 300px
**Styling**: White background, right border

**Components**:
1. **ddl_SelectEvaluation** (Dropdown)
   - Items: 
     ```powerfx
     Filter(
         ongoingEvaluations,
         email_responsable_tecnico = varUserEmail ||
         email_responsable_sst = varUserEmail ||
         email_responsable_ma = varUserEmail
     )
     ```
   - DisplayFields: `["documento_compras"]` hoặc custom template
   - OnChange: `Set(varSelectedEvaluation, Self.Selected)`

2. **lbl_EmptyState** (Label) - Optional
   - Visible: `IsBlank(varSelectedEvaluation)`
   - Text: "Selecciona una evaluación para continuar"

#### 2.2.4. Main Form Container (con_MainForm)
**Position**: Bên phải con_Main  
**FillPortions**: 3  
**LayoutOverflowY**: `LayoutOverflow.Scroll` ← Quan trọng!

**Child Containers** (theo thứ tự từ trên xuống):

##### A. Form Header Block (con_FormHeader)
- **Visible**: `!IsBlank(varSelectedEvaluation)`
- **Styling**: White card, border, radius 8px

**Fields hiển thị**:
- Provider name: `varSelectedEvaluation.nombre_proveedor`
- Contract ID: `varSelectedEvaluation.documento_compras`
- Period: `varSelectedEvaluation.periodo`
- Responsible: `varSelectedEvaluation.email_responsable_tecnico`

##### B. Technico Section (con_TecnicoSection)
- **Visible**: 
  ```powerfx
  !IsBlank(varSelectedEvaluation) && varCanEvaluateTecnico
  ```
- **DisplayMode**:
  ```powerfx
  If(varCanEvaluateTecnico, DisplayMode.Edit, DisplayMode.View)
  ```

**Input Fields**:
1. **txt_CalidadTecnico** (Text/Number Input)
   - Default: `varSelectedEvaluation.calificacion_criterio_calidad`
   - HintText: "Ingrese calificación (0-100)"

2. **txt_OportunidadTecnico** (Text/Number Input)
   - Default: `varSelectedEvaluation.calificacion_criterio_oportunidad`

3. **txt_GestionTecnico** (Text/Number Input)
   - Default: `varSelectedEvaluation.calificacion_criterio_gestion`

4. **chk_CumpleCodigoEtica** (Checkbox)
   - Default: `varSelectedEvaluation.cumple_codigo_etica`
   - Text: "Cumple código de ética"

5. **toggle_RequiereMA** (Toggle o Checkbox)
   - **Visible**: `varCanEvaluateTecnico` (Đặc quyền của Responsable Técnico)
   - Default: `varSelectedEvaluation.responsable_ma_responde`
   - Text: "Requiere evaluación de Medio Ambiente"
   - **OnChange**: 
     ```powerfx
     Patch(
         ongoingEvaluations,
         varSelectedEvaluation,
         {responsable_ma_responde: Self.Value}
     )
     ```

6. **txt_ObservacionesTecnico** (Multi-line Text)
   - Label: "Observaciones Técnico"
   - Default: `varSelectedEvaluation.observaciones_tecnico`
   - Mode: `TextMode.MultiLine`
   - Height: 100

##### C. SST Section (con_SSTSection)
- **Visible**: 
  ```powerfx
  !IsBlank(varSelectedEvaluation) && varCanEvaluateSST
  ```
- **DisplayMode**:
  ```powerfx
  If(varCanEvaluateSST, DisplayMode.Edit, DisplayMode.View)
  ```

**Input Fields**:
1. **txt_CalificacionSST**
   - Default: `varSelectedEvaluation.calificacion_criterio_sst`
   
2. **txt_ObservacionesSST** (Multi-line)
   - Label: "Observaciones SST"
   - Default: `varSelectedEvaluation.observaciones_sst`

##### D. MA Section (con_MASection)
- **Visible**: 
  ```powerfx
  !IsBlank(varSelectedEvaluation) && varCanEvaluateMA
  ```
- **DisplayMode**:
  ```powerfx
  If(varCanEvaluateMA, DisplayMode.Edit, DisplayMode.View)
  ```

**Input Fields**:
1. **txt_CalificacionMA**
   - Default: `varSelectedEvaluation.calificacion_criterio_ma`
   
2. **txt_ObservacionesMA** (Multi-line)
   - Label: "Observaciones MA"
   - Default: `varSelectedEvaluation.observaciones_ma`

##### E. Overall Score & Buttons (con_Overall)
- **Visible**: `!IsBlank(varSelectedEvaluation)`

**Components**:
1. **lbl_TotalScore** (Label)
   - **Text**:
     ```powerfx
     "Puntuación Total: " & 
     Text(
         Coalesce(Value(txt_CalidadTecnico.Text), 0) +
         Coalesce(Value(txt_OportunidadTecnico.Text), 0) +
         Coalesce(Value(txt_GestionTecnico.Text), 0) +
         Coalesce(Value(txt_CalificacionSST.Text), 0) +
         Coalesce(Value(txt_CalificacionMA.Text), 0)
     )
     ```

2. **btn_Reset** (Button)
   - Text: "Cancelar"
   - Appearance: Secondary
   - OnSelect: Reset all inputs

3. **btn_Submit** (Button)
   - Text: "Guardar"
   - Appearance: Primary
   - OnSelect: Patch data to SharePoint (see Logic section)

---

## 3. Logic và Behavior (Functionality)

### 3.1. OnVisible
```powerfx
// Get user info
Set(varUserEmail, User().Email);

// Xác định quyền dựa trên email và flags
Set(varCanEvaluateTecnico, 
    varSelectedEvaluation.email_responsable_tecnico = varUserEmail
);

Set(varCanEvaluateSST, 
    (varSelectedEvaluation.responsable_sst_responde && 
     varSelectedEvaluation.email_responsable_sst = varUserEmail) ||
    (!varSelectedEvaluation.responsable_sst_responde && 
     varCanEvaluateTecnico)
);

Set(varCanEvaluateMA, 
    (varSelectedEvaluation.responsable_ma_responde && 
     varSelectedEvaluation.email_responsable_ma = varUserEmail) ||
    (!varSelectedEvaluation.responsable_ma_responde && 
     varCanEvaluateTecnico)
);

// Reset selection
Set(varSelectedEvaluation, Blank());

// Set current screen
Set(gblCurrentScreen, scrEvaluateProviders);
```

### 3.2. Submit Logic
```powerfx
// btn_Submit.OnSelect
Patch(
    ongoingEvaluations,
    varSelectedEvaluation,
    {
        // Technico
        calificacion_criterio_calidad: Value(txt_CalidadTecnico.Text),
        calificacion_criterio_oportunidad: Value(txt_OportunidadTecnico.Text),
        calificacion_criterio_gestion: Value(txt_GestionTecnico.Text),
        cumple_codigo_etica: chk_CumpleCodigoEtica.Value,
        observaciones_tecnico: txt_ObservacionesTecnico.Text,
        // MA flag (if Responsable Técnico)
        responsable_ma_responde: If(varCanEvaluateTecnico, toggle_RequiereMA.Value, varSelectedEvaluation.responsable_ma_responde),
        // SST
        calificacion_criterio_sst: Value(txt_CalificacionSST.Text),
        observaciones_sst: txt_ObservacionesSST.Text,
        // MA
        calificacion_criterio_ma: Value(txt_CalificacionMA.Text),
        observaciones_ma: txt_ObservacionesMA.Text
    }
);

// Thông báo
Notify("Evaluación guardada correctamente", NotificationType.Success);
```

### 3.3. Validation Logic
```powerfx
// btn_Submit.DisplayMode
If(
    IsBlank(varSelectedEvaluation) ||
    (varCanEvaluateTecnico && 
     (IsBlank(txt_CalidadTecnico.Text) || 
      IsBlank(txt_OportunidadTecnico.Text) || 
      IsBlank(txt_GestionTecnico.Text))),
    DisplayMode.Disabled,
    DisplayMode.Edit
)
```

### 3.4. Role-Based Section Visibility

| User Role | Technico Section | SST Section | MA Section |
|-----------|----------------|-------------|------------|
| Responsable Técnico (RT) | Visible, Editable | Visible, Editable (nếu `responsable_sst_responde=false`) | Visible, Editable (nếu `responsable_ma_responde=false`) |
| Responsable SST | Hidden/View-only | Visible, Editable (nếu `responsable_sst_responde=true`) | Hidden/View-only |
| Responsable MA | Hidden/View-only | Hidden/View-only | Visible, Editable (nếu `responsable_ma_responde=true`) |

**Logic chi tiết**:
```powerfx
// Technico Section
con_TecnicoSection.Visible = varCanEvaluateTecnico

// SST Section
con_SSTSection.Visible = varCanEvaluateSST
con_SSTSection.DisplayMode = If(varCanEvaluateSST, DisplayMode.Edit, DisplayMode.View)

// MA Section
con_MASection.Visible = varCanEvaluateMA
con_MASection.DisplayMode = If(varCanEvaluateMA, DisplayMode.Edit, DisplayMode.View)
```

---

## 4. Data Connections

### 4.1. Data Sources
- **ongoingEvaluations** (SharePoint): Read & Write - Lấy danh sách evaluations và cập nhật scores/observations

**Lưu ý**: KHÔNG cần kết nối `completedEvaluations` ở screen này.

### 4.2. Variables
| Variable | Type | Purpose |
|----------|------|---------|
| `varSelectedEvaluation` | Record | Evaluation đang được edit |
| `varCanEvaluateTecnico` | Boolean | Quyền đánh giá Técnico |
| `varCanEvaluateSST` | Boolean | Quyền đánh giá SST (dựa trên flag + email) |
| `varCanEvaluateMA` | Boolean | Quyền đánh giá MA (dựa trên flag + email) |
| `varUserEmail` | Text | Email user hiện tại |

---

## 5. Responsive Design

### 5.1. Scroll Behavior
- **Desktop/Laptop**: Form có scroll vertical khi nội dung dài hơn viewport
- **Tablet**: Tương tự desktop
- **Mobile**: Sidebar có thể ẩn, chỉ hiển thị dropdown

### 5.2. Layout Adjustments
```powerfx
// Sidebar width
con_Sidebar.Width = If(Parent.Width < 768, 0, 300)

// Dropdown visible on mobile
ddl_SelectEvaluation_Mobile.Visible = Parent.Width < 768
```

---

## 6. Testing Scenarios

### 6.1. Scenario 1: Responsable Técnico đánh giá Técnico + SST (không có RT SST riêng)
**Given**: User là Responsable Técnico, `responsable_sst_responde = false`  
**When**: Chọn evaluation từ dropdown  
**Then**: 
- Technico Section visible & editable
- SST Section visible & editable
- MA Section: Tùy thuộc flag `responsable_ma_responde`

### 6.2. Scenario 2: Responsable SST chỉ đánh giá SST
**Given**: User là Responsable SST, `responsable_sst_responde = true`, email match  
**When**: Chọn evaluation  
**Then**: 
- Technico Section: View-only hoặc hidden
- SST Section: Editable
- MA Section: Hidden

### 6.3. Scenario 3: Responsable Técnico toggle MA flag
**Given**: User là RT, đang ở Technico Section  
**When**: Check "Requiere evaluación MA"  
**Then**:
- Flag `responsable_ma_responde` = true trong SharePoint
- MA Section sẽ hiển thị cho Responsable MA (nếu có)
- Nếu chưa có Responsable MA, RT vẫn đánh giá được

---

## 7. Known Issues & Limitations

### 7.1. Delegation
- Filter trên `email_responsable_tecnico` có thể gây delegation warning nếu list lớn
- **Mitigation**: Dùng View trong SharePoint với pre-filter

### 7.2. Concurrent Editing
- Nếu 2 users (General + SST) edit cùng 1 evaluation đồng thời, có thể xảy ra conflict
- **Mitigation**: Implement locking mechanism hoặc versioning

---

## 8. Future Enhancements

1. **Auto-save**: Tự động lưu khi user nhập
2. **Field-level validation**: Real-time validation (e.g., score phải 0-100)
3. **History**: Xem lịch sử chỉnh sửa
4. **Comments threading**: Cho phép reply comments
