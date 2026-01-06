
# Component: `cmpPrimaryButton` (Power Apps)

## Overview / Tổng Quan

### Tính năng (Features)
- Nút primary action button tiêu chuẩn cho các hành động tích cực (submit, approve, confirm)
- Hỗ trợ đầy đủ các trạng thái: Normal, Hover, Pressed, Disabled
- Icon chevron bên phải có thể bật/tắt
- Sự kiện OnSelect có thể tùy chỉnh từ bên ngoài component
- Border radius bo tròn đồng nhất
- Có thể enable/disable theo logic nghiệp vụ

### Mục đích (Purpose)
Được sử dụng cho các hành động chính trong app như:
- **Enviar** (Submit): Gửi đánh giá
- **Aprobar** (Approve): Phê duyệt đánh giá
- Các hành động xác nhận tích cực khác

Component này đảm bảo tính nhất quán về giao diện và hành vi của tất cả các nút primary trong toàn bộ ứng dụng.

### UI Specification (theo app_design.md)
Theo **Section 4.1** của `app_design.md`:

**Visual:**
- **Background (Normal)**: `#003087` (primary blue)
- **Background (Hover)**: `#009AFE` (accent blue)
- **Background (Pressed)**: `#003087` với độ fade -20%
- **Background (Disabled)**: `#003087` với opacity 30%
- **Font color**: `#FFFFFF` (white) - tất cả trạng thái
- **Border radius**: 20px (bo tròn đồng đều 4 góc)
- **Border thickness**: 0 (không viền)
- **Height**: 48px
- **Width**: 220px (default, có thể điều chỉnh)

**Icon decoration:**
- Icon ChevronRight ở bên phải
- Màu icon: White, tự động fade khi disabled
- Có thể ẩn icon thông qua property `ShowIcon`

**Behavior:**
- Hover: Background chuyển sang `#009AFE`, text vẫn white
- Pressed: Background fade darker
- Disabled: Background mờ đi, text mờ đi, không thể click

---

## 1. Tạo Component

1. Mở **Components** → **New component**
2. Đặt tên: `cmpPrimaryButton`
3. Chỉnh kích thước: Width = 220, Height = 48

> Root component chỉ là khung chứa.

---

## 2. Thêm Button vào component

1. Insert → **Button**, đặt tên `btnMain`.

### Thuộc tính của `btnMain`

```powerfx
// Position & size
btnMain.X = 0
btnMain.Y = 0
btnMain.Width  = Parent.Width
btnMain.Height = Parent.Height

// Corners
btnMain.RadiusTopLeft = 20; btnMain.RadiusTopRight = 20;
btnMain.RadiusBottomLeft = 20; btnMain.RadiusBottomRight = 20;
btnMain.BorderThickness = 0
```

---

## 3. Tạo Custom Properties

### 3.1 Property: `Text` (Input, Text)
Default: `"Primary Button"`
**Bind:** `btnMain.Text = Parent.Text`

### 3.2 Property: `IsEnabled` (Input, Boolean)
Default: `true`
**Bind:**
```powerfx
btnMain.DisplayMode = If(Parent.IsEnabled, DisplayMode.Edit, DisplayMode.Disabled)
```

### 3.3 Property: `ShowIcon` (Input, Boolean)
Default: `true`

### 3.4 Property: `OnSelectAction` (Event)
**Bind:**
```powerfx
btnMain.OnSelect = If(Parent.IsEnabled, OnSelectAction())
```

---

## 4. Định nghĩa màu trạng thái
```powerfx
btnMain.Fill = ColorValue("#003087")
btnMain.HoverFill = ColorValue("#009AFE")
btnMain.PressedFill = ColorFade(ColorValue("#003087"), -20%)
btnMain.DisabledFill = RGBA(0,48,135,0.3)
btnMain.Color = Color.White
```

## 5. Thêm Icon Chevron
Insert **Icon ChevronRight** (`icoArrow`).
```powerfx
icoArrow.Visible = Parent.ShowIcon
icoArrow.Color = Switch(btnMain.DisplayMode, DisplayMode.Disabled, ColorFade(Color.White, 40%), Color.White)
```

# Component: `cmpSecondaryButton` (Power Apps)

## Overview / Tổng Quan

### Tính năng (Features)
- Nút secondary/negative action button tiêu chuẩn cho các hành động phụ hoặc hủy bỏ
- Hỗ trợ đầy đủ các trạng thái: Normal, Hover, Pressed, Disabled
- Icon decoration bên phải (tương tự primary button)
- Sự kiện OnSelect có thể tùy chỉnh từ bên ngoài
- Border radius bo tròn đồng nhất
- Có thể enable/disable theo logic nghiệp vụ

### Mục đích (Purpose)
Được sử dụng cho các hành động phụ/tiêu cực trong app như:
- **Limpiar** (Reset/Clear): Xóa form dữ liệu
- **Cancelar** (Cancel): Hủy bỏ hành động
- **Rechazar** (Reject): Từ chối đánh giá
- Các hành động phụ khác không phải là primary action

Component này giúp người dùng phân biệt rõ ràng giữa hành động chính (primary) và hành động phụ/hủy bỏ (secondary) thông qua màu sắc.

### UI Specification (theo app_design.md)
Theo **Section 4.2** của `app_design.md`:

**Visual:**
- **Background (Normal)**: `#767676` (neutral gray)
- **Background (Hover)**: Tương tự primary button logic nhưng base color là gray
- **Background (Pressed)**: Gray với độ fade
- **Background (Disabled)**: `#767676` với opacity thấp
- **Font color**: `#FFFFFF` (white) - tất cả trạng thái
- **Border radius**: 20px (bo tròn đồng đều 4 góc)
- **Border thickness**: 0 (không viền)
- **Height**: 48px
- **Width**: 220px (default, có thể điều chỉnh)

**Icon decoration:**
- Right-side accent decoration với màu `#FF6A00` (accent orange)
- Icon màu white, tự động điều chỉnh khi disabled

**Behavior:**
- Hover: Background effect tương tự primary button
- Disabled: Background và text mờ đi, không thể click

---

## 1. Tạo Component
1. Name: `cmpSecondaryButton`
2. Size: 220 x 48

## 2. Custom Properties
- `Text` (Input, Text)
- `IsEnabled` (Input, Boolean)
- `OnSelectAction` (Event)

**Bind logic** tương tự Primary Button.
**Màu sắc**: Fill = `#767676`.

# Component: `cmpSectionTitle` (Power Apps)

## Overview / Tổng Quan

### Tính năng (Features)
- Component hiển thị tiêu đề phân đoạn (section header)
- Có cả title chính và subtitle (tùy chọn)
- Có separator line (đường kẻ ngang phân cách) có thể bật/tắt
- Sử dụng typography chuẩn theo design system
- Có thể tùy chỉnh text cho title và subtitle từ bên ngoài

### Mục đích (Purpose)
Được sử dụng để phân chia rõ ràng các phần trong form đánh giá:
- **Calidad** - Section đánh giá chất lượng
- **Oportunidad** - Section đánh giá tính kịp thời
- **Gestión** - Section đánh giá quản lý
- **Requisitos en Salud Ocupacional (SST)**
- **Requisitos Ambientales (MA)**
- **Código de Ética**
- **Observaciones**
- **Resumen**

Component này giúp cải thiện khả năng đọc (readability) và điều hướng (navigation) trong form đánh giá dài.

### UI Specification (theo app_design.md)
Theo **Section 3** (Typography) và **Section 8.4** (Section Separators) của `app_design.md`:

**Title Text:**
- **Font**: `AmsiPro, sans-serif` (hoặc font tương tự nếu không có)
- **Color**: `#003087` (primary blue)
- **Size**: Large, bold
- **Purpose**: Hiển thị tên section lớn như "Calidad", "Medioambiente", "Resumen"

**Subtitle Text:**
- **Font**: `Roboto` (hoặc tương tự)
- **Color**: `#003087`
- **Size**: Smaller than title, regular weight
- **Purpose**: Mô tả hoặc hướng dẫn ngắn cho section

**Separator Line:**
- **Type**: Solid horizontal rule
- **Color**: `#003087` (primary blue)
- **Purpose**: Phân cách giữa các section khác nhau
- **Visibility**: Có thể bật/tắt thông qua property `ShowSeparator`

---

## 1. Controls
- `lblTitle` (Title)
- `lblSubtitle` (Subtitle)
- `rectSeparator` (Line)

## 2. Custom Properties
- `TitleText` (Input, Text)
- `SubtitleText` (Input, Text)
- `ShowSeparator` (Input, Boolean)

# Component: `cmpCriteria` (Power Apps)

## Overview / Tổng Quan

### Tính năng (Features)
- Component để hiển thị một câu hỏi đánh giá dạng multiple-choice
- Hỗ trợ 2 hoặc 3 options (tùy thuộc vào câu hỏi)
- Hiển thị điểm số (points) tương ứng với option được chọn
- Visual states rõ ràng: Unanswered, Hover, Selected
- Tính toán tự động điểm số dựa trên option được chọn
- Có thể enable/disable dựa trên role và business logic
- Chiều cao tự động điều chỉnh theo nội dung

### Mục đích (Purpose)
Được sử dụng để hiển thị các câu hỏi đánh giá trong form, bao gồm:

**Calidad** (5 câu hỏi × 6 điểm = 30 điểm):
- Calidad de los bienes o servicios
- Cumplimiento de especificaciones técnicas
- etc.

**Oportunidad** (1 câu hỏi × 30 điểm = 30 điểm):
- Cumplimiento de plazos de entrega

**Gestión** (2 câu hỏi: 10 + 10 điểm = 20 điểm):
- Documentación completa
- Atención de solicitudes

**SST** (1 câu hỏi × 10 điểm = 10 điểm)
**MA** (1 câu hỏi × 10 điểm = 10 điểm)

Component này là thành phần cốt lõi nhất của evaluation form, cho phép user chọn mức độ đạt được của từng tiêu chí.

### UI Specification (theo app_design.md)
Theo **Section 7** (Questions Section) và **Section 8** (Question Visual States) của `app_design.md`:

**Structure:**
- **Question title**: Bold `Roboto`, color `#003087`
- **Option rows**: Clickable areas, 2-3 rows depending on question
- **Points display**: Right side, shows score for selected option

**Answer scoring logic (Section 7.3):**
- Câu hỏi 3 options:
  - Option 1: 100% điểm (Cumple / Achieved)
  - Option 2: 50% điểm (Cumple parcialmente / Partially achieved)
  - Option 3: 0% điểm (No cumple / Not achieved)
- Câu hỏi 2 options (như Gestión question 1):
  - Option 1: 100% điểm
  - Option 2: 0% điểm

**Visual States (Section 8):**

1. **Unanswered (Section 8.1):**
   - Background: Light blue/very pale
   - Text color: `#003087`
   - Points display: `"-"` (dash)
   - Border: Pale gray horizontal line separating questions

2. **Hover (Section 8.2):**
   - Background: `#009AFE` (bright accent blue)
   - Text color: `#FFFFFF` (white)
   - Applies to entire row

3. **Selected (Section 8.3):**
   - Background: `#003087` (primary blue)
   - Text color: `#FFFFFF` (white)
   - Points display: Shows numeric value (e.g., "6.0 puntos")
   - Font: Large `Roboto` numbers

**Behavior:**
- Click một option → option đó chuyển sang Selected state
- Option trước đó (nếu có) quay về Unanswered state
- Điểm số cập nhật real-time
- Khi disabled: User không thể click, visual state giữ nguyên selected value

---

## 1. Controls
- `lblTitle`
- `cntOpt1` / `lblOpt1`
- `cntOpt2` / `lblOpt2`
- `cntOpt3` / `lblOpt3`
- `cntScore` / `lblScore`

## 2. Input Properties
| Property | Type | Default | Mô tả |
|---|---|---|---|
| `TitleText` | Text | "" | Tên tiêu chí |
| `Option1Text` | Text | "" | |
| `Option2Text` | Text | "" | |
| `Option3Text` | Text | "" | |
| `OptionCount` | Number | 3 | |
| `TotalPoints` | Number | 0 | |
| `SelectedIndex` | Number | 0 | State từ Screen |
| `Enable` | Boolean | true | Cho phép chọn hay không |

## 3. Output Properties
- `Score` (Number): Điểm tính toán.
- `ContentHeight` (Number): Chiều cao tự động.

## 4. Events
- `OnSelectOption1`
- `OnSelectOption2`
- `OnSelectOption3`

## 5. Logic `Enable`
Component **không dùng DisplayMode** (vì Container không có). Thay vào đó, dùng logic `If` trong `OnSelect`.

**Tại `cntOpt1.OnSelect`:**
```powerfx
If(
    Parent.Enable && OptionCount >= 1,
    OnSelectOption1()
)
```
(Tương tự cho Option 2, 3).

# Component: `cmpObservationBox` (Power Apps)

## Overview / Tổng Quan

### Tính năng (Features)
- Textarea nhiều dòng (multiline) để nhập observations/comments
- Giới hạn ký tự tối đa (max 500 characters)
- Label mô tả rõ ràng cho từng observation box
- Có thể set default value
- Enable/disable dựa trên role của user
- Output value có thể đọc từ bên ngoài component
- Bind OnChange event để capture thay đổi

### Mục đích (Purpose)
Được sử dụng để thu thập observations (nhận xét) từ các stakeholders khác nhau trong quá trình đánh giá:

1. **Observaciones Responsable Técnico**: Nhận xét của người chịu trách nhiệm kỹ thuật
2. **Observaciones Responsable SST**: Nhận xét của người chịu trách nhiệm An toàn Sức khỏe Lao động
3. **Observaciones Responsable MA**: Nhận xét của người chịu trách nhiệm Môi trường
4. **Observaciones Revisor**: Nhận xét của người phê duyệt/review

Theo **Section 11** của `app_design.md`, các observations này:
- **Visible** cho tất cả users liên quan
- Chỉ **editable** bởi user có role tương ứng
- Users khác thấy ở chế độ read-only

Component này đảm bảo transparency và traceability trong quá trình đánh giá.

### UI Specification (theo app_design.md)
Theo **Section 11** (Observation Text Areas) của `app_design.md`:

**Visual:**
- **Shape**: Large rounded rectangle
- **Background**: `#FFFFFF` (white)
- **Border**: Thin blue border (màu `#003087`)
- **Border radius**: Rounded corners
- **Max characters**: **500 characters** (enforced)

**Label:**
- **Position**: Above each textarea
- **Font**: `Roboto`
- **Color**: `#003087`
- **Text examples**:
  - "Observaciones Responsable Técnico"
  - "Observaciones Responsable SST"
  - "Observaciones Responsable MA"
  - "Observaciones Revisor"

**Behavior:**
- **Editable mode** (khi `Enable = true`):
  - User có thể type/edit text
  - Border có thể highlight khi focus
  - Character counter có thể hiển thị
- **Read-only mode** (khi `Enable = false`):
  - DisplayMode = View
  - Text hiển thị nhưng không thể edit
  - Visual cue để phân biệt với editable mode

**Layout:**
- Các observation boxes được phân cách bởi gray horizontal line
- Thường được nhóm theo role hoặc section

---

## 1. Controls
- `lblLabel`
- `txtInput` (Mode = MultiLine)

## 2. Properties
- `LabelText` (Input, Text)
- `Default` (Input, Text) -> Bind: `txtInput.Default = Parent.Default`
- `MaxLength` (Input, Number) -> Bind: `txtInput.MaxLength = Parent.MaxLength`
- `Enable` (Input, Boolean)

**Bind Enable:**
```powerfx
txtInput.DisplayMode = If(Parent.Enable, DisplayMode.Edit, DisplayMode.View)
```

- `OnChangeAction` (Event) -> Bind: `If(Parent.Enable, OnChangeAction())`
- `Value` (Output, Text) -> `txtInput.Text`

# Component: `cmpYesNoQuestion` (Power Apps)

## Overview / Tổng Quan

### Tính năng (Features)
- Component để hiển thị câu hỏi dạng Yes/No (hoặc Sí/No)
- Sử dụng Radio button control với 2 options
- Có thể set default value (Yes hoặc No)
- Enable/disable dựa trên business logic và role
- Output value là Boolean (true/false)
- Bind OnChange event để xử lý logic khi giá trị thay đổi

### Mục đích (Purpose)
Được sử dụng cho 2 trường hợp chính trong evaluation form:

**1. Código de Ética Question (Section 10):**
- Statement: "El contratista incurrió en faltas al código de ética"
- Answer: Sí / No
- **Role**: Chỉ **Responsable Técnico** được phép trả lời
- **Impact**: 
  - **Sí** → Total score = 0 (nullify entire evaluation)
  - **No** → Total score = Subtotal (normal scoring)
- **Disabled** cho tất cả users khác

**2. Medioambiente Gating Question (Section 9):**
- Prompt: "Responsable de criterio de medioambiente debe responder:"
- Answer: Sí / No
- **Role**: Chỉ **Responsable Técnico** trả lời
- **Logic**:
  - **No** → Main MA question được trả lời bởi Técnico, hidden từ MA user
  - **Sí** → Main MA question được trả lời bởi `email_responsable_ma`, hidden/disabled cho Técnico
- Đây là gating logic để route question đến đúng người

Component này đóng vai trò quan trọng trong business logic routing và scoring của evaluation.

### UI Specification (theo app_design.md)
Theo **Section 9** (Medioambiente Yes/No) và **Section 10** (Código de Ética) của `app_design.md`:

**Layout:**
- **Question text**: Label phía trên, font `Roboto`, bold, color `#003087`
- **Radio options**: Horizontal layout với 2 buttons
  - "Sí" (Yes)
  - "No"

**Visual States:**
- **Unselected option**:
  - Light blue/pale background
  - Blue text `#003087`
  - Similar to criteria options style

- **Selected option**:
  - Background: `#003087` (primary blue)
  - Text color: `#FFFFFF` (white)

- **Hover**:
  - Background: `#009AFE` (accent blue)
  - Text color: `#FFFFFF` (white)

**Behavior:**
- Style tương tự như các options trong `cmpCriteria`
- Toggle giữa Sí/No
- Khi disabled: Giữ nguyên selected value, không thể thay đổi
- Visual cue rõ ràng cho disabled state

**Special behaviors:**
- **Código de Ética**: Khi chọn "Sí", Total score section phải show 0
- **MA Gating**: OnChange event trigger logic show/hide main MA question

---

## 1. Controls
- `lblQuestion` (Label)
- `rdoYesNo` (Radio) - Items: `["Sí", "No"]`

## 2. Properties

### 2.1 `QuestionText` (Input, Text)
Bind: `lblQuestion.Text = Parent.QuestionText`

### 2.2 `DefaultValue` (Input, Boolean)
Logic bind:
```powerfx
rdoYesNo.Default = If(Parent.DefaultValue, "Sí", "No") // Cần check kỹ default selected item
```

### 2.3 `Enable` (Input, Boolean)
Cho phép sửa hay không.

**Bind:**
```powerfx
rdoYesNo.DisplayMode = If(Parent.Enable, DisplayMode.Edit, DisplayMode.View)
```

### 2.4 `OutputValue` (Output, Boolean)
```powerfx
rdoYesNo.Selected.Value = "Sí"
```

### 2.5 `OnChangeAction` (Event)
Bind: `rdoYesNo.OnChange = OnChangeAction()`
