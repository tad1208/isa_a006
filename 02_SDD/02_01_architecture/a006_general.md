# A006 - Tổng quan quy trình Đánh giá Nhà cung cấp (Evaluación de Proveedores)

## 1. Giới thiệu chung
Quy trình **A006 - Đánh giá Nhà cung cấp** là một giải pháp tự động hóa nhằm mục đích đánh giá định kỳ hiệu quả làm việc của các nhà cung cấp (về chất lượng, tiến độ, an toàn, môi trường...).
Mục tiêu là đảm bảo các nhà cung cấp tuân thủ hợp đồng và các quy định của công ty, đồng thời lưu trữ kết quả đánh giá một cách minh bạch và có hệ thống.

## 2. Tại sao cần tự động hóa?
Trước đây (Quy trình As-Is), việc này làm thủ công:
-   Nhân viên phải tự lọc danh sách hợp đồng từ Excel.
-   Gửi email thủ công đính kèm file PDF cho từng người đánh giá.
-   Người đánh giá điền PDF, ký tên và gửi lại.
-   Nhân viên phải gom file, nhập điểm vào Excel để tính toán.
-   Cuối cùng nhập tay kết quả vào ServiceNow/SAP.

**Vấn đề**: Tốn thời gian, dễ sai sót, khó theo dõi ai chưa làm, dữ liệu rời rạc.

**Giải pháp To-Be**: Sử dụng **2 Robot UiPath** kết hợp với **Power Apps**.
-   **Robot**: Chạy ngầm để xử lý dữ liệu, gửi email nhắc nhở, và tổng hợp báo cáo.
-   **Power Apps**: Là giao diện web/mobile thân thiện để người dùng vào chấm điểm (thay vì điền PDF).

## 3. Luồng quy trình mới (To-Be)
Quy trình được chia thành 3 thành phần chính phối hợp với nhau:

1.  **Robot 1 (Dispatcher - Người giao việc)**:
    -   Đọc danh sách hợp đồng từ SAP/Excel.
    -   Tìm ra hợp đồng nào đến hạn đánh giá.
    -   Tạo "phiếu đánh giá" trên hệ thống (SharePoint List).
    -   Gửi email mời người quản lý hợp đồng vào App để chấm điểm.

2.  **Power App (Giao diện người dùng)**:
    -   Người quản lý hợp đồng (Administrador) đăng nhập vào App.
    -   Thấy danh sách các hợp đồng cần đánh giá.
    -   Chấm điểm các tiêu chí (Chất lượng, Tiến độ...).
    -   Nếu hợp đồng liên quan đến An toàn (SST) hoặc Môi trường, App sẽ hiện thêm phần đánh giá cho các bộ phận chuyên trách đó.

3.  **Robot 2 (Performer - Người tổng hợp)**:
    -   Định kỳ kiểm tra trên App xem ai đã chấm điểm xong.
    -   Kiểm tra xem đã đủ các bước chưa (Ví dụ: Quản lý chấm xong nhưng bên An toàn chưa chấm -> Chưa xong).
    -   Nếu đã xong hết: Tính tổng điểm, kiểm tra quy tắc Đạo đức (Ethics).
    -   Xuất kết quả ra file CSV/Excel và đẩy lên hệ thống ServiceNow/SAP.

## 4. Các hệ thống liên quan
-   **SAP**: Nơi chứa dữ liệu gốc về hợp đồng và nơi nhận kết quả cuối cùng.
-   **SharePoint**: Nơi lưu trữ dữ liệu trung gian (Database cho App và Robot).
-   **Power Apps**: Giao diện để con người tương tác.
-   **ServiceNow**: Hệ thống quản lý dịch vụ, nhận kết quả từ Robot để chuyển vào SAP.
-   **Outlook (Email)**: Kênh giao tiếp, thông báo.

## 5. Lợi ích
-   **Tiết kiệm thời gian**: Không cần gửi mail hay nhập liệu thủ công.
-   **Chính xác**: Robot tính điểm và kiểm tra quy tắc logic tuyệt đối chính xác.
-   **Minh bạch**: Mọi thao tác, chấm điểm đều được lưu lại trên hệ thống (Traceability).
