# Hướng Dẫn Deploy & Share App cho Client (Testing)

Tài liệu này hướng dẫn cách chia sẻ App cho khách hàng (Business Users) tham gia Test (UAT) mà không ảnh hưởng đến quá trình Development đang diễn ra.

## 1. Chiến lược: Dùng bản Copy (Recommended)

Để tránh việc khách hàng thấy lỗi khi bạn đang sửa code dở dang, hãy tạo một bản sao riêng cho họ.

1.  Mở Power App (bản Dev chính) -> **File** -> **Save As**.
2.  Đặt tên mới: `A006_ProviderEvaluation_UAT` (hoặc `_Test`).
3.  **Lưu ý**: Bản này vẫn dùng chung dữ liệu với bản chính trừ khi bạn đổi SharePoint List khác.

## 2. Quy trình "Publish" (Xuất bản)

Người dùng chỉ thấy các thay đổi khi bạn **Publish**.

1.  Mở App (bản UAT) trong Studio.
2.  Nhấn icon **Publish** (hình cái hộp có mũi tên lên / hoặc menu File -> Publish).
3.  Bấm **Publish this version**.

> **Mẹo**: Nếu khách hàng báo lỗi, bạn sửa xong phải nhớ Publish lại thì họ mới thấy bản sửa lỗi.

### Cách kiểm tra bản nào đang Live (Đã Publish thành công)
Để chắc chắn bản mới nhất đã đến tay người dùng:
1. Vào `make.powerapps.com` -> **Apps**.
2. Chọn dấu `...` cạnh App -> **Details**.
3. Chọn tab **Versions**.
4. Kiểm tra dòng có trạng thái **Live**. Nếu thời gian trùng khớp với lúc bạn vừa Publish thì đã thành công.

## 3. Quy trình "Share" (Chia sẻ App)

1.  Vào trang danh sách Apps (make.powerapps.com).
2.  Chọn dấu `...` bên cạnh App UAT -> **Share**.
3.  **Share cho từng người**: Nhập email khách hàng.
4.  **Share cho cả công ty**: Nhập `Everyone` (hoặc `All Staff`, `All Users` tùy domain).
    *   *Chọn group "Everyone..." hiện ra.*
5.  **Quyền hạn**:
    *   **Co-owner**: Bỏ chọn (trừ khi muốn họ sửa code).
    *   **User**: Mặc định (chỉ được dùng App).
6.  Bấm **Share**.

## 4. Cấp quyền Dữ liệu (SharePoint Permissions) - QUAN TRỌNG

User có App nhưng không vào được SharePoint thì App sẽ trắng trơn hoặc báo lỗi.

1.  Truy cập SharePoint Site dứa `ongoingEvaluations`.
2.  Vào **Settings** (bánh răng) -> **Site Permissions**.
3.  **Advanced permissions settings** (nếu cần).
4.  Thêm User (hoặc group `Everyone`) vào nhóm:
    *   **Site Members/Contributers**: Nếu họ cần **Ghi/Sửa/Duyệt** đánh giá.
    *   **Site Visitors/Read**: Nếu họ chỉ **Xem** báo cáo.
5.  **Kiểm tra**: Gửi link SharePoint List cho họ vào thử trước. Nếu họ thấy dữ liệu là OK.

---

## Tóm tắt Workflow

1.  **Dev** trên bản chính (`A006_Dev`).
2.  Khi hoàn thành 1 tính năng lớn -> **Save As** sang bản `A006_UAT` (lần đầu) hoặc Import Solution sang môi trường Test.
3.  **Publish** bản UAT.
4.  **Share** App + **Share** SharePoint Permission.
5.  Gửi link bản UAT cho khách hàng.
