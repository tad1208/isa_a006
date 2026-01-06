# Hướng dẫn tạo Flow "CreatPdf": Ký tên -> Tạo PDF -> Gửi Email

Tài liệu này hướng dẫn chi tiết cách tạo một quy trình tự động (Flow) tên là **CreatPdf**.
Quy trình này sẽ nhận chữ ký và thông tin từ Power Apps, điền vào mẫu Word, chuyển sang PDF và gửi email đính kèm file PDF đó.

## Phần 1: Chuẩn bị mẫu Word (Word Template)

Trước khi tạo Flow, bạn cần có một file Word mẫu.

1.  Mở Microsoft Word, tạo một văn bản mới (ví dụ: `BienBanNghiemThu.docx`).
2.  Soạn thảo nội dung văn bản.
3.  **Chèn các vị trí để điền chữ (Text):**
    -   Bật tab **Developer** (nếu chưa có: File -> Options -> Customize Ribbon -> Tích vào Developer).
    -   Đặt con trỏ vào chỗ cần điền (ví dụ: "Tên khách hàng").
    -   Bấm vào biểu tượng **Plain Text Content Control** (hình `Aa`).
    -   Bấm **Properties**, đặt **Title** và **Tag** giống nhau (ví dụ: `TenKhachHang`).
4.  **Chèn vị trí để ký (Image):**
    -   Đặt con trỏ vào chỗ cần ký.
    -   Bấm vào biểu tượng **Picture Content Control** (hình bức tranh).
    -   Bấm **Properties**, đặt **Title** và **Tag** là `ChuKy`.
5.  Lưu file `BienBanNghiemThu.docx` lên **OneDrive** (ví dụ: trong thư mục `Templates`).

---

## Phần 2: Tạo Flow "CreatPdf" trên Power Automate

1.  Vào [make.powerautomate.com](https://make.powerautomate.com/), chọn **+ New flow** -> **Instant cloud flow**.
2.  Đặt tên: `CreatPdf`.
3.  Chọn Trigger: **PowerApps (V2)** -> Nhấn **Create**.

### Bước 1: Cấu hình Input (Đầu vào)
Tại bước **PowerApps (V2)**, nhấn **+ Add an input** để tạo các biến sẽ nhận từ App:
1.  Chọn **Text** -> Đặt tên: `TenKhachHang`.
2.  Chọn **Text** -> Đặt tên: `NoiDung`.
3.  Chọn **Text** -> Đặt tên: `EmailNguoiNhan`.
4.  Chọn **Text** -> Đặt tên: `ChuKyBase64` (Đây là chuỗi mã hóa của ảnh chữ ký).

### Bước 2: Xử lý ảnh chữ ký
Thêm action **Compose** (Soạn thảo):
-   **Inputs**:
    ```json
    {
      "$content-type": "image/png",
      "$content": @{triggerBody()['text_3']}
    }
    ```
    *(Lưu ý: `@triggerBody()['text_3']` là chọn biến `ChuKyBase64` từ Dynamic Content. Nếu bạn chọn đúng biến, nó sẽ hiện ra màu tím).*

### Bước 3: Điền dữ liệu vào Word
Thêm action **Populate a Microsoft Word template** (Word Online (Business)):
-   **Location**: OneDrive for Business.
-   **Document Library**: OneDrive.
-   **File**: Chọn file `BienBanNghiemThu.docx` bạn đã lưu ở Phần 1.
-   Sau khi chọn file, các trường (Tag) bạn tạo trong Word sẽ hiện ra:
    -   `TenKhachHang`: Chọn biến `TenKhachHang` từ PowerApps.
    -   `ChuKy`: Chọn **Outputs** của bước **Compose** ở trên.

### Bước 4: Tạo file Word tạm
Thêm action **Create file** (OneDrive for Business):
-   **Folder Path**: `/Templates/Temp` (hoặc thư mục tạm nào đó).
-   **File Name**: `temp.docx`.
-   **File Content**: Chọn **Body** của bước *Populate a Microsoft Word template*.

### Bước 5: Chuyển đổi sang PDF
Thêm action **Convert file (using path)** (OneDrive for Business):
-   **File Path**: `/Templates/Temp/temp.docx` (đường dẫn file vừa tạo).
-   **Target format**: `pdf`.

### Bước 6: Lấy nội dung file PDF
Thêm action **Get file content** (OneDrive for Business) *[Quan trọng: Bước Convert chỉ tạo file, chưa lấy nội dung để gửi mail]*:
-   **File**: Chọn đường dẫn file PDF (thường là output của bước Convert, nhưng an toàn nhất là dùng Path cố định hoặc ID). Tuy nhiên, cách đơn giản nhất là dùng **File content** từ bước Convert nếu có, hoặc dùng action **Create file** để lưu PDF rồi mới lấy content.
    *Cách tối ưu hơn cho bước này:*
    -   Dùng action **Create file** (lần 2) để lưu file PDF:
        -   Path: `/Templates/Temp/final.pdf`
        -   Content: **File content** của bước *Convert file*.
    -   Dùng action **Get file content** trỏ vào `/Templates/Temp/final.pdf`.

### Bước 7: Gửi Email
Thêm action **Send an email (V2)** (Office 365 Outlook):
-   **To**: Chọn biến `EmailNguoiNhan`.
-   **Subject**: `Gửi biên bản nghiệm thu - [TenKhachHang]`.
-   **Body**: `Kính gửi anh/chị, đính kèm là file PDF đã ký.`.
-   **Advanced options** (Tùy chọn nâng cao) -> **Attachments**:
    -   **Name**: `BienBan.pdf`.
    -   **Content**: Chọn **File content** của bước *Get file content* (hoặc *Convert file*).

---

## Phần 3: Kết nối với Power Apps

1.  Mở App, chọn nút **Gửi** (Button).
2.  Kết nối Flow `CreatPdf`.
3.  Viết code `OnSelect` (Giả sử bạn có PenInput1, TextInputTen, TextInputEmail):

```powerapps
// 1. Xử lý ảnh chữ ký sang Base64 sạch
Set(varImageJSON, JSON(PenInput1.Image, JSONFormat.IncludeBinaryData));
Set(varSignatureBase64, 
    Mid(varImageJSON, Find(",", varImageJSON) + 1, Len(varImageJSON) - Find(",", varImageJSON) - 1)
);

// 2. Gọi Flow
CreatPdf.Run(
    TextInputTen.Text,          // TenKhachHang
    "Nội dung demo",            // NoiDung
    TextInputEmail.Text,        // EmailNguoiNhan
    varSignatureBase64          // ChuKyBase64
);

Notify("Đã gửi mail thành công!", NotificationType.Success);
```
