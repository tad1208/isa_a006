# Hướng dẫn Xây dựng Màn hình Dashboard (scrDashboard)

## Tóm tắt (Summary)

*   **Mục đích**: `scrDashboard` là màn hình tổng quan giúp theo dõi trạng thái của các đánh giá (Evaluations) đang diễn ra.
*   **Dữ liệu**: Lấy từ SharePoint List `ongoingEvaluations`.
*   **Hiển thị**: Dạng bảng (Table/Gallery) liệt kê các item với các cột trạng thái chi tiết (Submitted, Signed, Approved).

---

## 1. Thiết lập Màn hình (Screen Setup)

### 1.1. Tạo Screen
1.  **New Screen** → **Blank** (hoặc duplicate `scrLanding` để lấy Header/Menu sẵn).
2.  Đổi tên: `scrDashboard`.
3.  **Fill**: `RGBA(248, 249, 250, 1)` (Màu nền xám nhẹ).

### 1.2. Thiết lập App.OnStart (Global Setup)
Thay vì kiểm tra Navigate ở màn hình, ta thiết lập quyền ngay khi App khởi động.

```powerfx
// App.OnStart
Set(gblUserEmail, User().Email);
// Check if user is Admin
Set(varIsAdmin, gblUserEmail = "admin.email@example.com"); 
```

### 1.3. OnVisible Logic
```powerfx
// Chỉ tải lại dữ liệu khi vào màn hình
Refresh(ongoingEvaluations);
```

---

## 2. Layout Chính (Security UI)

Tạo một **Container** chính (`conDashboard`) bao bọc toàn bộ nội dung (Header + Gallery) để ẩn nội dung đối với user thường.

*   **Name**: `conDashboard`
*   **Visible**: `varIsAdmin`  <--- Chỉ hiện thị nếu là Email Admin
*   **Width**: Parent.Width
*   **Height**: Parent.Height
*   **X**: 0, **Y**: 0

*Lưu ý: Bạn có thể thêm một Label "Access Denied" nằm ngoài `conDashboard` và có `Visible = !varIsAdmin` để báo cho người dùng biết.*

Chúng ta sẽ sử dụng một **Vertical Gallery** để giả lập một bảng dữ liệu (Data Grid/Table) vì nó linh hoạt hơn Data Table control mặc định.

### 2.1. Header Row (Tiêu đề cột)
Tạo một **Container** ngang (`conGridHeader`) đặt phía trên Gallery để làm tiêu đề.

*   **Width**: Parent.Width
*   **Height**: 50
*   **Fill**: `Color.DarkBlue` (hoặc màu thương hiệu)
*   **Cấu trúc Labels** (Sắp xếp ngang, Width tuỳ chỉnh cho cân đối):
    1.  **Documento** (Width: 150)
    2.  **Fecha** (Width: 100)
    3.  **Sociedad** (Width: 100)
    4.  **Proveedor** (Width: 200)
    5.  **Tecnico Sub.** (Width: 80)
    6.  **SST Sub.** (Width: 80)
    7.  **MA Sub.** (Width: 80)
    8.  **Aprobación** (Width: 120)
    9.  **Firma Técnico** (Width: 100)
    10. **Firma Jefe** (Width: 100)

### 2.2. Gallery (`galDashboard`)

*   **Insert**: Vertical Gallery.
*   **Items**: `ongoingEvaluations` (Có thể thêm SortByColumns nếu cần, ví dụ sort theo ngày tạo mới nhất: `SortByColumns(ongoingEvaluations, "Created", SortOrder.Descending)`).
*   **Layout**: Blank.

#### Thiết kế Item trong Gallery (TemplateCell)
Trong `galDashboard`, thêm các control tương ứng với cột Header. Đặt tất cả trong một Horizontal Container hoặc sắp xếp toạ độ X khớp với Header.

1.  **lblDocumento**
    *   **Text**: `ThisItem.documento_comprass` (hoặc `ThisItem.Title`)
    *   **Width**: 150
2.  **lblFecha**
    *   **Text**: `Text(ThisItem.fecha_evaluacion, "dd/mm/yyyy")`
    *   **Width**: 100
3.  **lblSociedad**
    *   **Text**: `ThisItem.sociedad` (hoặc `ThisItem.field_sociedad`)
    *   **Width**: 100
4.  **lblProveedor**
    *   **Text**: `ThisItem.proveedor` (hoặc `ThisItem.Title` nếu tên NCC nằm ở Title)
    *   **Width**: 200
    
    *(Các cột trạng thái bên dưới nên dùng **Icon** để trực quan hơn)*

5.  **lblTecnicoSub** (Tecnico Submitted)
    *   **Text**: `If(ThisItem.tecnico_submitted, "✓", "-")`
    *   **Color**: `If(ThisItem.tecnico_submitted, Color.Green, Color.LightGray)`
    *   **Size**: 14 (Bold)
    *   **TextAlignment**: `Center`
    *   **Tooltip**: "Tecnico Submitted"
    *   **Width**: 80
6.  **lblSSTSub** (SST Submitted)
    *   **Text**: `If(ThisItem.sst_submitted, "✓", "-")`
    *   **Color**: `If(ThisItem.sst_submitted, Color.Green, Color.LightGray)`
    *   **Size**: 14 (Bold)
    *   **TextAlignment**: `Center`
    *   **Width**: 80
7.  **lblMASub** (MA Submitted)
    *   **Text**: `If(ThisItem.ma_submitted, "✓", "-")`
    *   **Color**: `If(ThisItem.ma_submitted, Color.Green, Color.LightGray)`
    *   **Size**: 14 (Bold)
    *   **TextAlignment**: `Center`
    *   **Width**: 80

8.  **lblApproval** (Jefe Area Rejected/Approved)
    *   **Text**: 
        ```powerfx
        If(ThisItem.aprobada, "APROBADO", 
           If(ThisItem.rechazada, "RECHAZADO", "PENDIENTE")
        )
        ```
    *   **Color**: 
        ```powerfx
        If(ThisItem.aprobada, Color.Green, 
           If(ThisItem.rechazada, Color.Red, Color.LightGray)
        )
        ```
    *   **Width**: 120

9.  **lblTecnicoSigned** (Tecnico Signed)
    *   **Text**: `If((ThisItem.firma_responsable_tecnico), "✓", "-")`
    *   **Color**: `If((ThisItem.firma_responsable_tecnico), Color.Green, Color.LightGray)`
    *   **Size**: 14 (Bold)
    *   **TextAlignment**: `Center`
    *   **Width**: 100

10. **lblJefeSigned** (Jefe Area Signed)
    *   **Text**: `If((ThisItem.firma_jefe_area), "✓", "-")`
    *   **Color**: `If((ThisItem.firma_jefe_area), Color.Green, Color.LightGray)`
    *   **Size**: 14 (Bold)
    *   **TextAlignment**: `Center`
    *   **Width**: 100

---

## 3. Các tính năng mở rộng (Optional)



### 3.2. Filter / Search
*   Thêm Text Input `txtSearch` phía trên Gallery.
*   Sửa items của Gallery:
    ```powerfx
    Search(ongoingEvaluations, txtSearch.Text, "Title", "proveedor", "documento_comprass")
    ```

### 3.3. Filter theo trạng thái "My Pending Actions"
*   Có thể thêm tab hoặc checkbox để lọc ra các item mà user hiện tại cần xử lý (logic tương tự như sidebar ở màn hình Evaluaciones).
