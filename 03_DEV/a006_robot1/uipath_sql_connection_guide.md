# Hướng dẫn kết nối UiPath với SQL Database

Tài liệu này hướng dẫn chi tiết cách sử dụng UiPath để kết nối đến SQL Server, lấy dữ liệu và ghi ra file Excel/CSV.

## 1. Cài đặt Package cần thiết

Để làm việc với Database, bạn cần cài đặt gói activity **UiPath.Database.Activities**.

1.  Mở UiPath Studio.
2.  Vào tab **Manage Packages**.
3.  Chọn **All Packages** (hoặc Official).
4.  Tìm kiếm từ khóa `UiPath.Database.Activities`.
5.  Chọn và nhấn **Install**, sau đó **Save**.

## 2. Quy trình thực hiện trong UiPath

Để quản lý kết nối tốt hơn và tái sử dụng cho nhiều truy vấn, chúng ta sẽ sử dụng activity **Connect** để tạo một biến kết nối.

### Bước 1: Thiết lập kết nối (Connect)

1.  Tìm activity **Connect** (nằm trong `App Integration > Database`).
2.  Kéo vào workflow.
3.  Nhấn vào nút **Configure Connection...** -> **Connection Wizard**.
4.  Chọn **Microsoft SQL Server** (System.Data.SqlClient).
5.  Điền thông tin Server, Authentication, Database -> Test Connection -> OK.
6.  **Quan trọng**: Tại phần Properties của activity Connect:
    -   Tìm mục **Output** -> **DatabaseConnection**.
    -   Nhấn `Ctrl + K` để tạo biến mới, ví dụ: `dbConnection`.
    -   Biến này sẽ chứa phiên làm việc với database.

### Bước 2: Truy vấn dữ liệu (Execute Query)

1.  Tìm activity **Execute Query**.
2.  Kéo vào workflow (bên dưới activity Connect).
3.  **Properties**:
    -   **ExistingDbConnection**: Điền biến `dbConnection` vừa tạo ở Bước 1.
    -   **Sql**: Nhập câu lệnh SQL (ví dụ: `"SELECT * FROM Users"`).
    -   **DataTable** (Output): Nhấn `Ctrl + K` tạo biến `dtResult`.
    -   *(Lưu ý: Khi đã dùng `ExistingDbConnection`, bạn KHÔNG cần điền `ConnectionString` và `ProviderName` nữa).*

### Bước 3: Ngắt kết nối (Disconnect) - Tùy chọn

Mặc dù UiPath thường tự đóng kết nối khi process kết thúc, nhưng để đảm bảo giải phóng tài nguyên (best practice), bạn nên ngắt kết nối khi đã xong việc.

1.  Tìm activity **Disconnect**.
2.  **DatabaseConnection**: Điền biến `dbConnection`.

### Bước 4: Ghi dữ liệu ra file (Write Range)

Tương tự như trước, dùng biến `dtResult` để ghi ra file.

**Ghi ra Excel:**
1.  Activity **Write Range Workbook**.
2.  Path: `"Data\Output.xlsx"`.
3.  DataTable: `dtResult`.

## 3. Ví dụ Workflow mẫu

Dưới đây là tóm tắt các bước trong một Sequence:

1.  **Connect**:
    -   Configure: (Chuỗi kết nối đến DB)
    -   Output (DatabaseConnection): `myDbConn`
# Hướng dẫn kết nối UiPath với SQL Database

Tài liệu này hướng dẫn chi tiết cách sử dụng UiPath để kết nối đến SQL Server, lấy dữ liệu và ghi ra file Excel/CSV.

## 1. Cài đặt Package cần thiết

Để làm việc với Database, bạn cần cài đặt gói activity **UiPath.Database.Activities**.

1.  Mở UiPath Studio.
2.  Vào tab **Manage Packages**.
3.  Chọn **All Packages** (hoặc Official).
4.  Tìm kiếm từ khóa `UiPath.Database.Activities`.
5.  Chọn và nhấn **Install**, sau đó **Save**.

## 2. Quy trình thực hiện trong UiPath

Để quản lý kết nối tốt hơn và tái sử dụng cho nhiều truy vấn, chúng ta sẽ sử dụng activity **Connect** để tạo một biến kết nối.

### Bước 1: Thiết lập kết nối (Connect)

1.  Tìm activity **Connect** (nằm trong `App Integration > Database`).
2.  Kéo vào workflow.
3.  Nhấn vào nút **Configure Connection...** -> **Connection Wizard**.
4.  Chọn **Microsoft SQL Server** (System.Data.SqlClient).
5.  Điền thông tin Server, Authentication, Database -> Test Connection -> OK.
6.  **Quan trọng**: Tại phần Properties của activity Connect:
    -   Tìm mục **Output** -> **DatabaseConnection**.
    -   Nhấn `Ctrl + K` để tạo biến mới, ví dụ: `dbConnection`.
    -   Biến này sẽ chứa phiên làm việc với database.

### Bước 2: Truy vấn dữ liệu (Execute Query)

1.  Tìm activity **Execute Query`.
2.  Kéo vào workflow (bên dưới activity Connect).
3.  **Properties**:
    -   **ExistingDbConnection**: Điền biến `dbConnection` vừa tạo ở Bước 1.
    -   **Sql**: Nhập câu lệnh SQL (ví dụ: `"SELECT * FROM Users"`).
    -   **DataTable** (Output): Nhấn `Ctrl + K` tạo biến `dtResult`.
    -   *(Lưu ý: Khi đã dùng `ExistingDbConnection`, bạn KHÔNG cần điền `ConnectionString` và `ProviderName` nữa).*

### Bước 3: Ngắt kết nối (Disconnect) - Tùy chọn

Mặc dù UiPath thường tự đóng kết nối khi process kết thúc, nhưng để đảm bảo giải phóng tài nguyên (best practice), bạn nên ngắt kết nối khi đã xong việc.

1.  Tìm activity **Disconnect**.
2.  **DatabaseConnection**: Điền biến `dbConnection`.

### Bước 4: Ghi dữ liệu ra file (Write Range)

Tương tự như trước, dùng biến `dtResult` để ghi ra file.

**Ghi ra Excel:**
1.  Activity **Write Range Workbook**.
2.  Path: `"Data\Output.xlsx"`.
3.  DataTable: `dtResult`.

## 3. Ví dụ Workflow mẫu

Dưới đây là tóm tắt các bước trong một Sequence:

1.  **Connect**:
    -   Configure: (Chuỗi kết nối đến DB)
    -   Output (DatabaseConnection): `myDbConn`
2.  **Execute Query**:
    -   ExistingDbConnection: `myDbConn`
    -   Sql: `"SELECT Top 10 * FROM Customers"`
    -   Output (DataTable): `dtCustomers`
3.  **Disconnect**:
    -   DatabaseConnection: `myDbConn`
4.  **Write Range Workbook**:
    -   Path: `"Result.xlsx"`
    -   DataTable: `dtCustomers`

> [!TIP]
> Nếu câu truy vấn SQL của bạn có tham số động (ví dụ `WHERE ID = @ID`), hãy sử dụng tính năng **Parameters** trong activity Execute Query để truyền biến vào an toàn, tránh nối chuỗi thủ công (dễ bị SQL Injection).

## 4. Ví dụ cụ thể: Lấy dữ liệu từ bảng 'documentoGarantia'

Giả sử bạn cần lấy tất cả dữ liệu từ bảng `documentoGarantia` và lưu vào file Excel.

**Câu lệnh SQL:**
```sql
SELECT * FROM documentoGarantia
```

**Quy trình trong UiPath:**

1.  **Connect**:
    -   Thiết lập kết nối đến DB chứa bảng `documentoGarantia`.
    -   Output (DatabaseConnection): `dbConn`

2.  **Execute Query**:
    -   ExistingDbConnection: `dbConn`
    -   Sql: `"SELECT * FROM documentoGarantia"`
    -   Output (DataTable): `dtDocumentoGarantia`

3.  **Write Range Workbook**:
    -   Workbook Path: `"Data\DocumentoGarantia.xlsx"`
    -   SheetName: `"Data"`
    -   DataTable: `dtDocumentoGarantia`
    -   AddHeaders: `True`

4.  **Disconnect**:
    -   DatabaseConnection: `dbConn`

> [!TIP]
> Nếu bạn chỉ muốn lấy các dòng có điều kiện (ví dụ `Status = 'Active'`), hãy sửa câu SQL thành:
> `"SELECT * FROM documentoGarantia WHERE Status = 'Active'"`

> [!TIP]
> Nếu câu truy vấn SQL của bạn có tham số động (ví dụ `WHERE ID = @ID`), hãy sử dụng tính năng **Parameters** trong activity Execute Query để truyền biến vào an toàn, tránh nối chuỗi thủ công (dễ bị SQL Injection).
