# Chi tiết Màn hình Landing Page

Tài liệu này mô tả chi tiết thiết kế và logic của màn hình **Landing Page** dựa trên hình ảnh mockup.

## 1. Bố cục chung (Layout)
Màn hình bao gồm một thanh tiêu đề (Header) cố định ở phía trên và khu vực nội dung chính (Main Content) ở phía dưới.

### 1.1. Header (Navigation Bar)
Thanh điều hướng nằm ngang ở trên cùng, chứa các phần tử sau:

*   **Logo**: Logo "isa INTERVIAL" nằm ở góc trái.
*   **Menu Navigation**: Các nút điều hướng chính (dạng thẻ/tab hoặc button):
    1.  **Evaluar Proveedores** (Đánh giá nhà cung cấp): Dành cho người đánh giá (Contract Owner/Admin).
    2.  **Aprobar evaluaciones** (Phê duyệt đánh giá): Dành cho cấp quản lý (Jefe de Área).
    3.  **Firmar cartas** (Ký thư): Dành cho người ký (Jefe de Área / Responsable Técnico).
    4.  **Dashboard? (Solo admin)**: Dashboard quản trị (chỉ dành cho Admin).

### 1.2. Main Content (Khu vực nội dung)
Hiện tại mockup đang để trắng với các ghi chú về logic hiển thị.

## 2. Logic & Chức năng (Functional Requirements)

Dựa trên các ghi chú trong mockup:

### 2.1. Phân quyền người dùng (User Permissions)
*   Ứng dụng cần sử dụng hàm `User()` của Power Apps kết hợp với dữ liệu đánh giá để xác định quyền hạn của người dùng hiện tại.
*   **Mục tiêu**: Xác định người dùng có thể làm gì (Do) và thấy gì (See).
    *   Ví dụ: Nếu là *Jefe de Área*, họ sẽ thấy tab "Aprobar evaluaciones". Nếu là *Contract Owner*, họ thấy "Evaluar Proveedores".

### 2.2. Trạng thái ban đầu (Initial State)
*   Khi mới vào, màn hình có thể chỉ hiển thị Header.
*   Hoặc hiển thị một thông điệp chào mừng cá nhân hóa.

### 2.3. Thông điệp chào mừng (Welcome Message)
*   Đề xuất hiển thị thông báo: **"Welcome, you have X pending evaluations to complete"** (Chào mừng, bạn có X đánh giá đang chờ hoàn thành).
*   Số lượng **X** cần được tính toán động dựa trên các đánh giá được gán cho người dùng đó (trong list `ongoingEvaluations`).

## 3. Đề xuất triển khai (Implementation Suggestions)

*   **OnStart**:
    *   Lấy email người dùng hiện tại: `User().Email`.
    *   Tra cứu vai trò của người dùng trong các bảng Master Data (Maestro de colaboradores, Flujo aprobadores...).
    *   Đếm số lượng đánh giá đang chờ (`CountRows` với `Filter` trên `ongoingEvaluations`).
*   **UI**:
    *   Sử dụng một `Label` lớn ở giữa màn hình để hiện thông điệp chào mừng.
    *   Bên dưới thông điệp có thể là một `Gallery` tóm tắt nhanh các task cần làm ngay (Pending Tasks).

## 4. Chi tiết Màn hình Đánh giá (scrEvaluateProviders)

Dựa trên 2 hình ảnh wireframe chi tiết về form đánh giá:

### 4.1. Bố cục (Layout)
*   **Header**: Giữ nguyên Header chung của App.
*   **Sidebar (Bên trái)**:
    *   **Dropdown "Select evaluation"**: Cho phép chọn đánh giá cần thực hiện.
    *   **Thông tin tóm tắt**: Hiển thị (Date) Provider name, Contract... của các đánh giá đang chờ.
*   **Main Form (Bên phải)**:
    *   Khu vực hiển thị form đánh giá chính.
    *   Chỉ hiển thị (Visible) hoặc cho phép nhập liệu (Enabled) khi đã chọn một đánh giá từ Sidebar.

### 4.2. Cấu trúc Form (Form Structure)
Form được chia thành các phần (Sections):

1.  **Header Block**:
    *   Thông tin chung về đánh giá, hợp đồng, nhà cung cấp.
2.  **General Section**:
    *   **Các chỉ số đánh giá**:
        *   `calificacion_criterio_calidad` (Number): Điểm chất lượng.
        *   `calificacion_criterio_oportunidad` (Number): Điểm kịp thời/đúng hạn.
        *   `calificacion_criterio_gestion` (Number): Điểm quản lý.
        *   `cumple_codigo_etica` (Boolean): Tuân thủ quy tắc đạo đức.
        *   `comment` (Multi-line Text): Nhận xét chung.
3.  **SST Section** (An toàn & Sức khỏe):
    *   **Các chỉ số đánh giá**:
        *   `calificacion_criterio_sst` (Number): Điểm SST.
        *   `comment` (Multi-line Text): Nhận xét về SST.
4.  **MA Section** (Medio Ambiente - Môi trường):
    *   **Các chỉ số đánh giá**:
        *   `calificacion_criterio_ma` (Number): Điểm môi trường.
        *   `comment` (Multi-line Text): Nhận xét về môi trường.
5.  **Overall Score**:
    *   Tổng điểm và các nút Submit/Reset.

### 4.3. Logic Hiển thị (Display Logic) - QUAN TRỌNG
Các quy tắc hiển thị section dựa trên vai trò người dùng (User Role) và dữ liệu:

*   **Admin User & No SST & No MA**: Hiển thị General Section.
*   **Admin User & SST**: Chỉ hiển thị **General Section** và **SST Section**
*   **SST User**: Chỉ hiển thị **SST Section**.
*   **MA User**: Chỉ hiển thị **MA Section**.
*   **Logic chung**: Các section (General, SST, MA) sẽ được **Enable/Disable** hoặc **Visible/Hidden** tùy thuộc vào Role của người dùng đang đăng nhập.

### 4.4. Các cờ logic (Flags/Inputs)
Cần có các input (Checkbox/Toggle) để đánh dấu các trường hợp đặc biệt:
1.  **Medio Ambiente Flag**: Input boolean để đánh dấu nếu cần người phụ trách Môi trường (Environment) tham gia đánh giá.
2.  **Ethical Code Flag**: Input boolean để đánh dấu nếu nhà cung cấp **vi phạm quy tắc đạo đức** (does not abide by ethical code).

### 4.5. Ghi chú UI khác
*   **Scoring Input**: Có thể dùng 3 Radio button cho mỗi dòng, hoặc cân nhắc dùng **Dropdown** đơn giản hơn cho mỗi Subcriterion.
*   **Empty State**: Nếu chưa chọn đánh giá nào, form nên ẩn đi hoặc hiện thông báo "Select an evaluation to continue".
