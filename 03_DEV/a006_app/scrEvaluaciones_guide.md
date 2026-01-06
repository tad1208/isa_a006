# Hướng dẫn Xây dựng Màn hình Đánh giá & Phê duyệt (scrEvaluaciones)

## Tóm tắt (Summary)

*   **Mục đích**: `scrEvaluaciones` là màn hình hợp nhất dùng cho cả việc **Đánh giá** (bởi Técnico, SST, MA) và **Phê duyệt** (bởi Jefe de Área).
*   **Hiển thị**: Toàn bộ câu hỏi đều hiện ra, nhưng chỉ user có role tương ứng (dựa trên email) mới có thể chỉnh sửa phần của mình.
*   **Footer**: Footer là container động chứa 2 nút bấm:
    *   Nếu là **Evaluator** (Técnico/SST/MA): Hiện nút **Finalizar** (Submit) và **Reset**.
    *   Nếu là **Approver** (Jefe de Área) và hồ sơ đã sẵn sàng: Hiện nút **Aprobar** (Approve) và **Rechazar** (Reject).
*   **Sidebar**: Dropdown sẽ tự động filter các evaluations mà user hiện tại có liên quan (bất kể vai trò là gì).

---

**Tài nguyên tham khảo:**
- Components: `components_guide.md`
- Criteria: `design/a006_app/evaluacion_criteria.md`
- Schema: `design/a006_app/sharepoint_lists_schema.md`

---

## 1. Thiết lập Màn hình (Screen Setup)

### 1.1. Tạo Screen
1.  **New Screen** → **Blank**
2.  Đổi tên: `scrEvaluaciones`
3.  **Fill**: `RGBA(248, 249, 250, 1)`

### 1.2. OnVisible Logic
```powerfx
// scrEvaluateProviders.OnVisible
Set(varUserEmail, gblUserEmail);
Set(varSelectedEvaluation, Blank());
Reset(ddlSelectEvaluation);
```

---

## 2. Layout Chính

Sử dụng cấu trúc **Sidebar (Left)** + **Main Form (Right)** + **Footer (Bottom)**.

### 2.1. Header
*   Copy `conHeader` từ `scrLanding`.

### 2.2. Main Container (`conMain`)
*   **Horizontal Container** full screen (trừ header & footer).
*   **Sidebar**: Chứa Dropdown chọn đánh giá.
    
#### 2.2.1. Sidebar Logic (Chọn Đánh giá)

*   Insert **Dropdown** vào Sidebar.
*   Name: `ddlSelectEvaluation`
*   **Properties**:
    *   `Items`: 
    ```powerfx
    Filter(
        ongoingEvaluations, 
        (
            // 1. Técnico: Sees if not submitted OR if rejected (to fix)
            (email_responsable_tecnico = varUserEmail && !tecnico_submitted) ||

            // 2. SST: Sees ONLY if flag is TRUE and not submitted
            (email_responsable_sst = varUserEmail && responsable_sst_responde && !sst_submitted) ||

            // 3. MA: Sees ONLY if flag is TRUE and not submitted
            (email_responsable_ma = varUserEmail && responsable_ma_responde && !ma_submitted) ||

            // 4. Approver: Sees if ready for approval (All submitted & Not processed)
            (email_jefe_area = varUserEmail && 
             !aprobada && !rechazada &&
             tecnico_submitted &&
             (!responsable_sst_responde || sst_submitted) &&
             (!responsable_ma_responde || ma_submitted)
            )
        )
    )
    ```
    *   `OnChange`:
    ```powerfx
    // Set selected evaluation
    Set(varSelectedEvaluation, Self.Selected);
    
    // === Load Values & Flags ===
    Set(varCalidadQ1Selected, Coalesce(varSelectedEvaluation.calidad_pregunta_1, 0));
    // ... (Load other questions similarly) ...
    
    Set(varTecnicoSubmitted, varSelectedEvaluation.tecnico_submitted);
    Set(varSSTSubmitted, varSelectedEvaluation.sst_submitted);
    Set(varMASubmitted, varSelectedEvaluation.ma_submitted);
    Set(varAprobada, varSelectedEvaluation.aprobada);
    Set(varRechazada, varSelectedEvaluation.rechazada);

    // === PERMISSION LOGIC ===
    
    // 1. Técnico Permission
    // Can edit if: My Email matches AND Not Submitted Yet
    Set(varCanEditTecnico, 
        varSelectedEvaluation.email_responsable_tecnico = varUserEmail &&
        !varTecnicoSubmitted
    );

    // 2. SST Section Permission
    // Logic: (Flag=True & I am SST & !SST_Sub) OR (Flag=False & I am Técnico & !SST_Sub)
    Set(varCanEditSST,
        If(varSelectedEvaluation.responsable_sst_responde,
            varSelectedEvaluation.email_responsable_sst = varUserEmail && !varSSTSubmitted,
            varSelectedEvaluation.email_responsable_tecnico = varUserEmail && !varSSTSubmitted
        )
    );

    // 3. MA Section Permission
    // Logic: (Flag=True & I am MA & !MA_Sub) OR (Flag=False & I am Técnico & !MA_Sub)
    Set(varCanEditMA,
        If(varSelectedEvaluation.responsable_ma_responde,
            varSelectedEvaluation.email_responsable_ma = varUserEmail && !varMASubmitted,
            varSelectedEvaluation.email_responsable_tecnico = varUserEmail && !varMASubmitted
        )
    );

    // 4. Approver Permission
    Set(varCanApprove, 
        varSelectedEvaluation.email_jefe_area = varUserEmail && 
        !varSelectedEvaluation.aprobada && 
        !varSelectedEvaluation.rechazada &&
        varTecnicoSubmitted &&
        varSSTSubmitted &&
        varMASubmitted
    );
    ```

---

## 3. Main Form Sections

Mỗi section sẽ có thuộc tính `Enable` được điều khiển động.

**General Rules**:
*   **Component Enable**: Use the standard `Enable` (Boolean) property.
*   Bind `Enable` property:
    ```powerfx
    Enable = Condition  // true = Edit, false = View
    ```

### 3.1. Section: Calidad, Oportunidad, Gestión
*   **Phụ trách**: Técnico.
*   **Enable Logic**:
    ```powerfx
    varCanEditTecnico && !varTecnicoSubmitted
    ```

### 3.2. Section: SST
*   **Phụ trách**: SST (nếu `responsable_sst_responde=true`) hoặc Técnico (nếu `false`).
*   **Enable Logic**:
    ```powerfx
    varCanEditSST && !varSSTSubmitted
    ```

### 3.3. Section: MA
*   **Phụ trách**: MA (nếu `responsable_ma_responde=true`) hoặc Técnico (nếu `false`).
*   **Enable Logic**:
    ```powerfx
    varCanEditMA && !varMASubmitted
    ```

---

## 4. Footer & Action Buttons

### 4.0. Thiết lập Container (`conFooter`)

Tạo một **Horizontal Container** cố định dưới đáy màn hình để chứa nút thao tác.

1.  **Insert**: Horizontal Container → Đổi tên `conFooter`.
2.  **Properties**:
    *   `X`: 0
    *   `Y`: Parent.Height - Self.Height
    *   `Width`: Parent.Width
    *   `Height`: 90
    *   `Fill`: `Color.White`
    *   `DropShadow`: Bold
    *   `LayoutJustifyContent`: `LayoutJustifyContent.End` (Căn phải)
    *   `LayoutAlignItems`: `LayoutAlignItems.Center`
    *   `Gap`: 20
    *   `PaddingRight`: 40

---

### 4.1. Nút cho Evaluator (Técnico / SST / MA)

Hiển thị khi user có quyền edit ít nhất 1 section VÀ chưa submit.

#### Nút 1: Limpiar (Secondary)
*   **Text**: "Limpiar"
*   **Visible**: `varCanEditTecnico || varCanEditSST || varCanEditMA`
*   **OnSelect**: Reset các giá trị về mặc định (theo quyền hạn).
    ```powerfx
    // 1. Reset Técnico Data
    If(varCanEditTecnico,
        Set(varCalidadQ1Selected, 0); // Calidad 1
        Set(varCalidadQ2Selected, 0);
        Set(varCalidadQ3Selected, 0);
        Set(varCalidadQ4Selected, 0);
        Set(varCalidadQ5Selected, 0);
        Set(varOportunidadQ1Selected, 0); // Oportunidad
        Set(varGestionQ1Selected, 0); // Gestion 1
        Set(varGestionQ2Selected, 0); // Gestion 2
        Set(varEtica, false);    
        Set(varMA, varSelectedEvaluation.responsable_ma_responde); // Revert to original flag
        Set(varObservationTecnico, "")
    );
    // 2. Reset SST Data
    If(varCanEditSST,
        Set(varSSTQ1Selected, 0); 
        Set(varObservationSST, "")
    );
    // 3. Reset MA Data
    If(varCanEditMA,
        Set(varMAQ1Selected, 0);
        Set(varObservationMA, "")
    );
    ```


#### Nút 2: Enviar (Primary)
*   **Text**: "Enviar"
*   **Visible**: `varCanEditTecnico || varCanEditSST || varCanEditMA`
*   **OnSelect**:
    ```powerfx
    // Patch dữ liệu về SharePoint
    Patch(
        ongoingEvaluations,
        varSelectedEvaluation,
        {
            puntaje_subtotal: If(lblSubtotalValue.Text = "-", Blank(), Value(lblSubtotalValue.Text))
        },
        If(varCanEditTecnico, {
            calidad_pregunta_1:	Value(cmpCalidadQ1.SelectedIndex),	
            calidad_pregunta_2:	Value(cmpCalidadQ2.SelectedIndex),		
            calidad_pregunta_3:	Value(cmpCalidadQ3.SelectedIndex),		
            calidad_pregunta_4:	Value(cmpCalidadQ4.SelectedIndex),		
            calidad_pregunta_5: Value(cmpCalidadQ5.SelectedIndex),
            oportunidad_pregunta_1:	Value(cmpOportunidadQ1.SelectedIndex),	
            gestion_pregunta_1:	Value(cmpGestionQ1.SelectedIndex),	
            gestion_pregunta_2:	Value(cmpGestionQ2.SelectedIndex),
            cumple_codigo_etica: varEtica,
            responsable_ma_responde: varMA,
            observaciones_tecnico: cmpObservationTecnico.Value,
            tecnico_submitted: true
        }, {}),
        If(varCanEditSST, {
            sst_pregunta_1:	Value(cmpSST.SelectedIndex),	
            observaciones_sst: cmpObservationSST.Value,	
            sst_submitted: true
        }, {}),
        If(varCanEditMA, {
            ma_pregunta_1:	Value(cmpMA.SelectedIndex),
            observaciones_ma: cmpObservationMA.Value,   
            ma_submitted: true
        }, {})
    );
    
    // Thông báo thành công
    Notify("Evaluación guardada correctamente", NotificationType.Success);

    // Reset selection (tuỳ chọn)
    Set(varSelectedEvaluation, Blank());
    ```

   **IsEnabled**: 
    ```powerfx
    // Button Enviar - IsEnabled
    // Técnico validation
    If(varCanEditTecnico,
        Value(cmpCalidadQ1.SelectedIndex) > 0 &&
        Value(cmpCalidadQ2.SelectedIndex) > 0 &&
        Value(cmpCalidadQ3.SelectedIndex) > 0 &&
        Value(cmpCalidadQ4.SelectedIndex) > 0 &&
        Value(cmpCalidadQ5.SelectedIndex) > 0 &&
        Value(cmpOportunidadQ1.SelectedIndex) > 0 &&
        Value(cmpGestionQ1.SelectedIndex) > 0 &&
        Value(cmpGestionQ2.SelectedIndex) > 0,
        true
    ) &&
    // SST validation
    If(varCanEditSST,
        Value(cmpSST.SelectedIndex) > 0,
        true
    ) &&
    // MA validation
    If(varCanEditMA,
        Value(cmpMA.SelectedIndex) > 0,
        true
    )
    ```



### 4.2. Nút cho Approver (Jefe de Área)

Hiển thị khi `varCanApprove` = true.

#### Nút 1: RECHAZAR (Destructive)
*   **Text**: "RECHAZAR"
*   **Color**: Red/Warning
*   **Visible**: `varCanApprove`
*   **OnSelect**:
    ```powerfx
    // Patch data to SharePoint
    Patch(
        ongoingEvaluations,
        varSelectedEvaluation,
        If(varCanApprove, {
            tecnico_submitted: false,
            sst_submitted: false,
            ma_submitted: false,
            aprobada: false,
            rechazada: true,
            observaciones_jefe_area: ""        
        }, {})    
    );
    
    // Notification
    Notify("Evaluation rejected", NotificationType.Success);

    // Reset selection (tuỳ chọn)
    Set(varSelectedEvaluation, Blank());
    ```

#### Nút 2: APROBAR (Primary)
*   **Text**: "APROBAR"
*   **Visible**: `varCanApprove`
*   **OnSelect**:
    ```powerfx
    // Patch data to SharePoint
    Patch(
        ongoingEvaluations,
        varSelectedEvaluation,
        If(varCanApprove, {
            aprobada: true,
            rechazada: false,
            observaciones_jefe_area: ""        
        }, {})    
    );
    
    // Notification
    Notify("Evaluation approved", NotificationType.Success);

    // Reset selection (tuỳ chọn)
    Set(varSelectedEvaluation, Blank());
    ```

---

## 5. Checklist Kiểm tra

- [ ] Dropdown hiển thị đúng list evaluations theo user login.
- [ ] Técnico edit được sections Calidad/Gestión/Oportunidad.
- [ ] User khác (SST/MA) chỉ xem được (View Only) các section không phải của mình.
- [ ] Nút "Finalizar" update đúng cờ `_submitted` tương ứng.
- [ ] Khi tất cả flags submitted -> Approver thấy nút Approve/Reject.
- [ ] Reject -> Reset flags -> Técnico có thể edit lại.
