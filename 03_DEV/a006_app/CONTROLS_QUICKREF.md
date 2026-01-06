# Power Apps Controls Quick Reference (A006)

Tài liệu này hướng dẫn cách sử dụng Controls trong dự án A006, đảm bảo tính nhất quán về giao diện (UI) và trải nghiệm (UX) theo Design System mới.

---

## 1. Nguyên tắc Chung (General Principles)

### 1.1. Classic vs Modern Controls
*   **Layout**: Sử dụng **Modern Containers** (Horizontal/Vertical) để responsive layout.
*   **Input/Action**: Sử dụng **Classic Controls** (Text Input, Button, Dropdown) để có khả năng tùy biến cao nhất về style (Border, Fill, Hover states).
    *   *Lý do*: Modern controls hiện tại chưa hỗ trợ đầy đủ các thuộc tính style cần thiết cho design này (ví dụ: border color theo state).

### 1.2. Naming Convention
| Control Type | Prefix | Example |
| :--- | :--- | :--- |
| Container | `con` | `conHeader`, `conMainForm` |
| Label | `lbl` | `lblProviderName` |
| Text Input | `txt` | `txtCalidad` |
| Button | `btn` | `btnSubmit` |
| Dropdown | `ddl` | `ddlEvaluation` |
| Gallery | `gal` | `galPendingTasks` |
| Icon | `ico` | `icoBack` |
| Rectangle/Shape | `shp` | `shpDivider` |

---

## 2. Design System Specs

### 2.1. Color Palette
| Name | Hex | Usage |
| :--- | :--- | :--- |
| **Primary Blue** | `#003087` | Header BG, Primary Buttons, Titles, Selected State |
| **Accent Blue** | `#009AFE` | Hover State (Primary Buttons, Question Options) |
| **Accent Orange** | `#FF6A00` | Button Decorations, Focus States |
| **Neutral Gray** | `#767676` | Secondary/Negative Buttons |
| **White** | `#FFFFFF` | Text on Blue, Card Backgrounds |
| **Light Blue/Pale** | `#F0F8FF` hoặc tương tự | Unanswered Question Background |

### 2.2. Typography
| Type | Font Family | Weight | Color | Usage |
| :--- | :--- | :--- | :--- | :--- |
| **Headings** | `AmsiPro` (or `Segoe UI`) | **Bold** | `#003087` | Screen Titles, Section Headers |
| **Body** | `Roboto` (or `Segoe UI`) | Regular | `#003087` | Labels, Questions, Inputs |
| **Button Text** | `Roboto` (or `Segoe UI`) | **Semibold** | `#FFFFFF` | Button Labels |

---

## 3. Control Cheat Sheet

### 3.1. Primary Button (Submit/Approve)
*   **Control**: Classic Button
*   **Size**: Width = 220, Height = 48 (default)
*   **Properties**:
    ```powerfx
    Color: RGBA(255, 255, 255, 1)      // White Text
    Fill: ColorValue("#003087")        // Primary Blue
    HoverFill: ColorValue("#009AFE")   // Accent Blue
    PressedFill: ColorFade(ColorValue("#003087"), -20%)
    DisabledFill: RGBA(0, 48, 135, 0.3) // Primary Blue with 30% opacity
    DisabledColor: ColorFade(RGBA(255, 255, 255, 1), 40%) // Faded white
    BorderThickness: 0                 // No border
    RadiusTopLeft: 20                  // Rounded corners
    RadiusTopRight: 20
    RadiusBottomLeft: 20
    RadiusBottomRight: 20
    FontWeight: FontWeight.Semibold
    ```
*   **Decoration**: Icon ChevronRight ở bên phải, màu white. Có thể thêm accent decoration màu Orange (`#FF6A00`).
*   **Reference**: `app_design.md` Section 4.1

### 3.2. Secondary Button (Cancel/Reset)
*   **Control**: Classic Button
*   **Size**: Width = 220, Height = 48 (default)
*   **Properties**:
    ```powerfx
    Color: RGBA(255, 255, 255, 1)      // White Text
    Fill: ColorValue("#767676")        // Neutral Gray
    HoverFill: ColorFade(ColorValue("#767676"), 20%)  // Lighter gray on hover
    PressedFill: ColorFade(ColorValue("#767676"), -20%) // Darker gray on press
    DisabledFill: RGBA(118, 118, 118, 0.3) // Gray with low opacity
    DisabledColor: ColorFade(RGBA(255, 255, 255, 1), 40%) // Faded white
    BorderThickness: 0
    RadiusTopLeft: 20
    RadiusTopRight: 20
    RadiusBottomLeft: 20
    RadiusBottomRight: 20
    ```
*   **Decoration**: Right-side accent decoration với màu `#FF6A00` (accent orange).
*   **Reference**: `app_design.md` Section 4.2
*   **Note**: Secondary button KHÔNG dùng Accent Blue (#009AFE) cho hover - chỉ dùng gray variations

### 3.3. Text Input (Classic)
*   **Properties**:
    ```powerfx
    Color: ColorValue("#003087")       // Blue Text
    Fill: RGBA(255, 255, 255, 1)       // White BG
    BorderColor: ColorValue("#003087") // Blue Border
    HoverBorderColor: ColorValue("#009AFE")
    FocusedBorderColor: ColorValue("#FF6A00") // Orange Focus
    BorderThickness: 1
    Radius: 4
    PaddingLeft: 12
    ```

### 3.4. Question Row (Evaluation Form)
Sử dụng **Container** chứa Label (Câu trả lời) để tạo hiệu ứng hover cho cả dòng.

**Reference**: `app_design.md` Section 8 (Question Visual States)

*   **Container Properties**:
    ```powerfx
    // Background Color Logic - 3 states: Unanswered, Hover, Selected
    Fill: If(
        ThisItem.IsSelected, 
        ColorValue("#003087"),         // Selected (Section 8.3): Primary Blue
        If(
            Self.Hover, 
            ColorValue("#009AFE"),     // Hover (Section 8.2): Accent Blue
            ColorValue("#F0F8FF")      // Unanswered (Section 8.1): Light blue/very pale
        )
    )
    
    // For disabled state (read-only):
    DisplayMode: If(varEnableEditing, DisplayMode.Edit, DisplayMode.View)
    ```
    
*   **Label (Text) Properties**:
    ```powerfx
    Color: If(
        ThisItem.IsSelected || Parent.Hover,
        RGBA(255, 255, 255, 1),        // White text on Hover/Selected
        ColorValue("#003087")          // Blue text on Unanswered
    )
    ```
    
*   **Points Display** (right side):
    ```powerfx
    Text: If(
        IsBlank(ThisItem.SelectedOption),
        "-",                            // Dash when unanswered
        ThisItem.Points & " puntos"    // Show points when answered
    )
    Font: Roboto, Large size
    Color: ColorValue("#003087")       // Blue (hoặc white tùy state)
    ```
    
*   **Separator**: Pale gray horizontal line giữa các questions (trong cùng section)

### 3.5. Header Container
*   **Control**: Horizontal Container
*   **Properties**:
    ```powerfx
    Fill: ColorValue("#003087")
    Height: 80
    AlignInContainer: AlignInContainer.Center
    Justify: Justify.SpaceBetween
    PaddingLeft: 40
    PaddingRight: 40
    ```

---

## 4. Common Patterns & Snippets

### 4.1. Responsive Width
Luôn sử dụng `Parent.Width` hoặc `FillPortions` trong Container.
```powerfx
Width: Parent.Width - 40  // Full width minus padding
```

### 4.2. Validation Visuals
Đổi màu viền sang đỏ nếu sai validation.
```powerfx
BorderColor: If(
    IsBlank(Self.Text) && varShowErrors,
    Color.Red,
    ColorValue("#003087")
)
```

### 4.3. Scrollable Screen Content
Để màn hình cuộn được (khi nội dung dài):
1.  Tạo **Main Container** (`conMainForm`).
2.  Set `Height = Parent.Height` (Quan trọng!).
3.  Set `LayoutOverflowY = LayoutOverflow.Scroll`.
4.  Thêm **Bottom Spacer** (Label rỗng, Height 100) ở cuối cùng để tránh bị cắt nội dung.

---

## 5. Icons & Assets
*   Sử dụng SVG icons nếu có thể để sắc nét.
*   Logo ISA: Upload vào Media, đặt tên `imgLogo_White`.
