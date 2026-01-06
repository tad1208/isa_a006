# A006 - Robot 2: Evaluación y Cierre (Performer)

## 1. Vai trò
**Robot 2** đóng vai trò là người xử lý và tổng hợp (Performer). Robot này hoạt động như một "cỗ máy kiểm tra", định kỳ quét qua danh sách các đánh giá đang chờ, xác định xem cái nào đã xong để chốt sổ, cái nào chưa xong thì nhắc nhở.

## 2. Quy trình chi tiết (Theo Chile Framework)

Robot 2 cũng sử dụng **State Machine** của Chile Framework, nhưng logic tập trung vào việc xử lý từng dòng dữ liệu trên SharePoint.

### 2.1. Framework Init State (Khởi tạo)
*Mục đích: Chuẩn bị.*
1.  **Đọc Config**: Lấy URL SharePoint, Email Template (Reminder), đường dẫn lưu file CSV output.
2.  **Kill Process**: Đóng Excel, Chrome.
3.  **Clean Up**: Dọn dẹp folder `process_data` và `out`.

### 2.2. Build Worktray State (Lấy dữ liệu cần xử lý)
*Mục đích: Lấy danh sách các đánh giá đang "treo" để kiểm tra.*
1.  **Đọc SharePoint List**: Robot dùng Activity `Get List Items` để lấy dữ liệu từ `a006_evaluations`.
2.  **Lọc dữ liệu (Filter Query)**: Chỉ lấy những dòng có trạng thái chưa hoàn thành:
    -   `Status` = 'Pending' HOẶC `Status` = 'Processing'.
    -   (Có thể lọc thêm: `IsLocked` = False để tránh đụng độ nếu chạy nhiều bot).
3.  **Kết quả**: Tạo ra một `DataTable` chứa danh sách các đánh giá cần kiểm tra (Worktray).

### 2.3. Process State (Xử lý từng đánh giá)
*Mục đích: Kiểm tra logic cho từng item và quyết định hành động (Chốt hay Nhắc).*

**Bước 1: Kiểm tra điều kiện hoàn thành (Validation)**
Robot kiểm tra xem User đã điền đủ điểm chưa:
-   `HasTech`: Trường `Tech_Score` có dữ liệu không?
-   `HasSST`: Nếu `RequiresSST`=True thì `SST_Score` có dữ liệu không?
-   `HasEnv`: Nếu `RequiresEnv`=True thì `Env_Score` có dữ liệu không?
-   `IsCompliant`: `Ethics_Compliant` có phải là True không?

**Bước 2: Phân nhánh xử lý (Decision)**

*   **Trường hợp A: Đã đánh giá xong nhưng chưa duyệt (Ready for Approval)**
    -   Điều kiện: `HasTech` + `HasSST` + `HasEnv` = True, nhưng `Approval_Status` = 'Pending' (hoặc Empty).
    -   Hành động:
        1.  **Gửi Email cho Approver**: Gửi mail cho sếp (Jefatura) báo "Vui lòng phê duyệt đánh giá [ID]".
        2.  **Cập nhật SharePoint**:
            -   `EmailSent_Approver` = True.
            -   `Status` = `Waiting for Approval`.
            -   `Observation` = "Sent to approver".

*   **Trường hợp B: Đã được phê duyệt (Approved & Ready to Close)**
    -   Điều kiện: `Approval_Status` = 'Approved'.
    -   Hành động:
        1.  **Lock Item**: Update `IsLocked` = True.
        2.  **Tính toán**: Tính điểm tổng kết.
        3.  **Tạo file Output**: Xuất CSV.
        4.  **Upload/Gửi kết quả**: Gửi cho Mua sắm.
        5.  **Cập nhật SharePoint**: `Status` = `Completed`, `R2_Status` = `Success`.

*   **Trường hợp C: Bị từ chối (Rejected)**
    -   Điều kiện: `Approval_Status` = 'Rejected'.
    -   Hành động:
        1.  **Gửi Email báo lại cho Evaluator**: "Đánh giá bị từ chối, vui lòng làm lại".
        2.  **Reset trạng thái**: `Status` = `Pending`, `Approval_Status` = `Pending`.

*   **Trường hợp D: Chưa xong & Đã quá hạn (Overdue)**
    -   (Giữ nguyên logic cũ: Gửi Reminder).

*   **Trường hợp E: Chưa xong (Waiting)**
    -   (Giữ nguyên logic cũ).

### 2.4. End State (Kết thúc)
*Mục đích: Báo cáo kết quả phiên làm việc.*
1.  **Tổng hợp báo cáo**:
    -   Số lượng đã hoàn thành (Completed): 2.
    -   Số lượng đã nhắc nhở (Reminded): 3.
    -   Số lượng đang chờ (Waiting): 10.
2.  **Gửi Email Report**: Gửi cho Admin/Manager để nắm tình hình tiến độ toàn bộ dự án.

## 3. Tóm tắt luồng dữ liệu (Data Flow)
1.  **SharePoint List (Pending)** -> **Robot 2 (Check Logic)** -> **CSV/ServiceNow (Final)** HOẶC **Email (Reminder)**.

## 4. Điểm cần lưu ý khi Dev
-   **Công thức tính điểm**: Cần đưa công thức tính toán vào file Config hoặc một Workflow riêng để dễ sửa đổi sau này (tránh hard-code).
-   **Mapping CSV**: Cần chú ý mapping chính xác tên cột của CSV theo yêu cầu của ServiceNow (thường rất khắt khe về format ngày tháng, số thập phân).
-   **Concurrency**: Robot 2 có thể chạy nhiều lần trong ngày (ví dụ 1 tiếng/lần) để cập nhật dữ liệu liên tục.
