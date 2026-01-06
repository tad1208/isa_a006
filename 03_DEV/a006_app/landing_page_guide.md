# Hướng dẫn Xây dựng Landing Page (Power Apps)

Tài liệu này hướng dẫn chi tiết cách xây dựng màn hình **Landing Page** (`scrLanding`) cho ứng dụng "Evaluación de Proveedores" dựa trên thiết kế đã thống nhất.

**Tài nguyên tham khảo:**
- **Design Spec**: `design/a006_app/scr_landing.md`
- **General Overview**: `design/a006_app/a006_app_general.md`
- **SharePoint Schema**: `design/a006_app/sharepoint_lists_schema.md`

---

## 0. Kết nối Dữ liệu (Quan trọng)

> [!IMPORTANT]
> Trước khi code, bạn cần kết nối SharePoint Lists vào App

### 0.1. Kết nối SharePoint

1. Vào tab **Data** (bên trái Power Apps Studio)
2. Chọn **Add data** → Tìm **SharePoint**
3. Chọn SharePoint site của bạn
4. Kết nối **2 lists sau** (thông qua Environment Variables):
   - `ongoingEvaluations` - Đánh giá đang thực hiện
   - `completedEvaluations` - Đánh giá đã hoàn tất (optional cho landing page)

> [!TIP]
> **Sử dụng Environment Variables (Recommended)**:
> Nếu có Environment Variable `Ongoing Evaluations List`, dùng nó để dễ deploy giữa DEV/TEST/PROD

### 0.2. Verify Connection

Sau khi kết nối, trong tab Data bạn sẽ thấy:
- `ongoingEvaluations` (tên DataSource sẽ match với Env Var name)
- Xem preview data để đảm bảo có đúng columns: `email_responsable_tecnico`, `email_responsable_sst`, `email_responsable_ma`, `email_jefe_area`

---

## 1. Thiết lập Màn hình (Screen Setup)

### 1.1. Tạo màn hình mới

1. **Tree View** → **New Screen** → **Blank**
2. Đổi tên: `scrLanding`
3. **Fill**: `RGBA(248, 249, 250, 1)` - Nền xám nhạt

### 1.2. OnVisible Logic

```powerfx
// scrLanding.OnVisible

// 1. Get user info from global variables (set in App.OnStart)
Set(varUserEmail, gblUserEmail);
Set(varUserName, gblUserName);

// 2. Count pending evaluations for this user
Set(
    varPendingCount,
    CountIf(
        ongoingEvaluations,
        (
            // 1. Técnico: sees rejected items OR items they haven't submitted yet
            (email_responsable_tecnico = varUserEmail && !tecnico_submitted) ||
            
            // 2. SST: sees non-rejected items where they are responsible and haven't submitted
            (email_responsable_sst = varUserEmail && responsable_sst_responde && !sst_submitted) ||
            
            // 3. MA: sees non-rejected items where they are responsible and haven't submitted
            (email_responsable_ma = varUserEmail && responsable_ma_responde && !ma_submitted) ||
            
            // 4. Approver: sees items ready for approval
            (
                email_jefe_area = varUserEmail && 
                !aprobada && !rechazada &&
                tecnico_submitted &&
                sst_submitted &&
                ma_submitted
            )
        )
    )
);

// 3. Set current screen (for header highlighting)
Set(gblCurrentScreen, scrLanding);
```

> [!NOTE]
> **Delegation Warning**: `CountRows` with `Filter` on SharePoint may show delegation warning.
> This is acceptable if your organization has < 2000 total evaluations.
> If concerned, use SharePoint views or move count logic to Power Automate.

---

## 2. Xây dựng Header (Navigation Bar)

> [!TIP]
> Header sẽ được tái sử dụng cho TẤT CẢ screens trong app. Build 1 lần, copy sang các screens khác.

### 2.1. Container Chính (Wrapper)

1. **Insert** → **Layout** → **Container**
2. Đổi tên: `conHeader`
3. **Thuộc tính**:
   - X: `0`, Y: `0`
   - Width: `Parent.Width`
   - Height: `100`
   - Fill: `ColorValue("#003087")` (Primary Blue)

### 2.2. Đường kẻ dưới (Bottom Border)

1. Chọn `conHeader` → **Insert** → **Shapes** → **Rectangle**
2. Đổi tên: `shpHeaderLine`
3. **Thuộc tính**:
   - X: `0`
   - Y: `Parent.Height - 2`
   - Width: `Parent.Width`
   - Height: `2`
   - Fill: `ColorValue("#FF6A00")` (Orange Accent)

### 2.3. Horizontal Container (Content)

1. Chọn `conHeader` → **Insert** → **Layout** → **Horizontal Container**
2. Đổi tên: `conHeaderContent`
3. **Thuộc tính**:
   - X: `0`, Y: `0`
   - Width: `Parent.Width`
   - Height: `Parent.Height - 2`
   - Fill: `RGBA(0, 0, 0, 0)` (Transparent)

4. **Layout Settings** (Properties panel bên phải):
   - **Justify**: `Space Between` (Logo trái, Menu phải)
   - **Align**: `Center` (Căn giữa vertical)
   - **Gap**: `20`
   - **PaddingLeft**: `40`, **PaddingRight**: `40`

### 2.4. Logo

1. Chọn `conHeaderContent` → **Insert** → **Media** → **Image**
2. Đổi tên: `imgLogo`
3. **Thuộc tính**:
   - Image: Upload logo "ISA INTERVIAL" (White version)
   - Width: `150`, Height: `50`
   - ImagePosition: `Fit`

### 2.5. Menu Navigation (Component-based)

#### Bước 2.5.1: Tạo Component `cmpHeaderButton`

1. Tab **Components** (cạnh Screens) → **New component**
2. Đổi tên: `cmpHeaderButton`
3. **Add Custom Properties**:

   **Property 1: Label**
   - Display name: `Label`
   - Property type: **Input**
   - Data type: **Text**

   **Property 2: Destination**
   - Display name: `Destination`
   - Property type: **Input**
   - Data type: **Screen**

   **Property 3: IsActive**
   - Display name: `IsActive`
   - Property type: **Input**
   - Data type: **Boolean**

4. **Design Component**:
   - Insert **Button** (Classic)
   - Đổi tên button: `btnNavButton`
   - **Thuộc tính**:
     ```powerfx
     // Text
     Text = cmpHeaderButton.Label

     // OnSelect
     OnSelect = Navigate(cmpHeaderButton.Destination, ScreenTransition.Fade)

     // Fill
     Fill = If(cmpHeaderButton.IsActive, 
               ColorValue("#009AFE"),   // Active: Accent Blue
               Transparent)             // Inactive

     // Color (text)
     Color = RGBA(255, 255, 255, 1)     // White Text

     // FontWeight
     FontWeight = FontWeight.Semibold

     // HoverFill
     HoverFill = ColorValue("#009AFE")  // Accent Blue

     // BorderStyle
     BorderStyle = BorderStyle.None

     // Radius (Pill Shape)
     RadiusTopLeft = 20
     RadiusTopRight = 20
     RadiusBottomLeft = 20
     RadiusBottomRight = 20

     // Width & Height
     Width = Parent.Width
     Height = Parent.Height
     ```

#### Bước 2.5.2: Sử dụng Component

1. Quay lại màn hình `scrLanding`
2. Chọn `conHeaderContent`
3. **Insert** → **Custom** → `cmpHeaderButton` (thêm 4 instances)

**Instance 1: Evaluar Proveedores**
```powerfx
Label = "Evaluar Proveedores"
Destination = scrEvaluaciones
IsActive = App.ActiveScreen.Name = "scrEvaluaciones"
```

**Instance 2: Aprobar evaluaciones**
```powerfx
Label = "Aprobar evaluaciones"
Destination = scrEvaluaciones
IsActive = App.ActiveScreen.Name = "scrEvaluaciones"
```

**Instance 3: Firmar cartas**
```powerfx
Label = "Firmar cartas"
Destination = scrSignLetters
IsActive = App.ActiveScreen.Name = "scrSignLetters"
```

**Instance 4: Dashboard (Solo admin)**
```powerfx
Label = "Dashboard"
Destination = scrDashboard
IsActive = App.ActiveScreen.Name = "scrDashboard"
```

> [!TIP]
> **Auto-highlighting**: Công thức `App.ActiveScreen.Name = "..."` đảm bảo nút tự động highlight khi user ở màn hình đó.

---

## 3. Main Content - Welcome Message

### 3.1. Welcome Label

1. **Insert** → **Text Label**
2. Đổi tên: `lblWelcome`
3. **Thuộc tính**:
   ```powerfx
   // Text
   Text = "Bienvenido " & varUserName & ", tienes " & 
          varPendingCount & " evaluaciones pendientes"

   // Position
   X = 0
   Y = 150  // Below header

   // Size
   Width = Parent.Width
   Height = 60

   // Styling
   Font = Font.'AmsiPro' (hoặc 'Segoe UI')
   Size = 24
   FontWeight = FontWeight.Bold
   Color = ColorValue("#003087")  // Primary Blue
   Align = Align.Center
   ```

---

## 4. Main Content - Pending Tasks Gallery (Optional)

> [!NOTE]
> Gallery này là **optional**. Nếu muốn simple, chỉ cần welcome message + navigation từ header.

### 4.1. Vertical Gallery

1. **Insert** → **Vertical Gallery** (Blank Vertical)
2. Đổi tên: `galPendingTasks`
3. **Thuộc tính**:
   ```powerfx
   // Items - Filter evaluations where user has permission (email + flag check)
   Items = Filter(
       ongoingEvaluations,
       (
           // Técnico - always included if assigned
           email_responsable_tecnico = varUserEmail ||
           
           // SST - only if flag is ON and email matches
           (responsable_sst_responde && email_responsable_sst = varUserEmail) ||
           
           // MA - only if flag is ON and email matches
           (responsable_ma_responde && email_responsable_ma = varUserEmail) ||
           
           // Jefe Area (Approver) - always included if assigned
           email_jefe_area = varUserEmail
       ) &&
       aprobada = false  // Only show pending evaluations
   )

   // Position
   X = (Parent.Width - Self.Width) / 2  // Center
   Y = lblWelcome.Y + lblWelcome.Height + 20

   // Size
   Width = Min(1000, Parent.Width - 100)
   Height = Parent.Height - Self.Y - 50

   // Template
   TemplateSize = 100
   TemplatePadding = 10

   // Visible only if has data
   Visible = varPendingCount > 0
   ```

### 4.2. Gallery Template Design

**Click vào biểu tượng bút chì** (Edit icon) trên gallery để sửa template.

#### 4.2.1. Card Background

1. **Insert Container** vào template
2. Đổi tên: `conCardBG`
3. **Thuộc tính**:
   ```powerfx
   X = 10
   Y = 5
   Width = Parent.TemplateWidth - 20
   Height = Parent.TemplateHeight - 10
   Fill = RGBA(255, 255, 255, 1)
   BorderColor = RGBA(230, 230, 230, 1)
   BorderThickness = 1
   DropShadow = DropShadow.Light
   RadiusTopLeft = 10
   RadiusTopRight = 10
   RadiusBottomLeft = 10
   RadiusBottomRight = 10
   ```

#### 4.2.2. Contract ID Label

1. **Insert Label** vào `conCardBG`
2. Đổi tên: `lblContractId`
3. **Thuộc tính**:
   ```powerfx
   Text = ThisItem.documento_compras
   X = 20
   Y = 15
   Width = 300
   Height = 30
   Size = 16
   FontWeight = FontWeight.Bold
   Color = ColorValue("#003087")  // Primary Blue
   ```

#### 4.2.3. Provider Name Label

1. **Insert Label**
2. Đổi tên: `lblProviderName`
3. **Thuộc tính**:
   ```powerfx
   Text = ThisItem.nombre_proveedor
   X = 20
   Y = 45
   Width = 400
   Height = 30
   Size = 14
   Color = ColorValue("#003087")  // Primary Blue
   ```

#### 4.2.4. Status Label

1. **Insert Label**
2. Đổi tên: `lblStatus`
3. **Thuộc tính**:
   ```powerfx
   Text = "Pendiente"
   X = Parent.Width - 150
   Y = 30
   Color = ColorValue("#FF6A00")  // Accent Orange
   FontWeight = FontWeight.Semibold
   ```

#### 4.2.5. Chevron Icon (Navigate)

1. **Insert** → **Icons** → **ChevronRight**
2. Đổi tên: `iconNavigate`
3. **Thuộc tính**:
   ```powerfx
   X = Parent.Width - 50
   Y = (Parent.Height - Self.Height) / 2
   Color = ColorValue("#003087")  // Primary Blue

   // OnSelect - Navigate to evaluation screen
   OnSelect = Set(varSelectedEvaluation, ThisItem);
              Navigate(scrEvaluaciones, ScreenTransition.Fade)
   ```

---

## 5. Copy Header sang các màn hình khác

### 5.1. Copy Process

1. Chọn `conHeader` ở màn hình `scrLanding`
2. **Ctrl + C** (Copy)
3. Sang màn hình khác (e.g., `scrEvaluaciones`)
4. **Ctrl + V** (Paste)
5. Đảm bảo vị trí X=0, Y=0

### 5.2. Auto-highlighting

Nhờ công thức `App.ActiveScreen.Name = "..."`, nút tương ứng sẽ **tự động sáng** ở mỗi màn hình mà không cần chỉnh sửa.

---

## 6. Testing Checklist

- [ ] Data connection: `ongoingEvaluations` đã kết nối
- [ ] OnVisible: Variables được set đúng (`varUserEmail`, `varPendingCount`)
- [ ] Welcome message: Hiển thị đúng tên user và số lượng pending
- [ ] Gallery: Hiển thị đúng evaluations của user (filter 4 email fields)
- [ ] Navigation: Click header buttons → chuyển màn hình
- [ ] Active state: Nút highlight đúng khi ở màn hình tương ứng
- [ ] Gallery OnSelect: Click item → Navigate đến evaluation screen với `varSelectedEvaluation` được set

---

## 7. Common Issues & Solutions

### 7.1. Delegation Warning

**Issue**: Yellow warning triangle với "CountRows operation not supported"

**Solution**: 
- Bỏ qua nếu user có < 100 evaluations
- Hoặc dùng SharePoint View với pre-filter

### 7.2. Gallery không hiển thị data

**Checklist**:
1. SharePoint connection đã đúng chưa?
2. Field names trong filter có match với SharePoint columns không?
3. User email có trong data không? (Test với email thật có trong SharePoint)
4. Filter logic có đúng không? (check `||` operators)

### 7.3. Header buttons không highlight

**Issue**: Nút không sáng khi ở màn hình đó

**Solution**:
- Check công thức `IsActive`: Phải dùng `App.ActiveScreen.Name = "exactScreenName"`
- **Lưu ý**: Tên màn hình phải CHÍNH XÁC (case-sensitive)

---

## 8. Next Steps

Sau khi hoàn thành Landing Page:

1. **Build scrEvaluaciones**: Màn hình Đánh giá & Phê duyệt (Hợp nhất)
   - Reference: `developement/a006_app/scrEvaluaciones_guide.md`
2. **Build scrSignLetters**: Màn hình ký điện tử
3. **Test end-to-end workflow**

---

## Appendix: Permission Logic

App **KHÔNG sử dụng** bảng UserRoles riêng. Permissions được quản lý **trực tiếp qua email fields** trong evaluation records:

| User Email Match | Màn hình thấy được |
|------------------|-------------------|
| `email_responsable_tecnico` | scrEvaluaciones (Técnico section) |
| `email_responsable_sst` | scrEvaluaciones (SST section) |
| `email_responsable_ma` | scrEvaluaciones (MA section) |
| `email_jefe_area` | scrEvaluaciones (Approval section) |

**Landing page**: Hiển thị TẤT CẢ evaluations mà user có liên quan (bất kỳ role nào).
