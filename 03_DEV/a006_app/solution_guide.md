# Hướng Dẫn Phát Triển App Với Solution & Environment Variables

Tài liệu này hướng dẫn cách xây dựng Power Apps/Power Automate trong **Solution** để dễ dàng export/import giữa các môi trường (Dev -> Test -> Prod) mà không cần sửa code thủ công.

## 1. Tạo Solution (Giải pháp)

Thay vì tạo App trực tiếp từ trang chủ, bạn cần tạo Solution trước.

1.  Truy cập [make.powerapps.com](https://make.powerapps.com).
2.  Chọn môi trường **Dev** (ví dụ: `duc.trananh`).
3.  Ở menu trái, chọn **Solutions** -> **New solution**.
4.  Điền thông tin:
    *   **Display name**: Ví dụ `A006_App_Solution`.
    *   **Publisher**: Nên tạo một Publisher mới (ví dụ: `SisuDev`) để có prefix đẹp (ví dụ: `sisu_`) thay vì `cr564_`.
    *   **Version**: `1.0.0.0`.
5.  Bấm **Create**.

## 2. Tạo Environment Variables (Quan trọng)

Đây là bước then chốt để App tự động nhận diện SharePoint Site/List mới khi sang môi trường khác.

### 2.1. Biến cho SharePoint Site
1.  Mở Solution vừa tạo.
2.  Chọn **New** -> **More** -> **Environment variable**.
3.  Thiết lập:
    *   **Display name**: `SharePoint Site URL`.
    *   **Name**: `sisu_SharePointSiteURL` (tự động sinh).
    *   **Data Type**: **Data Source**.
    *   **Connector**: chọn **SharePoint**.
    *   **Parameter type**: **Site**.
4.  Ở phần **Current Value** (hoặc Default Value): Chọn Site SharePoint Development của bạn.
5.  Bấm **Save**.

### 2.2. Biến cho SharePoint List
**Lưu ý quan trọng**: Bạn phải tạo 2 list `ongoingEvaluations` và `completedEvaluations` trên SharePoint **trước** khi thực hiện bước này.

1.  Trong Solution, chọn **New** -> **More** -> **Environment variable**.
2.  Thiết lập cho list **ongoingEvaluations**:
    *   **Display name**: `Ongoing Evaluations List`.
    *   **Name**: `sisu_OngoingEvaluationsList`.
    *   **Data Type**: **Data Source**.
    *   **Connector**: chọn **SharePoint**.
    *   **Parameter type**: **List**.
    *   **Parent Data Source**: Chọn biến `SharePoint Site URL` vừa tạo ở bước trên.
3.  Ở phần **Current Value**: Chọn List `ongoingEvaluations` đã tạo.
4.  Bấm **Save**.

*Làm tương tự cho list `completedEvaluations`.*

## 3. Tạo App trong Solution

1.  Trong Solution, chọn **New** -> **App** -> **Canvas app**.
2.  Đặt tên App và tạo.
3.  **Kết nối dữ liệu**:
    *   Vào tab **Data** -> **Add data**.
    *   Tìm **SharePoint**.
    *   **QUAN TRỌNG**: Khi chọn Site/List, hãy chọn tab **Advanced** (hoặc tìm trong danh sách) để chọn **Environment Variables** bạn vừa tạo, KHÔNG chọn trực tiếp tên Site cứng.
    *   *Lưu ý*: Giao diện mới của Power Apps đôi khi tự động nhận diện. Nếu không thấy biến, hãy đảm bảo bạn đã add biến vào Solution trước.
    *   Cách kiểm tra: Trong code `App.OnStart` hoặc property của datasource, nó sẽ tham chiếu đến tên biến môi trường.

## 4. Export & Import (Deploy)

### 4.1. Export tại môi trường Dev
1.  Vào mục **Solutions**, chọn Solution của bạn.
2.  Bấm **Export solution**.
3.  **Publish** changes nếu cần.
4.  Chọn **Managed** (nếu gửi cho khách hàng/Prod - không cho sửa) hoặc **Unmanaged** (nếu muốn move sang môi trường khác để dev tiếp).
5.  Tải file `.zip` về.

### 4.2. Import tại môi trường Đích (rpa@isa)
1.  Đăng nhập `rpa@isa.com` tại [make.powerapps.com](https://make.powerapps.com).
2.  Vào **Solutions** -> **Import solution**.
3.  Chọn file `.zip`.
4.  Hệ thống sẽ hiện bảng **Connection References**:
    *   Nó sẽ hỏi kết nối SharePoint. Bạn bấm tạo mới/chọn kết nối của `rpa@isa`.
5.  Hệ thống sẽ hiện bảng **Environment Variables**:
    *   Nó sẽ hỏi giá trị cho `SharePoint Site URL` và `Requests List`.
    *   Bạn chọn Site và List tương ứng ở môi trường này.
6.  Bấm **Import**.

Xong! App sẽ chạy với dữ liệu của môi trường mới mà không cần sửa một dòng code nào.
