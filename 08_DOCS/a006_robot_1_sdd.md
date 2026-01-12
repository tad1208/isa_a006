# A006 - Robot 1: Dispatcher & Monitor (Solution Design Document)

Tài liệu thiết kế chi tiết cho **Robot 1**, được tổng hợp từ Source Code thực tế (`07_CODES/a006.1`) và tài liệu kiến trúc ban đầu.

## 1. Tổng quan (Overview)

**Robot 1** hoạt động như một Dispatcher thông minh với 2 nhiệm vụ chính:
1.  **Generation**: Quét dữ liệu nguồn (DB/Excel), phát hiện các hợp đồng cần đánh giá và tạo Ticket trên SharePoint.
2.  **Monitoring**: Theo dõi các Ticket đang mở, gửi email nhắc nhở (Reminder) nếu quá hạn.

## 2. Kiến trúc (Architecture)

Robot sử dụng **Chile Framework** (Standalone Template) với cấu trúc tuần tự (Sequential Workflows).

### Cấu trúc thư mục code (`src/wfs/`)
*   `1_build_worktray`: Xây dựng danh sách các item cần xử lý.
*   `2_upload_new_evaluations`: Tạo ticket mới trên SharePoint.
*   `3_send_reminder`: Gửi nhắc nhở cho ticket chưa hoàn thành.
*   `4_send_execution_report`: Gửi báo cáo kết quả chạy Robot.
*   `_utils_process`: Các component tái sử dụng (SharePoint API, SQL Helper...).

---

## 3. Chi tiết quy trình (Process workflows)

### 3.1. Build Worktray (`1_build_worktray`)
*   **Mục đích**: Chuẩn bị dữ liệu đầu vào.
*   **Input**:
    *   Dữ liệu hợp đồng (Từ SQL Database hoặc Excel Report).
    *   Dữ liệu lịch sử từ SharePoint (để kiểm tra trùng lặp).
*   **Logic**:
    1.  Lấy danh sách hợp đồng đang hiệu lực (`utils_get_sql_data.xaml` hoặc đọc Excel).
    2.  Lấy danh sách các đánh giá đang chạy (`utils_get_sharepoint_list_data.xaml`).
    3.  **Filter Logic**:
        *   Lọc hợp đồng sắp hết hạn (End of Contract rule).
        *   Lọc hợp đồng đến hạn đánh giá định kỳ (Annual rule).
        *   Loại trừ các hợp đồng đã có Ticket "Pending" trên SharePoint (Tránh tạo trùng).
*   **Output**: `dt_Worktray` (DataTable chứa các hợp đồng cần tạo mới).

### 3.2. Upload New Evaluations (`2_upload_new_evaluations`)
*   **Mục đích**: Tạo Ticket đánh giá trên hệ thống.
*   **Logic**:
    *   Duyệt từng dòng trong `dt_Worktray`.
    *   Map dữ liệu từ Hợp đồng sang cấu trúc SharePoint (Tên NCC, Mã HĐ, Email Evaluator...).
    *   Gọi `utils_add_item_to_sharepoint_list.xaml` để tạo item mới với trạng thái:
        *   `Status`: **New**
        *   `Tecnico_Submitted`: **False**
    *   Gửi email mời đánh giá (Invitation Email) cho người phụ trách (Técnico).

### 3.3. Send Reminder (`3_send_reminder`)
*   **Mục đích**: Nhắc nhở các đánh giá bị trễ hạn.
*   **Logic**:
    1.  Query SharePoint List để lấy các item có trạng thái **Pending** (Chưa Submit).
    2.  Kiểm tra `Due Date` so với `Current Date`.
    3.  Nếu (Quá hạn) OR (Sắp đến hạn - Configurable threshold):
        *   Gửi email nhắc nhở (Reminder Email).
        *   Cập nhật Log hoặc đếm số lần nhắc nhở trên SharePoint (Optional).

## 4. Các thành phần dùng chung (`_utils_process`)

Robot 1 sử dụng các Utility riêng biệt để tương tác hệ thống:

| Component File | Chức năng | Ghi chú |
| :--- | :--- | :--- |
| `utils_get_sql_data.xaml` | Kết nối & Query Database | Dùng để lấy master data hợp đồng. |
| `utils_get_sharepoint_list_data.xaml` | Lấy dữ liệu từ SP List | Dùng CAML Query hoặc Filter OData. |
| `utils_add_item_to_sharepoint_list.xaml` | Tạo mới Item | Input là Dictionary {Column: Value}. |
| `utils_fill_word_template.xaml` | Điền Word | (Có thể dùng cho báo cáo hoặc thư mời dạng đính kèm). |

## 5. Configuration (`framework/configuration.xlsx`)

Các tham số cần cấu hình để Robot vận hành:

*   **SharePointURL**: Link tới site chứa List.
*   **ListName_Ongoing**: Tên List đánh giá (`ongoingEvaluations`).
*   **Threshold_DaysBefore**: Số ngày trước khi hết hạn HĐ để kích hoạt đánh giá (Ví dụ: 30 ngày).
*   **AdminEmail**: Email nhận báo cáo lỗi/kết quả.
*   **MailTemplate_Invite**: Đường dẫn mẫu email mời.
*   **MailTemplate_Reminder**: Đường dẫn mẫu email nhắc.
