# scrLanding - Màn hình Khởi tạo (Landing Screen)

## 1. Thông tin Chung (General Information)

**Tên màn hình**: `scrLanding`  
**Mục đích**: Màn hình khởi tạo của ứng dụng, hiển thị thông tin tổng quan và điều hướng người dùng đến các chức năng chính.  
**Người dùng**: Tất cả users có quyền truy cập app

---

## 2. Giao Diện UI (User Interface)

### 2.1. Layout Tổng Quan

```
┌─────────────────────────────────────────────────────┐
│ [LOGO]  [Evaluar] [Aprobar] [Firmar] [Dashboard]  │ ← Header
├─────────────────────────────────────────────────────┤
│                                                     │
│         Welcome Message                             │
│         "Bienvenido [Name],                        │
│          tienes X evaluaciones pendientes"         │
│                                                     │
│   ┌───────────────────────────────────────────┐   │
│   │   Pending Tasks Gallery (Optional)        │   │
│   │   - Task 1                                 │   │
│   │   - Task 2                                 │   │
│   └───────────────────────────────────────────┘   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### 2.2. Chi Tiết Components

#### 2.2.1. Header (con_Header)
- **Type**: Container (Reusable từ scrEvaluateProviders)
- **Position**: X=0, Y=0
- **Dimensions**: Width=Parent.Width, Height=80
- **Styling**:
  - Fill: White `RGBA(255,255,255,1)`
  - BorderThickness: 0, 0, 2, 0 (chỉ viền dưới)
  - BorderColor: `RGBA(0,134,208,1)`

**Components bên trong**:
- **imgLogo**: ISA INTERVIAL logo (trái)
- **con_Navigation**: Horizontal Container chứa 4 `cmpHeaderButton`
  - `cmpHB_Evaluate`: "Evaluar Proveedores"
  - `cmpHB_Approve`: "Aprobar evaluaciones"
  - `cmpHB_Sign`: "Firmar cartas"
  - `cmpHB_Dashboard`: "Dashboard (Solo admin)"

#### 2.2.2. Welcome Message (lbl_Welcome)
- **Type**: Label
- **Position**: X=0, Y=150
- **Dimensions**: Width=Parent.Width, Height=60
- **Text**:
  ```powerfx
  "Bienvenido " & varUserName & ", tienes " & 
  varPendingCount & " evaluaciones pendientes para completar"
  ```
- **Styling**:
  - Font: Segoe UI
  - Size: 24
  - FontWeight: Bold
  - Color: `RGBA(0,51,102,1)` (Dark Blue)
  - Align: Center

#### 2.2.3. Pending Tasks Gallery (gal_PendingTasks) - Optional
- **Type**: Vertical Gallery (Blank)
- **Visible**: `varPendingCount > 0`
- **Position**: X=(Parent.Width - Self.Width)/2, Y=230
- **Dimensions**: 
  - Width= `Min(1000, Parent.Width - 100)`
  - Height= `Parent.Height - Self.Y - 50`
- **Items**:
  ```powerfx
  Filter(
      'Ongoing Evaluation List',
      (email_responsable_tecnico = varUserEmail || 
       email_jefe_area = varUserEmail) &&
      aprobada = false
  )
  ```

**Template (mỗi item)**:
- **con_CardBG** (Container): Nền trắng, border, shadow
- **lbl_ContractId**: Hiển thị `ThisItem.documento_compras`
- **lbl_ProviderName**: Hiển thị `ThisItem.nombre_proveedor`
- **lbl_Status**: "Pendiente" (màu cam)
- **icon_ChevronRight**: Mũi tên điều hướng
  - OnSelect:
    ```powerfx
    Set(varSelectedEvaluation, ThisItem);
    Navigate(scrEvaluateProviders, ScreenTransition.Fade)
    ```

---

## 3. Logic và Behavior (Functionality)

### 3.1. OnVisible
```powerfx
// Lấy thông tin user
Set(varUserEmail, User().Email);
Set(varUserName, User().FullName);

// Đếm số đánh giá chờ (chỉ từ ongoingEvaluations)
Set(
    varPendingCount,
    CountRows(
        Filter(
            ongoingEvaluations,
            email_responsable_tecnico = varUserEmail ||
            email_responsable_sst = varUserEmail ||
            email_responsable_ma = varUserEmail ||
            email_jefe_area = varUserEmail
        )
    )
);

// Set current screen cho header highlighting
Set(gblCurrentScreen, scrLanding);
```

### 3.2. User Permissions Logic
Landing screen không enforce permissions cụ thể. Số lượng pending tasks hiển thị phụ thuộc vào:
- **Evaluations assigned to user**: Email match với `email_responsable_tecnico`, `email_responsable_sst`, hoặc `email_responsable_ma`
- **Evaluations pending approval**: Email match với `email_jefe_area`

**Quan trọng**: App **KHÔNG sử dụng** bảng UserRoles. Permission được quản lý trực tiếp qua email fields trong evaluation records.

### 3.3. Navigation
- Từ header buttons → Navigate đến các screens tương ứng
- Từ pending tasks gallery → Navigate đến scrEvaluateProviders với evaluation đã chọn

---

## 4. Data Connections

### 4.1. Data Sources
- **ongoingEvaluations** (SharePoint): Đọc để đếm và hiển thị pending tasks
- **User()**: Built-in Power Apps function để lấy user info

**Lưu ý**: KHÔNG cần kết nối với completedEvaluations ở landing screen.

### 4.2. Variables
| Variable | Type | Purpose |
|----------|------|---------|
| `varUserEmail` | Text | Email user hiện tại |
| `varUserName` | Text | Tên đầy đủ user |
| `varPendingCount` | Number | Số lượng evaluations chờ |
| `varSelectedEvaluation` | Record | Evaluation được chọn từ gallery |
| `gblCurrentScreen` | Screen | Screen hiện tại (cho header highlighting) |

---

## 5. Responsive Design

### 5.1. Breakpoints
- **Desktop** (Width >= 1024px): Full layout với gallery
- **Tablet** (768px - 1023px): Gallery thu nhỏ
- **Mobile** (< 768px): Chỉ hiển thị message, ẩn gallery (navigate từ header)

### 5.2. Adaptive Elements
```powerfx
// Gallery visible chỉ khi không phải mobile
gal_PendingTasks.Visible = Parent.Width >= 768 && varPendingCount > 0
```

---

## 6. Testing Scenarios

### 6.1. Scenario 1: User có pending evaluations (as Responsable Técnico)
**Given**: User email = "juan.perez@isa.com", có 3 evaluations với `email_responsable_tecnico = "juan.perez@isa.com"`  
**When**: User mở app  
**Then**: 
- Message hiển thị "tienes 3 evaluaciones pendientes"
- Gallery hiển thị 3 items
- Click vào item → Navigate đến scrEvaluateProviders

### 6.2. Scenario 2: User là Approver có evaluations chờ duyệt
**Given**: User email = "maria.gonzalez@isa.com", có 2 evaluations với `email_jefe_area = "maria.gonzalez@isa.com"`, `aprobada = false`  
**When**: User mở app  
**Then**: 
- Message hiển thị "tienes 2 evaluaciones pendientes"
- Gallery hiển thị 2 items (có thể route đến scrApprover)

### 6.3. Scenario 3: User không có pending tasks
**Given**: User không có evaluations nào (email không match với bất kỳ record nào)  
**When**: User mở app  
**Then**: 
- Message hiển thị "tienes 0 evaluaciones pendientes"
- Gallery không hiển thị hoặc hiển thị empty state

### 6.4. Scenario 4: Header navigation
**Given**: User đang ở Landing screen  
**When**: User click "Evaluar Proveedores" button  
**Then**: Navigate đến scrEvaluateProviders

---

## 7. Known Issues & Limitations

### 7.1. Delegation Warning
- `CountRows` với Filter phức tạp có thể gây delegation warning
- **Impact**: Nếu user có > 2000 pending evaluations, count có thể không chính xác
- **Mitigation**: Trong thực tế, rất hiếm user có > 100 pending, nên có thể bỏ qua

### 7.2. Performance
- Nếu Ongoing Evaluation List có nhiều records (> 5000), gallery load có thể chậm
- **Mitigation**: Thêm pagination hoặc lazy loading

---

## 8. Future Enhancements

1. **Quick Actions**: Thêm nút "Start Evaluation" ngay trong gallery
2. **Filters**: Cho phép filter tasks theo provider, date, status
3. **Charts**: Mini chart hiển thị trend số lượng evaluations theo tháng
4. **Notifications**: Badge hiển thị notification count trên header buttons
