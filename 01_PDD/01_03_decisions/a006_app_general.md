# A006 Power App - Tổng quan (General Overview)

## 1. Giới thiệu (Introduction)

### 1.1. Mục đích ứng dụng
Power App **"Evaluación de Proveedores"** (Đánh giá Nhà cung cấp) được phát triển cho ISA INTERVIAL nhằm số hóa quy trình đánh giá hiệu suất của các nhà cung cấp. Ứng dụng cho phép:

- **Responsable Técnico** (Người chịu trách nhiệm kỹ thuật): Đánh giá tiêu chí Técnico, và có thể đánh giá SST/MA nếu không có người chuyên trách
- **Responsable SST** (Người chuyên trách An toàn): Đánh giá tiêu chí SST (nếu flag `responsable_sst_responde = true`)
- **Responsable MA** (Người chuyên trách Môi trường): Đánh giá tiêu chí Medio Ambiente (nếu flag `responsable_ma_responde = true`)
- **Aprobador** (Người phê duyệt): Xem xét và phê duyệt/từ chối các đánh giá đã hoàn thành
- **Firmantes** (Người ký): Ký điện tử các thư đánh giá

### 1.2. Vai trò trong giải pháp tổng thể (A006 Solution)
Power App là thành phần **front-end** trong giải pháp A006, tích hợp với:
- **UiPath Robot 1**: Tạo và gửi đánh giá từ SharePoint trigger
- **UiPath Robot 2**: Xử lý kết quả đánh giá, tạo CSV và thư đánh giá
- **SharePoint Lists**: Lưu trữ dữ liệu đánh giá và master data

---

## 2. Kiến trúc Kỹ thuật (Technical Architecture)

### 2.1. Platform
- **Nền tảng**: Microsoft Power Apps (Canvas App)
- **Môi trường**: Power Platform Environment với Dataverse hoặc SharePoint connector
- **Deployment**: Power Apps Solution (.msapp hoặc managed solution)

### 2.2. Cấu trúc Screens
App bao gồm các màn hình chính được tối giản hóa:

1. **scrEvaluations**: Màn hình chính xử lý cả hai luồng công việc:
   - **Evaluate**: Dành cho Responsable Técnico/SST/MA thực hiện đánh giá.
   - **Approve**: Dành cho Approver xem xét và phê duyệt.
2. **scrSignLetters** (Firma Carta): Placeholder - Nội dung và chức năng chưa được quyết định.
3. **scrDashboard** (Admin Only): Placeholder - Nội dung và chức năng chưa được quyết định.

### 2.3. Components Tái sử dụng
- **cmpPrimaryButton**: Nút hành động chính (Submit, Approve).
- **cmpSecondaryButton**: Nút hành động phụ (Cancel, Clear).
- **cmpSectionTitle**: Tiêu đề section với separator.
- **cmpCriteria**: Hiển thị câu hỏi đánh giá và tính điểm.
- **cmpObservationBox**: Ô nhập liệu nhận xét (multiline text).


---

## 3. Kết nối Dữ liệu (Data Connections)

### 3.1. SharePoint Lists (Primary Data Source)

#### 3.1.1. ongoingEvaluations (Ongoing Evaluation List)

**Identification & Contract Info**:
- `Title` (String): Item ID
- `documento_compras` (String): Contract ID
- `fecha_evaluacion` (Date): Evaluation date
- `nombre_proveedor` (String): Supplier name
- `sociedad` (String): Company code
- `fecha_inicio_validez` (Date)
- `fecha_fin_validez` (Date)
- `valor_contrato` (String)
- `consecutivo_evaluacion` (String)

**Responsible Persons**:
- `email_responsable_tecnico` (String)
- `nombre_responsable_tecnico` (String)
- `email_responsable_sst` (String)
- `nombre_responsable_sst` (String)
- `email_responsable_ma` (String)
- `nombre_responsable_ma` (String)
- `email_jefe_area` (String): Approver email
- `nombre_jefe_area` (String)

**Responsibility Flags**:
- `responsable_sst_responde` (Boolean)
- `responsable_ma_responde` (Boolean)

**Evaluation Scores & Answers**:
- **Calidad**: `calidad_pregunta_1`...`5`, `calificacion_criterio_calidad`
- **Oportunidad**: `oportunidad_pregunta_1`, `calificacion_criterio_oportunidad`
- **Gestión**: `gestion_pregunta_1`...`2`, `calificacion_criterio_gestion`
- **SST**: `sst_pregunta_1`, `calificacion_criterio_sst`
- **MA**: `ma_pregunta_1`, `calificacion_criterio_ma`
- **Ethics**: `cumple_codigo_etica` (Boolean)

**Observations**:
- `observaciones_tecnico` (Multiline)
- `observaciones_sst` (Multiline)
- `observaciones_ma` (Multiline)
- `observaciones_jefe_area` (Multiline)

**Totals & Status**:
- `puntaje_subtotal` (Int)
- `puntaje_total` (Int)
- `aprobada` (Boolean)
- `rechazada` (Boolean)
- `numero_rechazos` (Int)

**Robot Outputs**:
- `url_csv`, `url_carta`, `url_carta_firmada`
- `firma_responsable_tecnico`, `firma_jefe_area`

#### 3.1.2. completedEvaluations (Completed Evaluation List)  
**Tên biến trong App**: `completedEvaluations` hoặc `Completed Evaluation List`
**Mục đích**: Lưu trữ các đánh giá đã hoàn tất (archived)
**Cấu trúc**: **Giống hệt** ongoingEvaluations
**Di chuyển từ ongoing**: Tự động bởi Robot 2 hoặc Power Automate Flow sau khi ký xong

**Lưu ý quan trọng về Permissions**: 
App **KHÔNG sử dụng** bảng UserRoles riêng. Phân quyền được quản lý trực tiếp thông qua các trường email trong mỗi evaluation record:
- `email_responsable_tecnico`: Người có quyền đánh giá Técnico
- `email_responsable_sst`: Người có quyền đánh giá SST (nếu flag ON)
- `email_responsable_ma`: Người có quyền đánh giá MA (nếu flag ON)  
- `email_jefe_area`: Người có quyền approve

### 3.2. Kết nối qua Environment Variables (Recommended)
Để dễ dàng chuyển môi trường (DEV → TEST → PROD), nên sử dụng Environment Variables:

1. Tạo Environment Variables trong Power Platform Solution:
   - `OngoingEvaluationListUrl`
   - `CompletedEvaluationListUrl`

2. Kết nối trong Power Apps:
   ```powerfx
   // Trong App.OnStart hoặc Screen.OnVisible
   Set(varOngoingList, EnvironmentVariable("OngoingEvaluationListUrl"));
   ```

---

## 4. Phân Quyền và Bảo Mật (Security & Permissions)

### 4.1. Email-Based Access Control
App sử dụng email người dùng (`User().Email`) để xác định quyền **trực tiếp từ evaluation record**, KHÔNG cần bảng UserRoles riêng.

**Logic phân quyền**:
```powerfx
// Lấy email user hiện tại
Set(varUserEmail, User().Email);

// Kiểm tra quyền dựa trên email fields trong evaluation
Set(varCanEvaluateTecnico, 
    varSelectedEvaluation.email_responsable_tecnico = varUserEmail
);

Set(varCanEvaluateSST,
    (varSelectedEvaluation.responsable_sst_responde && 
     varSelectedEvaluation.email_responsable_sst = varUserEmail) ||
    (!varSelectedEvaluation.responsable_sst_responde && varCanEvaluateTecnico)
);

Set(varCanEvaluateMA,
    (varSelectedEvaluation.responsable_ma_responde && 
     varSelectedEvaluation.email_responsable_ma = varUserEmail) ||
    (!varSelectedEvaluation.responsable_ma_responde && varCanEvaluateTecnico)
);

Set(varCanApprove,
    varSelectedEvaluation.email_jefe_area = varUserEmail
);
```

| Role | Màn hình được phép truy cập | Chức năng |
|------|----------------------------|-----------|
| **Responsable Técnico** | scrEvaluateProviders | Đánh giá Technico Section; Đánh giá SST/MA nếu không có người chuyên trách |
| **Responsable SST** | scrEvaluateProviders | Đánh giá SST Section (nếu `responsable_sst_responde = true`) |
| **Responsable MA** | scrEvaluateProviders | Đánh giá MA Section (nếu `responsable_ma_responde = true`) |
| **Aprobador** | scrApprover | Phê duyệt/Từ chối đánh giá |
| **Firmante** | scrSignLetters | Ký điện tử thư |

### 4.2. Data-Level Security
- Mỗi user chỉ thấy **đánh giá được gán cho mình** thông qua filter:
  ```powerfx
  Filter(
      OngoingEvaluations,
      (email_responsable_tecnico = User().Email ||
      email_responsable_sst = User().Email ||
      email_responsable_ma = User().Email ||
      email_jefe_area = User().Email) &&
      (aprobada = false && rechazada = false)
  )
  ```

### 4.3. Section-Level Permissions (Flag-Based)
Quyền truy cập từng section được xác định bởi **responsibility flags**:

| Section | Visible & Editable khi |
|---------|------------------------|
| **Technico** | `email_responsable_tecnico = User().Email` |
| **SST** | (`responsable_sst_responde = true` AND `email_responsable_sst = User().Email`) OR (`responsable_sst_responde = false` AND `email_responsable_tecnico = User().Email`) |
| **MA** | (`responsable_ma_responde = true` AND `email_responsable_ma = User().Email`) OR (`responsable_ma_responde = false` AND `email_responsable_tecnico = User().Email`) |

### 4.3. SharePoint Security
- Áp dụng SharePoint permissions ở cấp Site/List
- Power App sử dụng **Delegated Authentication** (user's identity)

---

## 5. Giải Pháp Triển Khai (Deployment Strategy)

### 5.1. Solution Structure
App nên được đóng gói trong **Power Platform Solution**:
```
A006_EvaluacionProveedores_Solution/
├── Power Apps (Canvas)
│   └── A006_EvaluacionProveedores.msapp
├── Environment Variables
│   ├── OngoingEvaluationListUrl
│   └── CompletedEvaluationListUrl
├── Connections
│   └── SharePoint Connection
└── Dependencies
    └── (None or UiPath connector if needed)
```

### 5.2. Deployment Steps

#### 5.2.1. Development Environment
1. Tạo SharePoint Lists với cấu trúc đã định nghĩa
2. Develop Power App trong Power Apps Studio
3. Test với dữ liệu mẫu
4. Export App as **Unmanaged Solution**

#### 5.2.2. Test/Staging Environment
1. Import Solution (Unmanaged)
2. Update Environment Variables với URLs mới
3. Verify connections
4. User Acceptance Testing (UAT)

#### 5.2.3. Production Environment
1. Export Solution as **Managed Solution**
2. Import to Production
3. Configure Environment Variables
4. Assign security roles
5. Monitor and support

### 5.3. Version Control
- **Power Apps Source Control**: Sử dụng `.msapp` unpacking hoặc Power Apps Git integration
- **Solution Versioning**: Increment version (e.g., 1.0.0.0 → 1.1.0.0) khi có thay đổi

---

## 6. Tích Hợp với UiPath Robots

### 6.1. Robot 1 - Khởi tạo Đánh giá
**Trigger**: Tự động theo lịch (hàng tháng/quý)
**Nhiệm vụ**:
1. Tạo record mới trong `Ongoing Evaluation List`
2. Gán email người đánh giá dựa trên master data
3. Gửi email thông báo

**Tương tác với App**:
- Robot tạo data → App đọc và hiển thị trong dropdown

### 6.2. Robot 2 - Xử Lý Kết Quả
**Trigger**: Khi đánh giá được phê duyệt (`aprobada = true`)
**Nhiệm vụ**:
1. Đọc kết quả từ SharePoint
2. Tạo file CSV
3. Tạo thư đánh giá (Word/PDF)
4. Upload file và cập nhật trạng thái

**Tương tác với App**:
- App cập nhật `aprobada = true` → Trigger Robot 2
- Robot 2 upload letter → App hiển thị trong scrSignLetters

---

## 7. User Experience (UX) Design

### 7.1. Nguyên tắc thiết kế
- **Consistency**: Header và navigation giống nhau trên tất cả screens
- **Simplicity**: Chỉ hiển thị sections user có quyền
- **Responsive**: Hoạt động tốt trên laptop, tablet, iPad

### 7.2. Color Palette
- **Primary Blue**: `#003087` (Main brand color)
- **Accent Blue**: `#009AFE` (Hover state)
- **Accent Orange**: `#FF6A00` (Highlights/Decorations)
- **Neutral Gray**: `#767676` (Secondary actions)
- **Background**: `#F8F9FA` (Light gray)
- **Text**: `#003087` (Primary text)

### 7.3. Typography
- **Titles**: `AmsiPro` (Bold, #003087)
- **Body**: `Roboto` (Regular, #003087)
- **Buttons**: `Roboto` (Bold, White text)

---

## 8. Performance & Scalability

### 8.1. Data Loading Strategy
- Sử dụng `ClearCollect` để cache data cần thiết trong `App.OnStart`
- Filter client-side khi có thể để giảm delegation warnings

### 8.2. Delegation Best Practices
- Chỉ filter trên các cột **delegable** (email, choice, number)
- Tránh dùng `CountRows` trên large datasets (> 2000 records)

### 8.3. Expected Load
- **Concurrent Users**: 20-50 users
- **Records per User**: 5-20 pending evaluations
- **Total Records**: < 1000 ongoing evaluations tại một thời điểm

---

## 9. Testing & Quality Assurance

### 9.1. Test Scenarios
1. **Functional Testing**:
   - User login và xem đúng evaluations của mình
   - Submit evaluation thành công
   - Approval/Rejection workflow
   - Signature capture

2. **Security Testing**:
   - User không thấy evaluations của người khác
   - Role-based screen access

3. **Integration Testing**:
   - SharePoint connection
   - Robot trigger khi approval

### 9.2. Test Data
- Tạo test users với các roles khác nhau
- Tạo sample evaluations ở các trạng thái khác nhau

---


