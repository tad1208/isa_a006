# A006 - Power App: Provider Evaluation (Solution Design Document)

Tài liệu thiết kế chi tiết cho **Power App**, tổng hợp từ Source Code (`07_CODES/a006_app`) và các hướng dẫn phát triển.

## 1. Tổng quan (Overview)

Ứng dụng **Provider Evaluation** là cổng giao tiếp chính cho người dùng (Técnico, SST, MA, Jefe Area) để thực hiện đánh giá nhà cung cấp.
*   **Platform**: Canvas App.
*   **Data Source**: SharePoint Online.
*   **Đối tượng sử dụng**:
    *   **Técnico**: Đánh giá chính (Calidad, Oportunidad, Gestión).
    *   **SST/MA**: Đánh giá chuyên môn (An toàn/Môi trường) nếu được yêu cầu.
    *   **Jefe Area**: Phê duyệt kết quả.
    *   **Admin**: Theo dõi dashboard.

## 2. Kiến trúc Ứng dụng (App Architecture)

Ứng dụng gồm 3 màn hình chính và các Global Variables điều khiển logic.

### 2.1. Cấu trúc Màn hình (Screens)

| Tên màn hình | Chức năng chính | Ghi chú |
| :--- | :--- | :--- |
| **scrLanding** | Trang chủ, hiển thị Lời chào và Thống kê nhanh (My Pending Tasks). | Entry Point. Điều hướng người dùng dựa trên vai trò. |
| **scrEvaluaciones** | Form đánh giá chi tiết. | Màn hình phức tạp nhất. Chứa logic hiển thị động (Dynamic UI) cho từng Role. |
| **scrDashboard** | Bảng tổng hợp trạng thái (cho Admin/Manager). | Grid View, Search, Filter, Auto-numbering. |

### 2.2. Global Variables (`App.OnStart`)

*   `gblUserEmail`: Email người dùng hiện tại (`User().Email`).
*   `varIsAdmin`: Biến boolean kiểm tra quyền Admin (`gblUserEmail = "admin..."`).
*   `colUserRole`: Collection lưu vai trò của User (nếu cần phân quyền phức tạp hơn).

---

## 3. Thiết kế Chi tiết Màn hình

### 3.1. scrLanding (Home)
*   **Components**:
    *   `conHeader`: Logo, User Info.
    *   `galPendingTasks`: Gallery hiển thị các task đang chờ xử lý của User (Lọc từ SP List).
        *   *Filter Logic*: `Status='Pending' && (Evaluator=Me || Approver=Me)`.
    *   `btnStart`: Nút điều hướng sang `scrEvaluaciones`.

### 3.2. scrEvaluaciones (Core Logic)
Đây là màn hình "All-in-One" xử lý toàn bộ quy trình nhập liệu.

*   **Logic Sidebar (Navigation)**:
    *   Danh sách bên trái (`ddlSelectEvaluation`) tự động lọc ra các đánh giá liên quan đến User.
    *   Ưu tiên hiển thị các đánh giá chưa hoàn thành (`!submitted`).

*   **Logic Phân quyền (Section Visibility & Editability)**:
    *   Sử dụng biến cục bộ (`varCanEditTecnico`, `varCanEditSST`, `varCanEditMA`) set tại `OnChange` của Sidebar.
    *   **Técnico Section**: Enable nếu `User=Tecnico` AND `!TecnicoSubmitted`.
    *   **SST Section**: Enable nếu `User=SST` AND `!SSTSubmitted`.
    *   **MA Section**: Enable nếu `User=MA` AND `!MASubmitted`.
    *   **Approval Section** (Footer): Enable nếu `User=Jefe` AND `AllSubmitted=True`.

*   **Data Action (Submit)**:
    *   Dùng hàm `Patch` cập nhật từng phần dữ liệu vào SharePoint List `ongoingEvaluations`.
    *   Cờ trạng thái (`tecnico_submitted`, `sst_submitted`...) được bật `true` sau khi submit thành công.

### 3.3. scrDashboard (Monitoring)
*   **View**: Dành cho Admin hoặc User muốn tra cứu lịch sử.
*   **Security**:
    *   Kiểm tra `varIsAdmin` ngay khi vào màn hình.
    *   Ẩn toàn bộ nội dung nếu không có quyền.
*   **Features**:
    *   **Auto-Numbering**: Tự động đánh số thứ tự (1, 2, 3...) cho kết quả tìm kiếm bằng Collection.
    *   **Status Indicators**:
        *   Dùng Label **"✓"** (Xanh) cho Completed.
        *   Dùng Label **"-"** (Xám) cho Pending.
        *   Hiển thị trạng thái duyệt: **APROBADO** / **RECHAZADO**.

## 4. Mô hình Dữ liệu (Data Model)

### SharePoint List: `ongoingEvaluations`
Chứa toàn bộ dữ liệu giao dịch.

*   **Thông tin chung**: `Title` (EvaluationID), `documento_comprass`, `proveedor`, `sociedad`.
*   **Người phụ trách**: `email_responsable_tecnico`, `email_responsable_sst`, `email_responsable_ma`, `email_jefe_area`.
*   **Điểm số (Input)**:
    *   `calidad_pregunta_1`...`5`
    *   `oportunidad_pregunta_1`
    *   `gestion_pregunta_1`...`2`
    *   `sst_pregunta_1`
    *   `ma_pregunta_1`
*   **Trạng thái (Flags)**:
    *   `tecnico_submitted` (Y/N)
    *   `sst_submitted` (Y/N)
    *   `ma_submitted` (Y/N)
    *   `aprobada` (Y/N), `rechazada` (Y/N)
*   **Chữ ký (Signatures)**:
    *   `firma_responsable_tecnico` (Base64/Link)
    *   `firma_jefe_area` (Base64/Link)

## 5. UI/UX Standards
*   **Colors**:
    *   Primary: Dark Blue (Header).
    *   Background: Light Gray (`RGB(248,249,250)`).
    *   Status Green: `Color.Green` (Success).
    *   Status Red: `Color.Red` (Error/Reject).
*   **Fonts**: Segoe UI (Standard Power Apps font).
