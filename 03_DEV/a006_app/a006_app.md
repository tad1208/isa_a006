# A006 - Provider Evaluation App Development Guide

Tài liệu này là **Master Guide** cho việc phát triển ứng dụng A006. Nó mô tả kiến trúc, dữ liệu, logic nghiệp vụ và liên kết đến các hướng dẫn chi tiết (Step-by-step Guides) cho từng phần.

---

## 1. Tổng quan (Overview)

**Mục đích**: Ứng dụng Power Apps cho phép các bên liên quan (Técnico, SST, MA, Approver) thực hiện đánh giá nhà cung cấp định kỳ.
**Kiến trúc**:
- **Robot 1 (Dispatcher)**: Đọc Excel, tạo bản ghi đánh giá mới trong SharePoint (`ongoingEvaluations`).
- **Power App**: User nhập liệu, chấm điểm và phê duyệt.
- **Robot 2 (Performer)**: Đọc các đánh giá đã hoàn thành, tính toán điểm tổng, tạo báo cáo PDF và gửi email.

---
 

## 2. Cấu trúc Dữ liệu (SharePoint Schema)
Dữ liệu được lưu trữ trên SharePoint Online.
> **Sample**: `https://isaempresas.sharepoint.com/sites/EvaluaciondeProveedores_INTERVIALCHILESA/Lists/ongoingEvaluation/AllItems.aspx?viewid=651cc428%2Dac3f%2D442d%2Db04e%2Dd8c600f89fd6`

App sử dụng 2 sharepoint list:
- `ongoingEvaluations` (chứa các đánh giá đang xử lý)
- `completedEvaluations` (chứa các đánh giá đã hoàn thành, có cấu trúc giống hệt `ongoingEvaluations`)

### 2.1. List: `ongoingEvaluations`
Lưu trữ các đánh giá đang xử lý. Robot 1 tạo, App cập nhật, Robot 2 xử lý.

#### A. Identification & Primary Key
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `item_id` | Text | **Primary Key**. Format: `{documento_compras}_{YYYYMMDD}` |
| `documento_compras` | Text | Mã hợp đồng (Contract ID) |
| `fecha_evaluacion` | Date | Ngày đánh giá |

#### B. Contract & Provider Information
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `fecha_inicio_validez` | Date | Ngày bắt đầu hiệu lực |
| `fecha_fin_validez` | Date | Ngày kết thúc hiệu lực |
| `sociedad` | Text | Mã công ty (Company Code) |
| `nombre_proveedor` | Text | Tên nhà cung cấp |
| `objeto_contrato` | Text | Mục tiêu hợp đồng |

#### C. Responsible Persons (Phân quyền)
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `nombre_responsable_tecnico` | Text | Tên Responsable Técnico |
| `email_responsable_tecnico` | Text | Email Responsable Técnico |
| `nombre_responsable_sst` | Text | Tên Responsable SST |
| `email_responsable_sst` | Text | Email Responsable SST |
| `nombre_responsable_ma` | Text | Tên Responsable MA |
| `email_responsable_ma` | Text | Email Responsable MA |
| `nombre_jefe_area` | Text | Tên Approver (Jefe de Área) |
| `email_jefe_area` | Text | Email Approver |
| `nombre_area` | Text | Tên phòng ban |

#### D. Responsibility Flags (Toggle)
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `responsable_sst_responde` | Yes/No | True = SST làm; False = Técnico làm thay |
| `responsable_ma_responde` | Yes/No | True = MA làm; False = Técnico làm thay |

#### E. Evaluation Scores (Điểm số)
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `calificacion_criterio_calidad` | Number | Điểm Chất lượng (0-100) |
| `calificacion_criterio_oportunidad` | Number | Điểm Tiến độ (0-100) |
| `calificacion_criterio_gestion` | Number | Điểm Quản lý (0-100) |
| `calificacion_criterio_sst` | Number | Điểm An toàn (0-100) |
| `calificacion_criterio_ma` | Number | Điểm Môi trường (0-100) |
| `cumple_codigo_etica` | Yes/No | Tuân thủ đạo đức? (False = 0 điểm) |

#### F. Comments & Observations
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `observaciones_tecnico` | Note | Ghi chú từ Técnico |
| `observaciones_sst` | Note | Ghi chú từ SST |
| `observaciones_ma` | Note | Ghi chú từ MA |
| `observaciones_jefe_area` | Note | Ghi chú từ Approver |

#### G. Score Calculations & Improvement
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `nota_subtotal` | Number | Tổng điểm các tiêu chí |
| `nota_total` | Number | Điểm cuối cùng (0 nếu vi phạm đạo đức) |
| `plan_mejoramiento` | Note | Kế hoạch cải thiện |

#### H. Approval Workflow
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `aprobada` | Yes/No | Đã phê duyệt? |
| `rechazada` | Yes/No | Bị từ chối? |
| `numero_rechazos` | Number | Số lần từ chối |

#### I. Robot 2 Outputs (CSV & Letter)
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `csv_generado` | Yes/No | CSV đã tạo? |
| `url_csv` | URL | Link file CSV |
| `carta_generada` | Yes/No | Carta đã tạo? |
| `url_carta` | URL | Link file Carta (chưa ký) |

#### J. Digital Signature
| Tên trường (Internal) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `firma_responsable_technico` | Yes/No | Técnico đã ký? |
| `firma_jefe_area` | Yes/No | Approver đã ký? |
| `url_carta_firmada` | URL | Link file Carta đã ký đầy đủ |

---

## 3. Thiết lập Môi trường (Setup)

Trước khi phát triển, cần thiết lập Solution và Biến môi trường.

*   **Hướng dẫn tạo Solution & Environment Variables**: [Xem chi tiết tại `solution_guide.md`](./solution_guide.md)
*   **Hướng dẫn cấu hình App.OnStart (Dev/Prod Modes)**: [Xem chi tiết tại `app_setup_guide.md`](./app_setup_guide.md)

---

## 4. Màn hình & Logic (Screens)

### 4.1. Landing Page (`scrLanding`)
**Mô tả**: Dashboard chính, hiển thị lời chào và danh sách các đánh giá cần xử lý.
**Logic**:
*   Hiển thị số lượng đánh giá chờ (`Pending Count`).
*   Filter danh sách dựa trên Email người dùng hiện tại (`gblUserEmail`) so khớp với 4 trường email trách nhiệm.
*   Điều hướng đến các màn hình chức năng.

👉 **Hướng dẫn thực hiện chi tiết**: [Xem `landing_page_guide.md`](./landing_page_guide.md)

### 4.2. Evaluaciones (`scrEvaluaciones`)
**Mô tả**: Màn hình hợp nhất cho cả Đánh giá (Evaluator) và Phê duyệt (Approver).
**Logic Phân quyền Động**:
*   **Evaluators (Técnico/SST/MA)**: Có quyền chỉnh sửa (Edit) các section tương ứng của mình nếu đánh giá chưa được phê duyệt (`aprobada = false`).
*   **Approver (Jefe de Área)**: Xem toàn bộ thông tin (Read-only) và có quyền Phê duyệt/Từ chối (`Approval Section`).
*   **Trạng thái**: Khi `aprobada = true`, toàn bộ màn hình chuyển sang chế độ Read-only.

👉 **Hướng dẫn thực hiện chi tiết**: [Xem `scrEvaluaciones_guide.md`](./scrEvaluaciones_guide.md)

*(Màn hình này thay thế cho `scrEvaluateProviders` và `scrApprover` cũ)*

### 4.4. Sign Letters (`scrSignLetters`)
**Mô tả**: Màn hình ký số cho các thư đánh giá đã được tạo.
**Behavior**:
*   Chọn thư cần ký từ danh sách.
*   Xem trước nội dung thư (PDF/HTML).
*   Ký tên bằng **Pen Input** control hoặc Upload ảnh.
*   Lưu chữ ký (Base64) vào SharePoint để Robot 2 xử lý.

*(Chưa có hướng dẫn chi tiết riêng)*

---

---

## 5. Design & UI Specifications (New)

> **Source**: `design/a006_app/design.md`

### 5.1. Global Concept
*   **Style**: Inspired by "Inicio – ISA Vías" website. Clean, white space, rounded buttons.
*   **Palette**:
    *   Primary Blue: `#003087` (Header, Primary Buttons, Text)
    *   Accent Blue: `#009AFE` (Hover states)
    *   Accent Orange: `#FF6A00` (Decorations)
    *   Neutral Gray: `#767676` (Secondary/Negative Buttons)
*   **Typography**:
    *   Titles: `AmsiPro` (or similar sans-serif), Bold, `#003087`.
    *   Body: `Roboto`, Regular, `#003087`.

### 5.2. UI Components
*   **Header**: Blue background (`#003087`), White text. Left: Logo. Right: Navigation pills.
*   **Buttons**:
    *   **Primary** (Submit/Approve): Blue `#003087` + Orange accent dot. Hover: Lighter Blue `#009AFE`.
    *   **Secondary** (Cancel/Reset): Gray `#767676` + Orange accent dot.
*   **Questions**:
    *   **Unanswered**: Light blue background, Blue text.
    *   **Hover**: Bright Blue `#009AFE` background, White text.
    *   **Selected**: Dark Blue `#003087` background, White text. Score appears on right.

---

## 6. Evaluation Criteria & Logic (Detailed)

> **Source**: `design/a006_app/evaluacion_criteria.md`

### 6.1. Structure & Weights
Total Score: **100 points**.

1.  **Calidad (30 pts)**: 5 questions x 6 pts.
2.  **Oportunidad (30 pts)**: 1 question x 30 pts.
3.  **Gestión (20 pts)**: 2 questions (10 pts each).
4.  **SST (10 pts)**: 1 question x 10 pts.
5.  **Medioambiente (10 pts)**: 1 question x 10 pts.

### 6.2. Detailed Questions (Criteria)

#### A. Calidad (5 Questions)
1.  **Bienes - Cumplimiento**: Cumple / Regular / Grave.
2.  **Bienes - Cooperación**: Cumple / Regular / Incumplimiento.
3.  **Servicios - Cumplimiento**: Cumple / Incumplimiento menor / Incumplimiento grave.
4.  **Servicios - Personal**: Cumple / Regular / Grave.
5.  **Servicios - Atención**: Cumple / Regular / Grave.

#### B. Oportunidad (1 Question)
1.  **Cumplimiento plazos**: Cumple / Sin impacto / Con impacto.

#### C. Gestión (2 Questions)
1.  **Documentos Inicio**: Cumple / Incumplimiento.
2.  **Documentos Contractuales**: Cumple / Regular / Grave.

#### D. SST (1 Question)
1.  **Requisitos Salud Ocupacional**: Cumple / Incumplimiento menor / Incumplimiento grave.

#### E. Medioambiente (1 Question + Gate)
*   **Gate (Yes/No)**: "Responsable de criterio de medioambiente debe responder?".
    *   If **No**: Técnico answers.
    *   If **Yes**: MA Specialist answers.
1.  **Requisitos Ambientales**: Cumple / Incumplimiento menor / Incumplimiento grave.

#### F. Código de Ética (Override)
*   **Question**: "El contratista incurrió en faltas al código de ética?" (Yes/No).
*   **Logic**:
    *   **No**: Total = Sum of points.
    *   **Yes**: **Total = 0** (Override).

### 6.3. Scoring Logic
*   **3-Option Questions**:
    *   Option 1: 100% points.
    *   Option 2: 50% points.
    *   Option 3: 0% points.
*   **2-Option Questions**: 100% or 0%.

---

## 7. Controls & Standards (Quy chuẩn)

*   **Controls**: Sử dụng **Modern Containers** cho layout, **Classic Controls** cho Input/Button (để dễ styling).
*   **Naming**: `con` (Container), `lbl` (Label), `txt` (Input), `btn` (Button), `gbl` (Global Var), `var` (Local Var).
*   **Tham khảo Controls**: [Xem `CONTROLS_QUICKREF.md`](./CONTROLS_QUICKREF.md)

