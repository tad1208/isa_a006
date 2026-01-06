# SharePoint List: `ongoingEvaluations`

## 1. Tổng quan

- **Tên list**: `ongoingEvaluations`  
- **Mục đích**: Lưu **tất cả các đánh giá đang diễn ra (ongoing evaluations)** cho từng hợp đồng.  
- **Ai ghi dữ liệu?**
  - **Robot 1**: tạo mới và cập nhật thông tin header của đánh giá (contract, người phụ trách, flags ai trả lời phần nào…).  
  - **Power App**: người dùng nhập điểm, tick các tiêu chí, ghi nhận phê duyệt / từ chối.  
  - **Robot 2**: sau khi đánh giá được phê duyệt, Robot 2 cập nhật các cờ `csv_generado`, `carta_generada`, `firma_*`, `url_*` và chuẩn bị dữ liệu chuyển sang `completedEvaluations`.

> 📝 Lưu ý: các cột giữ nguyên tên **tiếng Spanish** như yêu cầu; trong phần mô tả sẽ có chú thích ngắn để dễ hiểu.

---

## 2. Data Dictionary – `ongoingEvaluations`

### 2.1. Thông tin hợp đồng & header evaluation

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `documento_compras` | Single line of text | Robot 1 | ID hợp đồng từ SAP – **“Documento compras”** (mã hợp đồng / purchasing document). Đây là khóa chính nghiệp vụ để biết evaluation thuộc hợp đồng nào. |
| `fecha_evaluacion` | DateTime | Robot 1 | Ngày đánh giá – **“fecha evaluación”** (evaluation date) mà Robot 1 tạo record cho kỳ đánh giá này. |
| `fecha_inicio_validez` | DateTime | Robot 1 | Ngày bắt đầu hiệu lực hợp đồng – **“In.período validez”** (start of validity period). |
| `fecha_fin_validez` | DateTime | Robot 1 | Ngày kết thúc hiệu lực hợp đồng – **“Fin período validez”** (end of validity period). |
| `sociedad` | Single line of text | Robot 1 | **“Sociedad”** – mã công ty / company code. Dùng để xác định các evaluator phù hợp theo “sociedad”. |
| `nombre_proveedor` | Single line of text | Robot 1 | Tên nhà cung cấp – **“Nombre proveedor”** (supplier name). Dùng để hiển thị trong app và trong thư đánh giá. |
| `objeto_contrato` | Single line of text | Robot 1 | Mô tả mục đích hợp đồng – **“Obj.Contrato”** (contract objective / contract subject). Hiển thị ở màn hình đánh giá và trên thư. |

---

### 2.2. Thông tin người chịu trách nhiệm & luồng phê duyệt

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `nombre_responsable_tecnico` | Single line of text | Robot 1 | Tên người phụ trách kỹ thuật / admin hợp đồng – **“responsable técnico”**. Thường lấy từ **Maestro colaboradores**. |
| `email_responsable_tecnico` | Single line of text | Robot 1 | Email corporate của responsable técnico. |
| `nombre_responsable_sst` | Single line of text | Robot 1 | Tên người đánh giá tiêu chí **SST** – **“Seguridad y Salud en el Trabajo”** (an toàn & sức khỏe nghề nghiệp). |
| `email_responsable_sst` | Single line of text | Robot 1 | Email người đánh giá SST. |
| `nombre_responsable_ma` | Single line of text | Robot 1 | Tên người đánh giá tiêu chí **Medio Ambiente** (môi trường). |
| `email_resopnsable_ma` ⚠️ | Single line of text | Robot 1 | Email người đánh giá Medio Ambiente. **Lưu ý chính tả**: trong spec gốc bị typo là `email_resopnsable_ma`; khi thiết kế có thể cân nhắc dùng đúng là `email_responsable_ma` nếu khách cho phép. |
| `nombre_jefe_area` | Single line of text | Robot 1 | Tên **jefe de área** – trưởng bộ phận phê duyệt đánh giá (approver & signer). |
| `email_jefe_area` | Single line of text | Robot 1 | Email của jefe de área (người phê duyệt cuối). |
| `nombre_area` | Single line of text | Robot 1 | Tên **“Área”** – tên phòng ban / bộ phận mà jefe de área phụ trách. |

---

### 2.3. Cờ phân công ai trả lời từng phần

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `responsable_sst_responde` | Yes/No (bool) | Robot 1 | Nếu **true** → phần câu hỏi về **SST** sẽ do người `nombre_responsable_sst` trả lời. Nếu **false** → phần SST sẽ do `responsable_tecnico` tự trả lời (tức admin hợp đồng tự trang trải luôn SST). |
| `responsable_ma_responde` | Yes/No (bool) | Robot 1 / App | Nếu **true** → phần câu hỏi **Medio Ambiente** do `nombre_responsable_ma` trả lời. Nếu **false** → admin hợp đồng trả lời luôn phần MA. (Có thể được set mặc định từ Robot 1 và cho phép override trong app nếu business muốn). |

---

### 2.4. Điểm (calificaciones) cho từng tiêu chí

> Các cột **calificación** là điểm đánh giá (thường dạng thang điểm 1–7 hoặc tương tự, tùy business rule).

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `calificacion_criterio_calidad` | Number (int) | App | Điểm tiêu chí **Calidad** – chất lượng dịch vụ / sản phẩm. |
| `calificacion_criterio_oportunidad` | Number (int) | App | Điểm tiêu chí **Oportunidad** – tính đúng hạn / đúng thời điểm (timeliness). |
| `calificacion_criterio_gestion` | Number (int) | App | Điểm tiêu chí **Gestión** – quản lý, phối hợp, đáp ứng yêu cầu. |
| `calificacion_criterio_sst` | Number (int) | App | Điểm tiêu chí **SST – Seguridad y Salud en el Trabajo** (an toàn & sức khỏe nghề nghiệp). |
| `calificacion_criterio_ma` | Number (int) | App | Điểm tiêu chí **Medio Ambiente** (tác động môi trường, tuân thủ quy định). |

---

### 2.5. Các checkbox / điều kiện bổ sung

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `cumple_codigo_etica` | Yes/No (bool) | App | Nhà cung cấp có **tuân thủ “Código de ética / código de conducta”** không. Dùng để điều chỉnh điểm hoặc quyết định phê duyệt. |

---

### 2.6. Nhận xét (observaciones) từ từng vai trò

> Tất cả đều là trường text tự do, cho phép người dùng ghi comment.

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `observaciones_tecnico` | Multiple lines of text | App | Comment của **responsable técnico** (admin hợp đồng) về hiệu suất nhà cung cấp. |
| `observaciones_sst` | Multiple lines of text | App | Comment của người phụ trách **SST** (nếu có). |
| `observaciones_ma` | Multiple lines of text | App | Comment của người phụ trách **Medio Ambiente** (nếu có). |
| `observaciones_jefe_area` | Multiple lines of text | App | Comment / justification của **jefe de área** khi phê duyệt hoặc từ chối. |

---

### 2.7. Điểm tổng & kế hoạch cải thiện

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `nota_subtotal` | Number (int) | App | Điểm **sub-total** (thường là tổng/average các điểm tiêu chí trước khi áp dụng điều chỉnh do `cumple_codigo_etica` hoặc business rule khác). |
| `nota_total` | Number (int) | App | Điểm **tổng cuối cùng** sau tất cả điều chỉnh (đây là điểm thể hiện trên báo cáo, CSV và thư gửi nhà cung cấp). |
| `plan_mejoramiento` | Multiple lines of text | App | **“Plan de mejoramiento”** – kế hoạch cải thiện, hành động khắc phục khi kết quả không đạt kỳ vọng. Có thể để trống nếu không cần. |

---

### 2.8. Trạng thái phê duyệt (approval state)

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `aprobada` | Yes/No (bool) | App | Đánh giá **được phê duyệt** (approved) bởi jefe de área.<br>**true** = approved, đủ điều kiện để Robot 2 tạo CSV và thư. |
| `rechazada` | Yes/No (bool) | App | Đánh giá **bị từ chối** (rejected). Khi `rechazada = true`, app thường yêu cầu người dùng chỉnh sửa lại nội dung, sau đó gửi lại. |
| `numero_rechazos` | Number (int) | App | Số lần bị từ chối – **“número de rechazos”**. Giúp theo dõi việc phải gửi lại bao nhiêu lần. |

> Business rule gợi ý:  
> - Chỉ khi `aprobada = true` và `rechazada = false` thì Robot 2 mới xử lý.  
> - Khi user sửa lại sau khi bị từ chối, app có thể tăng `numero_rechazos` và reset status.

---

### 2.9. Thông tin đầu ra CSV & thư (Robot 2)

| Column | Type (SharePoint) | Ghi bởi | Giải thích |
|-------|--------------------|--------|-----------|
| `csv_generado` | Yes/No (bool) | Robot 2 | **true** khi Robot 2 đã tạo file CSV tương ứng với evaluation này. |
| `url_csv` | Hyperlink or Single line of text | Robot 2 | URL/đường dẫn đến file CSV được lưu trên SharePoint hoặc location khác. |
| `carta_generada` | Yes/No (bool) | Robot 2 | **true** khi Robot 2 đã tạo file thư đánh giá (PDF) – **“carta de evaluación”**. |
| `url_carta` | Hyperlink or Single line of text | Robot 2 | URL tới bản **carta** (thư) chưa ký. |
| `firma_responsable_tecnico` | Yes/No (bool) | Robot 2 / hệ thống ký | **true** khi thư đã được responsable técnico ký (digital hoặc upload). |
| `firma_jefe_area` | Yes/No (bool) | Robot 2 / hệ thống ký | **true** khi thư đã có chữ ký của jefe de área. |
| `url_carta_firmada` | Hyperlink or Single line of text | Robot 2 | URL tới bản **carta final** đã được ký đầy đủ (tài liệu chính dùng để lưu trữ / gửi NCC). |

---

## 3. Gợi ý thêm cho implementation

- **Khóa chính kỹ thuật**: dùng `ID` mặc định của SharePoint.  
- **Khóa nghiệp vụ**: kết hợp `documento_compras` + (counter từ list `evaluationsCounter`) + kỳ đánh giá (nếu có).  
- **Robot 1**:
  - Chỉ **tạo mới** row nếu chưa có evaluation active cho kỳ đó.
  - Luôn đảm bảo điền đầy đủ các cột “header” (contract + responsables) trước khi người dùng vào app.
- **Robot 2**:
  - Chỉ lấy các row `aprobada = true` & `rechazada = false` & (`csv_generado = false` hoặc `carta_generada = false`) để xử lý.
  - Sau khi xử lý xong, có thể chuyển row sang `completedEvaluations` và/hoặc set thêm flag riêng tùy yêu cầu khách.

Nếu bạn muốn, bước tiếp theo mình có thể giúp bạn:
- Định nghĩa **schema chi tiết ở dạng JSON** (đúng kiểu SharePoint: field internal name, display name, type), hoặc  
- Vẽ mapping từ `ongoingEvaluations` sang `completedEvaluations` và sang file CSV output để dev Robot 2.  
