````md
# Giải pháp `cmpHeaderButton` + copy Header Bar  
(*dùng chung header cho mọi screen, dễ sửa & dễ bảo trì*)

Dưới đây là hướng dẫn **từ A–Z**:

- Tạo **component nút**: `cmpHeaderButton`
- Dùng `cmpHeaderButton` để xây **header bar** (`conHeaderBar`) trên 1 screen
- Dùng **copy–paste** header bar sang các screen khác
- Sửa header 1 chỗ → xóa header cũ ở các screen khác → paste header mới là xong

---

## 0. Chuẩn bị: biến role & biến screen

### 0.1. Biến role `gblUserRole` (tuỳ bạn, nếu đã có thì bỏ qua)

Giả sử bạn có SharePoint list `UserRoles` với cột:

- `Email`
- `CanEvaluate`
- `CanApprove`
- `CanSign`
- `IsAdmin`

**App.OnStart**:

```powerfx
// Lưu user hiện tại
Set(
    gblCurrentUser,
    {
        FullName: User().FullName,
        Email: Lower(User().Email)
    }
);

// Lấy role của user hiện tại
Set(
    gblUserRole,
    LookUp(
        UserRoles,
        Lower(Email) = gblCurrentUser.Email
    )
);

// Khởi tạo màn hình hiện tại (sẽ update tiếp trong OnVisible)
Set(
    gblCurrentScreen,
    App.ActiveScreen
);
````

---

### 0.2. Biến `gblCurrentScreen` để highlight tab header

Trong **mỗi screen**, ở property `OnVisible`, set:

```powerfx
// Ví dụ trong scrLanding
Set(gblCurrentScreen, scrLanding);

// Trong scrEvaluateProviders
Set(gblCurrentScreen, scrEvaluateProviders);

// Trong scrApproveEvaluations
Set(gblCurrentScreen, scrApproveEvaluations);

// Trong scrSignLetters
Set(gblCurrentScreen, scrSignLetters);

// Trong scrDashboard
Set(gblCurrentScreen, scrDashboard);
```

---

## 1. Tạo component `cmpHeaderButton`

### 1.1. Tạo component mới

1. Mở Power Apps Studio.
2. Trong panel trái → **Components** → **+ New component**.
3. Đổi tên component:

```text
cmpHeaderButton
```

4. Set size (tùy ý, có thể đổi sau):

```powerfx
Width  = 200
Height = 50
```

---

### 1.2. Thêm UI: nền + text

#### 1.2.1. Rectangle nền

Insert → **Rectangle**
Đổi tên:

```text
shpBackground
```

Thuộc tính:

```powerfx
X = 0
Y = 0
Width  = Parent.Width
Height = Parent.Height

Fill  = RGBA(0, 87, 119, 1)      // xanh default
BorderThickness = 0
RadiusTopLeft     = 8
RadiusTopRight    = 8
RadiusBottomLeft  = 8
RadiusBottomRight = 8
```

#### 1.2.2. Label text

Insert → **Label**
Đổi tên:

```text
lblText
```

Thuộc tính:

```powerfx
X = 0
Y = 0
Width  = Parent.Width
Height = Parent.Height

Text          = "Header Button"
Align         = Align.Center
VerticalAlign = VerticalAlign.Middle
Color         = Color.White
Font          = Font.'Segoe UI'
Size          = 16
PaddingLeft   = 0
PaddingRight  = 0
```

---

### 1.3. Tạo Custom Properties cho `cmpHeaderButton`

#### 1.3.1. Property `HeaderText` (Input/Text)

1. Chọn `cmpHeaderButton`.
2. Panel phải → phần **Custom properties** → **+ New custom property**.
3. Điền:

```text
Display name : HeaderText
Name         : HeaderText
Property type: Input
Data type    : Text
```

4. Bấm **Create**.

**Gán cho label**:

```powerfx
// lblText.Text
Self.HeaderText
```

---

#### 1.3.2. Property `OnSelect` (Event)

1. Vẫn trong Custom properties → **+ New custom property**.
2. Điền:

```text
Display name : OnSelect
Name         : OnSelect
Property type: Event
```

3. Bấm **Create**.

**Gán cho nền & label**:

```powerfx
// shpBackground.OnSelect
If(
    !IsBlank(Self.OnSelect),
    Self.OnSelect()
)

// lblText.OnSelect
If(
    !IsBlank(Self.OnSelect),
    Self.OnSelect()
)
```

---

#### 1.3.3. Property `IsSelected` (Input/Boolean)

1. New custom property:

```text
Display name : IsSelected
Name         : IsSelected
Property type: Input
Data type    : Boolean
```

2. Bấm **Create**.

**Đổi màu khi được chọn:**

```powerfx
// shpBackground.Fill
If(
    Self.IsSelected,
    RGBA(0, 120, 180, 1),    // màu active
    RGBA(0, 87, 119, 1)      // màu normal
)
```

---

#### 1.3.4. Property `Enable` (Input/Boolean) – để disable theo role

1. New custom property:

```text
Display name : Enable
Name         : Enable
Property type: Input
Data type    : Boolean
```

2. Bấm **Create**.

**Control DisplayMode & màu khi disabled:**

```powerfx
// cmpHeaderButton.DisplayMode
If(
    Self.Enable,
    DisplayMode.Edit,
    DisplayMode.Disabled
)
```

(optional) Nếu muốn màu xám khi disabled:

```powerfx
// shpBackground.Fill (nếu muốn xét cả Enable)
If(
    !Self.Enable,
    RGBA(160, 160, 160, 1),          // disabled
    If(
        Self.IsSelected,
        RGBA(0, 120, 180, 1),        // active
        RGBA(0, 87, 119, 1)          // normal
    )
)
```

---

## 2. Tạo Header Bar (`conHeaderBar`) trên 1 screen mẫu

Bạn chọn **1 screen làm “chuẩn”** để thiết kế header, ví dụ: `scrLanding`
Hoặc tạo riêng `scrHeaderTemplate` chỉ để copy header.

### 2.1. Thêm logo

Insert → **Image** trên `scrLanding` (không trong component)
Đặt tên:

```text
imgLogoHeader
```

Thuộc tính (gợi ý):

```powerfx
X = 10
Y = 10
Height = 60
Width  = 140
```

Set `Image =` logo công ty.

---

### 2.2. Thêm container header bar

Insert → **Horizontal container**
Đặt tên:

```text
conHeaderBar
```

Thuộc tính:

```powerfx
X = imgLogoHeader.X + imgLogoHeader.Width + 20
Y = 10
Height = 60
Width  = App.Width - imgLogoHeader.Width - 40

AlignItems   = Align.Center
Justify      = Justify.SpaceBetween
PaddingLeft  = 0
PaddingRight = 0
Fill         = RGBA(0,0,0,0)   // Transparent
```

---

### 2.3. Thêm 4 instance `cmpHeaderButton` vào `conHeaderBar`

Khi đang chọn `conHeaderBar`:

1. Insert → **Custom** → chọn `cmpHeaderButton` 4 lần.
2. Đổi tên lần lượt:

```text
cmpHB_Evaluate
cmpHB_Approve
cmpHB_Sign
cmpHB_Dashboard
```

Do nằm trong container Horizontal, chúng sẽ tự xếp hàng ngang.

---

## 3. Cấu hình từng `cmpHeaderButton` trong header bar

Dưới đây là **ví dụ 5 screen**:

* `scrLanding`
* `scrEvaluateProviders`
* `scrApproveEvaluations`
* `scrSignLetters`
* `scrDashboard`

Giả sử đã có `gblCurrentScreen` và `gblUserRole` như phần 0.

---

### 3.1. Nút “Evaluar Proveedores” – `cmpHB_Evaluate`

```powerfx
// cmpHB_Evaluate.HeaderText
"Evaluar Proveedores"

// cmpHB_Evaluate.OnSelect
Navigate(scrEvaluateProviders, ScreenTransition.Fade)

// cmpHB_Evaluate.IsSelected
gblCurrentScreen = scrEvaluateProviders

// cmpHB_Evaluate.Enable
If(
    IsBlank(gblUserRole),
    true,                    // nếu chưa có role, cho phép
    gblUserRole.CanEvaluate  // nếu có role, check quyền
)
```

---

### 3.2. Nút “Aprobar evaluaciones” – `cmpHB_Approve`

```powerfx
// HeaderText
"Aprobar evaluaciones"

// OnSelect
Navigate(scrApproveEvaluations, ScreenTransition.Fade)

// IsSelected
gblCurrentScreen = scrApproveEvaluations

// Enable
If(
    IsBlank(gblUserRole),
    false,
    gblUserRole.CanApprove
)
```

---

### 3.3. Nút “Firmar cartas” – `cmpHB_Sign`

```powerfx
// HeaderText
"Firmar cartas"

// OnSelect
Navigate(scrSignLetters, ScreenTransition.Fade)

// IsSelected
gblCurrentScreen = scrSignLetters

// Enable
If(
    IsBlank(gblUserRole),
    false,
    gblUserRole.CanSign
)
```

---

### 3.4. Nút “Dashboard (Solo admin)” – `cmpHB_Dashboard`

```powerfx
// HeaderText
"Dashboard (Solo admin)"

// OnSelect
Navigate(scrDashboard, ScreenTransition.Fade)

// IsSelected
gblCurrentScreen = scrDashboard

// Enable
If(
    IsBlank(gblUserRole),
    false,
    gblUserRole.IsAdmin
)

// (optional) Ẩn hẳn nếu không phải admin:
Visible =
If(
    IsBlank(gblUserRole),
    false,
    gblUserRole.IsAdmin
)
```

---

## 4. Dùng header bar này cho các screen khác

Đây là **trọng tâm** giải pháp bạn chọn:

> Sửa 1 `conHeaderBar` mẫu → **copy & paste** sang screen khác.

### 4.1. Lần đầu: copy header sang các screen

1. Trên screen mẫu (vd `scrLanding`), chọn:

   * `imgLogoHeader`
   * `conHeaderBar` (chứa 4 `cmpHeaderButton`)
2. Nhấn **Ctrl + G** nếu muốn Group cho dễ copy (không bắt buộc).
3. **Ctrl + C** để copy.
4. Mở screen khác, vd `scrEvaluateProviders` → **Ctrl + V** để paste.
5. Đặt lại vị trí:

```powerfx
X = 0       // cho group/controls
Y = 0
```

6. Nội dung (form, gallery…) trên mỗi screen set:

```powerfx
Y      = imgLogoHeader.Height + 10   // ví dụ 80
Height = Parent.Height - (imgLogoHeader.Height + 10)
```

> Tùy bạn đặt cố định: `Y = 80` thì dễ hơn.

### 4.2. Khi cần sửa thiết kế header

Giả sử bạn muốn:

* Thêm 1 nút mới “Configuración”
* Đổi khoảng cách, chiều cao header
* Đổi background header

**Quy trình:**

1. Chỉ sửa header bar trên **1 screen chuẩn** (vd `scrLanding`).
2. Sau khi OK:

   * Vào từng screen khác
   * Xóa `conHeaderBar` + `imgLogoHeader` cũ
   * Copy từ screen chuẩn → paste vào
3. Do mỗi nút là **`cmpHeaderButton`**, toàn bộ style bên trong vẫn đồng bộ.

---

## 5. Tóm tắt flow sử dụng

1. **Tạo `cmpHeaderButton`**

   * Có `HeaderText`, `OnSelect`, `IsSelected`, `Enable`
   * Định nghĩa UI & behavior chuẩn cho mọi header button.

2. **Trên 1 screen mẫu**

   * Tạo `imgLogoHeader` + `conHeaderBar`
   * Thêm 4 `cmpHeaderButton`
   * Cấu hình `HeaderText`, `OnSelect`, `IsSelected`, `Enable`.

3. **Copy header bar sang các screen khác**

   * Lần đầu: paste để có header ở mọi screen.
   * Về sau: khi thay đổi layout header, sửa 1 nơi → xóa/replace ở screen còn lại.

4. **Phần tái sử dụng**

   * Thay đổi **logic & style của nút**: sửa trong `cmpHeaderButton` → **mọi header trên mọi screen tự cập nhật**.
   * Thay đổi **layout header** (spacing, thêm nút, vị trí logo): sửa ở 1 header bar → rồi **copy–replace**.

---

Nếu bạn muốn bước tiếp theo, có thể làm:

* `cmpPrimaryButton` dùng trong form Evaluate/Approve
* `cmpInputText` + `cmpDropdown` để form nhìn chuẩn & consistent.

```
::contentReference[oaicite:0]{index=0}
```
