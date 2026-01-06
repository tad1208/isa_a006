# A006 - Robot 1: Generación y Activación (Dispatcher)

## 1. Vai trò
**Robot 1** đóng vai trò là người khởi tạo quy trình (Dispatcher). Nó chịu trách nhiệm đọc dữ liệu hợp đồng từ SAP, xác định hợp đồng nào cần đánh giá, và tạo các yêu cầu đánh giá trên SharePoint để người dùng thực hiện qua App.

## 2. Quy trình chi tiết (Theo Chile Framework)

Robot 1 được xây dựng dựa trên **State Machine** của Chile Framework. Dưới đây là chi tiết từng bước xử lý trong các State:

### 2.1. Framework Init State (Khởi tạo)
*Mục đích: Chuẩn bị môi trường làm việc.*
1.  **Đọc Config**: Robot đọc file `configuration.xlsx` để lấy các tham số:
    -   Đường dẫn file input (SAP Report).
    -   URL của SharePoint List (`a006_evaluations`).
    -   Email template (Mẫu thư mời).
    -   Các ngưỡng quy định (Ví dụ: Hợp đồng sắp hết hạn trong bao nhiêu ngày thì đánh giá).
2.  **Kill Process**: Đóng các ứng dụng Excel, Chrome đang mở để tránh xung đột.
3.  **Clean Up**: Xóa các file tạm trong thư mục `process_data` từ lần chạy trước.

### 2.2. Build Worktray State (Tạo dữ liệu xử lý)
*Mục đích: Tạo ra danh sách các việc cần làm (Worktray).*
Đây là bước quan trọng nhất của Robot 1.
1.  **Đọc Input**: Robot đọc file Excel báo cáo hợp đồng ("Informe de contratos vigentes") từ thư mục đầu vào.
2.  **Lọc Hợp đồng (Filter Logic)**: Robot duyệt qua từng dòng hợp đồng và áp dụng quy tắc để chọn ra hợp đồng cần đánh giá:
    -   *Quy tắc 1 (Hết hạn)*: Nếu `Ngày kết thúc` - `Ngày hiện tại` <= 20 ngày VÀ `Thời hạn` < 1 năm -> Chọn (Loại `EndOfContract`).
    -   *Quy tắc 2 (Định kỳ)*: Nếu `Thời hạn` > 1 năm VÀ đã đến kỳ đánh giá năm (dựa vào ngày bắt đầu) -> Chọn (Loại `Annual`).
    -   *Quy tắc 3 (Loại trừ)*: Bỏ qua các hợp đồng đã bị hủy hoặc thuộc loại không cần đánh giá (theo Config).
3.  **Kiểm tra trùng lặp (Check Existence)**:
    -   Với mỗi hợp đồng được chọn, Robot tạo một mã định danh `Evaluation ID` (Ví dụ: `EVAL-2025-CTR123`).
    -   Robot kiểm tra trên SharePoint List `a006_evaluations` xem ID này đã tồn tại chưa.
    -   Nếu chưa có -> Thêm vào danh sách cần xử lý (Worktray DT).
    -   Nếu đã có -> Bỏ qua (để tránh tạo lại phiếu đánh giá 2 lần).

### 2.3. Process State (Xử lý từng item)
*Mục đích: Thực hiện hành động cho từng hợp đồng trong Worktray.*
Robot sẽ lặp qua từng dòng trong Worktray đã tạo ở bước trên:

**Bước 1: Xác định yêu cầu (Business Rules)**
-   Dựa vào loại dịch vụ (Service Type) hoặc dữ liệu trong file Excel, Robot xác định:
    -   `RequiresSST`: Có cần bộ phận An toàn đánh giá không? (True/False).
    -   `RequiresEnv`: Có cần bộ phận Môi trường đánh giá không? (True/False).
-   Tính toán các hạn chót:
    -   `EvaluationDueDate`: Hạn cho người đánh giá (Ví dụ: Today + 5 ngày).
    -   `ApprovalDueDate`: Hạn cho sếp duyệt (Ví dụ: Today + 10 ngày).

**Bước 2: Tạo Item trên SharePoint (Create)**
-   Robot sử dụng Activity `Create List Item` để tạo dòng mới trên List `a006_evaluations`.
-   Điền đầy đủ thông tin: `ContractNumber`, `SupplierName`, `EvaluatorEmail`, `EvaluationType`, `DueDates`...
-   Đặt trạng thái ban đầu:
    -   `Status` = `New`
    -   `R1_Status` = `New`

**Bước 3: Gửi Email mời (Send Invite)**
-   Robot soạn email gửi cho `EvaluatorEmail` (Người quản lý hợp đồng).
-   Nội dung: "Kính gửi [Tên], Hợp đồng [Số] với nhà cung cấp [Tên] đã đến hạn đánh giá. Vui lòng truy cập App tại [Link] để thực hiện trước ngày [DueDate]."
-   Nếu có lỗi khi gửi mail (sai email, mạng lỗi), Robot sẽ đánh dấu item là `Failed` nhưng không dừng quy trình, tiếp tục làm item tiếp theo.

**Bước 4: Cập nhật trạng thái (Update)**
-   Sau khi gửi mail thành công, Robot update lại item trên SharePoint:
    -   `Status` = `Pending` (Chờ người dùng làm).
    -   `R1_Status` = `Success`.
    -   `EmailSent_Invite` = `True`.
    -   `InviteSentDate` = `Now`.

### 2.4. End State (Kết thúc)
*Mục đích: Dọn dẹp và báo cáo.*
1.  **Gửi báo cáo chạy (Execution Report)**: Robot gửi email cho Admin hệ thống, báo cáo:
    -   Tổng số hợp đồng quét được: 100.
    -   Số hợp đồng cần đánh giá: 5.
    -   Số email đã gửi thành công: 5.
    -   Danh sách lỗi (nếu có).
2.  **Đóng ứng dụng**: Đóng Outlook, Excel.

## 3. Tóm tắt luồng dữ liệu (Data Flow)
1.  **Excel (SAP)** -> **Robot 1 (Memory/Logic)** -> **SharePoint List (New Item)** -> **Email (User)**.

## 4. Điểm cần lưu ý khi Dev
-   **Worktray Template**: Cần định nghĩa rõ các cột trong `Build Worktray` khớp với SharePoint List.
-   **Retry Logic**: Nếu mất mạng khi đang tạo SharePoint Item, cần có cơ chế Retry (thử lại 3 lần).
-   **Batch Processing**: Nếu số lượng quá lớn (>1000), có thể cân nhắc xử lý theo lô (Batch) để tránh Timeout, nhưng với quy trình này (khoảng 50 item/lần) thì xử lý tuần tự (Sequential) là ổn.
