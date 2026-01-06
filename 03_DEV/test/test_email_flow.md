# Hướng dẫn Test: Tạo nút gửi Email trong Power Apps (Dành cho người mới bắt đầu)

Tài liệu này hướng dẫn từng bước chi tiết để bạn tạo một nút bấm trong Power Apps, nút này sẽ kích hoạt một quy trình (Flow) gửi email thử nghiệm.

## Mục tiêu
Kiểm tra xem hệ thống có cho phép gửi email tự động hay không và xác định các lỗi tiềm ẩn (nếu có).

---

## Phần 1: Tạo Power Automate Flow (Quy trình gửi mail)

1.  Truy cập trang [make.powerautomate.com](https://make.powerautomate.com/).
2.  Ở menu bên trái, nhấn vào **My flows** (Dòng của tôi).
3.  Nhấn nút **+ New flow** (Dòng mới) ở góc trên bên trái -> Chọn **Instant cloud flow** (Dòng đám mây tức thì).
4.  Một cửa sổ hiện ra:
    -   **Flow name**: Đặt tên là `TestEmailFlow`.
    -   **Choose how to trigger this flow**: Chọn **PowerApps (V2)** (biểu tượng hình thoi màu tím).
    -   Nhấn **Create** (Tạo).
5.  Bạn sẽ vào giao diện thiết kế Flow. Nhấn nút **+ New step** (Bước mới).
6.  Trong ô tìm kiếm, gõ `Send an email (V2)` và chọn hành động **Send an email (V2)** (của Office 365 Outlook).
7.  Điền thông tin vào các ô:
    -   **To**: Nhập địa chỉ email của bạn (để test).
    -   **Subject**: Nhập `Test Flow Trigger from Power Apps`.
    -   **Body**: Nhập `Đây là email test từ Power Apps.`.
8.  Nhấn nút **Save** (Lưu) ở góc trên bên phải.
    -   *Lưu ý: Đợi đến khi có thông báo "Your flow is ready to go" màu xanh lá cây.*

---

## Phần 2: Tạo Nút bấm trong Power Apps

1.  Truy cập trang [make.powerapps.com](https://make.powerapps.com/).
2.  Mở App của bạn (hoặc tạo một App mới để test: **+ Create** -> **Blank app** -> **Blank canvas app** -> **Create**).
3.  Trong giao diện chỉnh sửa App:
    -   Nhìn menu bên trái, chọn biểu tượng **Power Automate** (hình luồng gió).
    -   Nhấn **Add flow** (Thêm dòng).
    -   Tìm và chọn flow `TestEmailFlow` bạn vừa tạo ở Phần 1.
4.  Quay lại giao diện thiết kế (biểu tượng Tree view - hình 3 lớp giấy):
    -   Nhấn vào menu **Insert** (Chèn) ở trên cùng -> Chọn **Button** (Nút).
    -   Một nút bấm sẽ xuất hiện trên màn hình.
5.  Khi nút đang được chọn, nhìn lên thanh công thức (Formula bar) ở trên cùng (cạnh chữ `OnSelect`):
    -   Gõ công thức sau:
        ```powerapps
        TestEmailFlow.Run()
        ```
    -   *Giải thích: Lệnh này gọi flow tên là `TestEmailFlow` chạy ngay lập tức.*

---

## Phần 3: Chạy thử (Test)

1.  Nhấn nút **Play** (hình tam giác) ở góc trên bên phải màn hình (hoặc nhấn F5).
2.  Nhấn vào cái nút bạn vừa tạo.
3.  Kiểm tra hộp thư email của bạn.
    -   **Thành công**: Nếu bạn nhận được email.
    -   **Thất bại**: Nếu không nhận được email sau 1-2 phút.

## Phần 4: Kiểm tra lỗi (Nếu không nhận được mail)

### 1. Kiểm tra Lịch sử chạy (Run History) - Quan trọng nhất
Để biết Flow có thực sự chạy hay không, bạn làm như sau:
1.  Vào trang [make.powerautomate.com](https://make.powerautomate.com/).
2.  Vào **My flows** -> Nhấn vào tên flow `TestEmailFlow` (đừng nhấn nút Edit).
3.  Kéo xuống dưới cùng, bạn sẽ thấy phần **28-day run history** (Lịch sử chạy 28 ngày).
4.  Quan sát danh sách:
    -   **Không có dòng nào**: Nghĩa là nút bấm bên App chưa kích hoạt được Flow. -> *Kiểm tra lại công thức OnSelect hoặc kết nối mạng.*
    -   **Status = Failed (Đỏ)**: Flow đã chạy nhưng bị lỗi. -> *Nhấn vào dòng đó để xem lỗi ở bước nào (thường là do IT chặn gửi mail hoặc sai email).*
    -   **Status = Succeeded (Xanh)**: Flow chạy thành công. -> *Nếu vẫn không thấy mail, hãy kiểm tra mục Spam/Junk hoặc quy tắc chặn mail của công ty.*

### 2. Các lỗi thường gặp khác (Stoppers)
-   [ ] **License**: Tài khoản của bạn có bản quyền Power Automate/Office 365 không?
-   [ ] **DLP Policy**: Công ty có chặn connector "Office 365 Outlook" không? (Thường báo lỗi đỏ ngay lúc tạo Flow).
-   [ ] **Spam/Junk**: Kiểm tra mục Spam trong email.

---

## Phần Nâng cao: Gửi kèm thông tin từ App vào Email

Giả sử bạn muốn truyền 2 thông tin từ App vào Flow:
1.  **Email người nhận** (để gửi cho người khác nhau).
2.  **Nội dung tin nhắn**.

### 1. Cập nhật Flow (Power Automate)
1.  Vào lại Flow `TestEmailFlow`, nhấn **Edit**.
2.  Nhấn vào bước **PowerApps (V2)**.
    -   Nhấn **+ Add an input** -> Chọn **Email** (thay vì Text) -> Đặt tên là `EmailNguoiNhan`.
    -   Nhấn **+ Add an input** -> Chọn **Text** -> Đặt tên là `NoiDungTinNhan`.
3.  Nhấn vào bước **Send an email (V2)**.
    -   Trong ô **To**: Xóa email cũ đi. Bấm vào ô đó, bảng Dynamic content hiện ra, chọn `EmailNguoiNhan`.
    -   Trong ô **Body**: Chọn `NoiDungTinNhan`.
4.  Nhấn **Save**.

### 2. Cập nhật App (Power Apps)
1.  Quay lại Power Apps.
2.  Vào tab **Power Automate**, nhấn `...` ở flow `TestEmailFlow` -> **Refresh**.
3.  Sửa công thức `OnSelect` của nút bấm. Bây giờ hàm `Run` sẽ đòi hỏi 2 tham số theo thứ tự bạn đã tạo:
    ```powerapps
    TestEmailFlow.Run(
        "nguoi.nhan@example.com",   // Tham số 1: Email
        "Xin chào, đây là test!"    // Tham số 2: Nội dung
    )
    ```

    **Ví dụ thực tế**: Lấy từ các ô nhập liệu trên màn hình (giả sử bạn có `TextInputEmail` và `TextInputBody`):
    ```powerapps
    TestEmailFlow.Run(
        TextInputEmail.Text,
        TextInputBody.Text
    )
    ```

4.  Chạy thử và kiểm tra kết quả.
