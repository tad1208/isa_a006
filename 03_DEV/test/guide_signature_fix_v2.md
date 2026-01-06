
## BƯỚC 1: Xử lý dữ liệu trong Power Apps

Chúng ta phải đảm bảo chuỗi gửi đi là **Base64 sạch** (bắt đầu bằng `iVBOR...`).

1.  Vào Power Apps, chọn nút **Gửi** (Button).
2.  Dán đoạn code sau vào thuộc tính `OnSelect` (Thay thế code cũ):

```powerapps
// 1. Lấy toàn bộ chuỗi JSON của ảnh
Set(varRawImage, JSON(PenInput_Signature.Image, JSONFormat.IncludeBinaryData));

// 2. Xử lý cắt chuỗi để lấy Base64 sạch
// Tìm vị trí dấu phẩy đầu tiên "," và lấy phần sau nó
Set(varCleanBase64, 
    Mid(
        varRawImage, 
        Find(",", varRawImage) + 1, 
        Len(varRawImage) - Find(",", varRawImage) - 1
    )
);

// 3. (Tùy chọn) Kiểm tra xem chuỗi có sạch không
// Bạn có thể tạo một Label tạm thời, gán Text = Left(varCleanBase64, 20)
// Nếu thấy hiện "iVBORw0KG..." là ĐÚNG.
// Nếu thấy hiện "data:image..." hoặc dấu ngoặc kép " là SAI.

// 4. Gửi sang Flow
// Giả sử Flow tên là CreatPdf, tham số đầu tiên là chữ ký
CreatPdf.Run(
    varCleanBase64,      // Chữ ký (Base64 sạch)
    TextInputTen.Text,   // Tên khách hàng
    TextInputEmail.Text  // Email
);
```

---

## BƯỚC 2: Cấu hình lại Flow (Power Automate)

Hãy sửa lại Flow `CreatPdf` của bạn.

### 1. Kiểm tra Trigger (PowerApps V2)
-   Đảm bảo bạn đã khai báo input cho chữ ký (ví dụ tên là `ChuKyBase64`).

### 2. Thêm Action "Compose" (Để kiểm tra)
-   Thêm action **Compose** ngay sau Trigger.
-   **Inputs**: Chọn biến `ChuKyBase64` từ Dynamic Content.
-   *Mục đích*: Sau khi chạy thử, bạn xem Output của bước này. Nó **phải** là chuỗi dài bắt đầu bằng `iVBOR...`. Nếu nó có `data:image...` thì quay lại Bước 1 sửa Power Apps.

### 3. Sửa Action "Populate a Microsoft Word template"
Đây là bước quyết định.

-   Mở action này ra.
-   Tìm đến trường **SignatureImage** (hoặc tên Tag bạn đặt trong Word).
-   **Xóa hết** nội dung cũ trong ô này.
-   Nhấp chuột vào ô đó.
-   Một cửa sổ nhỏ hiện ra bên cạnh (Dynamic Content). Chọn tab **Expression** (Biểu thức).
-   Nhập chính xác hàm sau:
    ```
    dataUriToBinary(concat('data:image/png;base64,', triggerBody()['text']))
    ```
    *(Lưu ý: `triggerBody()['text']` là tên biến input chữ ký của bạn. Cách an toàn nhất là bạn gõ `base64ToBinary()`, đặt con trỏ vào giữa 2 ngoặc, rồi chuyển sang tab Dynamic Content và chọn biến `ChuKyBase64`. Nó sẽ tự điền đúng tên biến).*
    
    Ví dụ nếu biến của bạn tên là `FileContent`, hàm sẽ là: `base64ToBinary(triggerBody()['file']['contentBytes'])` hoặc tương tự. **Hãy nhìn vào Dynamic Content để chọn đúng.**

-   Nhấn **OK**.
-   Lúc này trong ô SignatureImage sẽ hiện một biểu tượng hàm màu hồng/tím `fx base64ToBinary(...)`.

**TUYỆT ĐỐI KHÔNG** nhập thêm bất kỳ cái gì khác (không ngoặc nhọn `{}`, không `"$content-type"`, không dấu nháy kép). Chỉ duy nhất cái hàm màu tím đó thôi.

---

## BƯỚC 4: Cấu hình Convert File (Quan trọng)

Sau khi điền dữ liệu vào Word, bạn cần tạo file Word tạm rồi mới convert. Lỗi thường gặp là chọn sai ID file.

1.  **Action: Create file** (OneDrive for Business hoặc SharePoint)
    -   Tạo file Word tạm (ví dụ `temp.docx`).
    -   **File Content**: Chọn **Body** của bước *Populate a Microsoft Word template*.

2.  **Action: Convert file (using path)** (OneDrive for Business)
    *Lưu ý: Nếu dùng connector OneDrive, nó thường yêu cầu "File" (là ID) hoặc "File Path".*
    
    -   **File (hoặc File Path)**: Hãy xóa hết nội dung cũ.
    -   Chọn từ Dynamic Content: Tìm mục **Create file** (bước vừa tạo ở trên) -> Chọn **Id**.
    -   *Mẹo*: Nếu chọn **Id** mà vẫn lỗi "File not found", hãy thử chọn **Path** (hoặc **Full Path**) từ bước *Create file*.
    -   **Target format**: `pdf`.

    *Giải thích*: Action Convert của OneDrive đôi khi yêu cầu ID, đôi khi yêu cầu Path tùy version. Nhưng **Id** thường ổn định hơn. Nếu ID không chạy, hãy đổi sang Path.

3.  **Action: Create file** (Lần 2 - Lưu PDF)
    -   **File Content**: Chọn **File content** của bước *Convert file*.

---

## BƯỚC 5: Kiểm tra Word Template

Đảm bảo file Word mẫu của bạn đúng chuẩn:
1.  Mở file Word mẫu.
2.  Bấm vào khung ảnh (Picture Content Control).
3.  Tab **Developer** -> **Properties**.
4.  Đảm bảo **Tag** là `SignatureImage` (hoặc tên bạn dùng trong Flow).
5.  Đảm bảo **Title** cũng là `SignatureImage`.
6.  Lưu và ghi đè file mẫu trên OneDrive.

---

## BƯỚC 4: Chạy thử (Test)

1.  Vào Power Apps, ký tên và nhấn Gửi.
2.  Sang Power Automate, xem **Run History** của Flow vừa chạy.
3.  Nếu vẫn lỗi, hãy bấm vào lượt chạy đó (Failed):
    -   Xem bước **Compose**: Input nhận được là gì? Có phải `iVBOR...` không?
    -   Xem bước **Populate...**: Lỗi chi tiết là gì?

Nếu bạn làm đúng Bước 1 (cắt chuỗi) và Bước 2 (dùng hàm `base64ToBinary`), chắc chắn sẽ thành công.
