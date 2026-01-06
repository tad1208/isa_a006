# A006 Power App – Components (Exact from unpacked sources)

Source folder: `Src/Components/` (from `a006_app.zip`)

This document lists each component and the controls/properties that are **explicitly set** in the `.fx.yaml` files. Values are shown exactly as they appear in source (including Power Fx formulas).

## cmpHeaderButton (CanvasComponent)

**Explicit properties (as defined in source):**

- `Destination`: `=App.ActiveScreen`
- `Fill`: `=Color.Transparent`
- `Height`: `=80`
- `IsActive`: `=true`
- `Label`: `="Text"`
- `Width`: `=180`
- `X`: `=0`
- `Y`: `=0`
- `ZIndex`: `=1`

**Child controls:**

### btnNavButton (button)

Properties:

- `Color`: `=If(Parent.IsActive, RGBA(0, 51, 102, 1), Color.White)`
- `Fill`: `=If(cmpHeaderButton.IsActive, ColorValue("#009AFE"),  Color.Transparent)`
- `Font`: `=Font.'Segoe UI'`
- `FontWeight`: `=If(Parent.IsActive, FontWeight.Bold, FontWeight.Semibold)`
- `Height`: `=Parent.Height`
- `HoverColor`: `=RGBA(0, 134, 208, 1)`
- `HoverFill`: `=RGBA(245, 245, 245, 1)`
- `OnSelect`: `=Navigate(Parent.Destination)`
- `RadiusBottomLeft`: `=15`
- `RadiusBottomRight`: `=15`
- `RadiusTopLeft`: `=15`
- `RadiusTopRight`: `=15`
- `Text`: `=Parent.Label`
- `Width`: `=Parent.Width`
- `ZIndex`: `=1`

### iconNavButton (icon.ChevronRight)

Properties:

- `Color`: `=If(Parent.IsActive, Color.Transparent, Color.White)`
- `Height`: `=20`
- `Icon`: `=Icon.ChevronRight`
- `Width`: `=20`
- `X`: `=160`
- `Y`: `=30`
- `ZIndex`: `=3`


---

## cmpObservationBox (CanvasComponent)

**Explicit properties (as defined in source):**

- `Default`: `=""`
- `Enable`: `=true`
- `Fill`: `=RGBA(0, 0, 0, 0)`
- `Height`: `=200`
- `LabelText`: `="observation"`
- `OnReset`: `=Reset(txtInput)`
- `Value`: `=txtInput.Text`
- `Width`: `=600`
- `X`: `=0`
- `Y`: `=0`
- `ZIndex`: `=1`

**Child controls:**

### txtInput (text)

Properties:

- `BorderColor`: `=ColorValue("#003087")`
- `Color`: `=ColorValue("#003087")`
- `Default`: `=Parent.Default`
- `DisabledBorderColor`: `=ColorValue("#003087")`
- `DisabledColor`: `=ColorValue("#003087")`
- `DisplayMode`: `=If(cmpObservationBox.Enable, DisplayMode.Edit, DisplayMode.Disabled)`
- `Fill`: `=RGBA(0, 0, 0, 0)`
- `FocusedBorderThickness`: `=2`
- `Font`: `=Font.'Segoe UI'`
- `Height`: `=Parent.Height - txtInput.Y`
- `HoverFill`: `=ColorFade(RGBA(186, 202, 226, 1),85%)`
- `MaxLength`: `=500`
- `Mode`: `=TextMode.MultiLine`
- `PaddingBottom`: `=8`
- `PaddingRight`: `=8`
- `PaddingTop`: `=8`
- `RadiusBottomLeft`: `=8`
- `RadiusBottomRight`: `=8`
- `RadiusTopLeft`: `=8`
- `RadiusTopRight`: `=8`
- `Width`: `=Parent.Width`
- `Y`: `=lblObservationLabel.Y + lblObservationLabel.Height + 4`
- `ZIndex`: `=1`

### lblObservationLabel (label)

Properties:

- `Color`: `=ColorValue("#003087")`
- `Font`: `=Font.'Segoe UI'`
- `FontWeight`: `=FontWeight.Bold`
- `Size`: `=18`
- `Text`: `=Parent.LabelText`
- `Width`: `=Parent.Width`
- `ZIndex`: `=2`


---

## cmpPrimaryButton (CanvasComponent)

**Explicit properties (as defined in source):**

- `ThisProperty`: ``
- `Default`: `=`
- `Fill`: `=Color.Transparent`
- `Height`: `=48`
- `IsEnabled`: `=true`
- `Text`: `="Primary Button"`
- `Width`: `=220`
- `X`: `=0`
- `Y`: `=0`
- `ZIndex`: `=1`

**Child controls:**

### btnMainPrimary (button)

Properties:

- `BorderThickness`: `=0`
- `Color`: `=Color.White`
- `DisabledColor`: `=ColorFade(Color.White, 40%)`
- `DisabledFill`: `=RGBA(0, 48, 135, 0.3)`
- `DisplayMode`:

```powerfx
=If(
Parent.IsEnabled,
DisplayMode.Edit,
DisplayMode.Disabled
)
```
- `Fill`: `=ColorValue("#003087")`
- `FocusedBorderThickness`: `=2`
- `Height`: `=Parent.Height`
- `HoverColor`: `=Color.White`
- `HoverFill`: `=ColorValue("#009AFE")`
- `OnSelect`:

```powerfx
=If(
Parent.IsEnabled,
Parent.OnSelectAction()
)
```
- `PressedColor`: `=Color.White`
- `PressedFill`: `=ColorFade(ColorValue("#003087"), -20%)`
- `RadiusBottomLeft`: `=20`
- `RadiusBottomRight`: `=20`
- `RadiusTopLeft`: `=20`
- `RadiusTopRight`: `=20`
- `Text`: `=Parent.Text`
- `Width`: `=Parent.Width`
- `ZIndex`: `=1`

### icoArrowPrimary (icon.ChevronRight)

Properties:

- `Color`: `=Color.White`
- `Fill`: `=Color.Transparent`
- `Height`: `=20`
- `Icon`: `=Icon.ChevronRight`
- `Width`: `=20`
- `X`: `=btnMainPrimary.X + btnMainPrimary.Width - icoArrowPrimary.Width - 10`
- `Y`: `=btnMainPrimary.Y + (btnMainPrimary.Height - icoArrowPrimary.Height) / 2`
- `ZIndex`: `=2`


---

## cmpSecondaryButton (CanvasComponent)

**Explicit properties (as defined in source):**

- `ThisProperty`: ``
- `Default`: `=`
- `Fill`: `=Color.Transparent`
- `Height`: `=48`
- `IsEnabled`: `=true`
- `Text`: `="Secondary Button"`
- `Width`: `=220`
- `X`: `=0`
- `Y`: `=0`
- `ZIndex`: `=1`

**Child controls:**

### btnMainSecondary (button)

Properties:

- `BorderThickness`: `=0`
- `Color`: `=Color.White`
- `DisabledColor`: `=ColorFade(Color.White, 40%)`
- `DisabledFill`: `=RGBA(118,118,118,0.3)`
- `DisplayMode`:

```powerfx
=If(
Parent.IsEnabled,
DisplayMode.Edit,
DisplayMode.Disabled
)
```
- `Fill`: `=ColorValue("#767676")`
- `FocusedBorderThickness`: `=2`
- `Height`: `=Parent.Height`
- `HoverColor`: `=Color.White`
- `HoverFill`: `=ColorFade(Self.Fill, -15%)`
- `OnSelect`:

```powerfx
=If(
Parent.IsEnabled,
Parent.OnSelectAction()
)
```
- `PressedColor`: `=Color.White`
- `PressedFill`: `=ColorFade(Self.Fill, -25%)`
- `RadiusBottomLeft`: `=20`
- `RadiusBottomRight`: `=20`
- `RadiusTopLeft`: `=20`
- `RadiusTopRight`: `=20`
- `Text`: `=Parent.Text`
- `Width`: `=Parent.Width`
- `ZIndex`: `=1`

### icoArrowSecondary (icon.ChevronRight)

Properties:

- `Color`: `=Color.White`
- `Fill`: `=Color.Transparent`
- `Height`: `=20`
- `Icon`: `=Icon.ChevronRight`
- `Width`: `=20`
- `X`: `=btnMainSecondary.X + btnMainSecondary.Width - icoArrowSecondary.Width - 10`
- `Y`: `=btnMainSecondary.Y + (btnMainSecondary.Height - icoArrowSecondary.Height) / 2`
- `ZIndex`: `=2`


---

## cmpSectionTitle (CanvasComponent)

**Explicit properties (as defined in source):**

- `Fill`: `=RGBA(0, 0, 0, 0)`
- `Height`: `=30`
- `ShowSeparator`: `=true`
- `Text`: `="Section Title"`
- `Width`: `=500`
- `X`: `=0`
- `Y`: `=0`
- `ZIndex`: `=1`

**Child controls:**

### rectSeparator (rectangle)

Properties:

- `Fill`: `=ColorValue("#003087")`
- `Height`: `=2`
- `Visible`: `=Parent.ShowSeparator`
- `Width`: `=Parent.Width`
- `Y`: `=lblTitle.Y + lblTitle.Height + 4`
- `ZIndex`: `=1`

### lblTitle (label)

Properties:

- `Color`: `=ColorValue("#003087")`
- `Font`: `=Font.'Segoe UI'`
- `FontWeight`: `=FontWeight.Bold`
- `Height`: `=28`
- `Size`: `=20`
- `Text`: `=Parent.Text`
- `Width`: `=Parent.Width`
- `ZIndex`: `=2`


---

## cmpSubCriteria (CanvasComponent)

**Explicit properties (as defined in source):**

- `ThisProperty`: ``
- `Default`: `=`
- `ContentHeight`: `=If(
Self.OptionCount = 2,
cntOpt2.Y + cntOpt2.Height + 8,
cntOpt3.Y + cntOpt3.Height + 8
)`
- `Enable`: `=true`
- `Fill`: `=RGBA(0, 0, 0, 0)`
- `Height`: `=300`
- `OnReset`: `=`
- `Option1Text`: `="El contratista durante la ejecución del contrato cumplió con"`
- `Option2Text`: `="El contratista durante la ejecución del contrato presento algunos incumplimientos con los temas administrativos y de terreno de Seguridad y Salud en el Trabajo"`
- `Option3Text`: `="El contratista durante la ejecución del contrato presento incumplimientos reiterativos con temas administrativos y de terreno de Seguridad y Salud en el Trabajo"`
- `OptionCount`: `=3`
- `Score`: `=If(
Self.OptionCount = 3,
Switch(
Self.SelectedIndex,
1, Self.TotalPoints * 1,
2, Self.TotalPoints * 0.5,
3, 0,
0
),
If(
Self.OptionCount = 2,
Switch(
Self.SelectedIndex,
1, Self.TotalPoints * 1,
2, 0,
0
),
0
)
)`
- `SelectedIndex`: `=0`
- `TitleText`: `="sub criteria"`
- `TotalPoints`: `=100`
- `Width`: `=800`
- `X`: `=0`
- `Y`: `=0`
- `ZIndex`: `=1`

**Child controls:**

### cntBody (groupContainer.manualLayoutContainer)

Properties:

- `DropShadow`: `=DropShadow.None`
- `Height`:

```powerfx
=If(
Parent.OptionCount = 2,
cntOpt2.Y + cntOpt2.Height + 8,
cntOpt3.Y + cntOpt3.Height + 8
)
```
- `Width`: `=Parent.Width`
- `ZIndex`: `=6`

Children:

#### cntScore (groupContainer.manualLayoutContainer)

Properties:

- `BorderColor`: `=ColorValue("#003087")`
- `BorderThickness`: `=1`
- `Height`:

```powerfx
=If(
cmpSubCriteria.OptionCount = 2,
(cntOpt2.Y + cntOpt2.Height) - cntScore.Y,
// nếu 3 option
(cntOpt3.Y + cntOpt3.Height) - cntScore.Y
)
```
- `Width`: `=100`
- `X`: `=Parent.Width - cntScore.Width-1`
- `Y`: `=lblCriteriaTitle.Y + lblCriteriaTitle.Height`
- `ZIndex`: `=1`

Children:

##### lblScore (label)

Properties:

- `Align`: `=Align.Center`
- `Color`: `=ColorValue("#003087")`
- `Font`: `=Font.'Segoe UI'`
- `FontWeight`: `=FontWeight.Bold`
- `Height`: `=Parent.Height`
- `Size`: `=18`
- `Text`:

```powerfx
=If(
cmpSubCriteria.SelectedIndex = 0,
"-",
Text(cmpSubCriteria.Score, "0.0")
)
```
- `Width`: `=Parent.Width`
- `ZIndex`: `=1`

#### cntOpt3 (groupContainer.horizontalAutoLayoutContainer)

Properties:

- `BorderColor`: `=ColorValue("#003087")`
- `BorderThickness`: `=1`
- `Fill`:

```powerfx
=If(
cmpSubCriteria.SelectedIndex = 3,
ColorValue("#003087"),
RGBA(0, 0, 0, 0)
)
```
- `Height`: `=lblOpt3.Height + 16`
- `LayoutAlignItems`: `=LayoutAlignItems.Center`
- `LayoutMode`: `=LayoutMode.Auto`
- `PaddingBottom`: `=8`
- `PaddingLeft`: `=8`
- `PaddingRight`: `=8`
- `PaddingTop`: `=8`
- `RadiusBottomLeft`: `=6`
- `RadiusBottomRight`: `=6`
- `RadiusTopLeft`: `=6`
- `RadiusTopRight`: `=6`
- `Visible`: `=cmpSubCriteria.OptionCount = 3`
- `Width`: `=Parent.Width - cntScore.Width - 8`
- `X`: `=1`
- `Y`: `=cntOpt2.Y + cntOpt2.Height + 4`
- `ZIndex`: `=2`

Children:

##### lblOpt3 (label)

Properties:

- `AutoHeight`: `=true`
- `Color`:

```powerfx
=If(
cmpSubCriteria.SelectedIndex = 3,
Color.White,
ColorValue("#003087")
)
```
- `Font`: `=Font.'Segoe UI'`
- `LayoutMaxHeight`: `=0`
- `LayoutMaxWidth`: `=0`
- `LayoutMinHeight`: `=16`
- `LayoutMinWidth`: `=16`
- `OnSelect`:

```powerfx
=If(
cmpSubCriteria.Enable && cmpSubCriteria.OptionCount >= 3,
cmpSubCriteria.OnSelectOption3()
)
```
- `PaddingBottom`: `=2`
- `PaddingLeft`: `=2`
- `PaddingRight`: `=2`
- `PaddingTop`: `=2`
- `Text`: `=cmpSubCriteria.Option3Text`
- `Visible`: `=cmpSubCriteria.OptionCount = 3`
- `Width`: `=Parent.Width - 32`
- `X`: `=16`
- `Y`: `=8`
- `ZIndex`: `=1`

#### cntOpt2 (groupContainer.horizontalAutoLayoutContainer)

Properties:

- `BorderColor`: `=ColorValue("#003087")`
- `BorderThickness`: `=1`
- `Fill`:

```powerfx
=If(
cmpSubCriteria.SelectedIndex = 2,
ColorValue("#003087"),
RGBA(0, 0, 0, 0)
)
```
- `Height`: `=lblOpt2.Height + 16`
- `LayoutAlignItems`: `=LayoutAlignItems.Center`
- `LayoutMode`: `=LayoutMode.Auto`
- `PaddingBottom`: `=8`
- `PaddingLeft`: `=8`
- `PaddingRight`: `=8`
- `PaddingTop`: `=8`
- `RadiusBottomLeft`: `=6`
- `RadiusBottomRight`: `=6`
- `RadiusTopLeft`: `=6`
- `RadiusTopRight`: `=6`
- `Width`: `=Parent.Width - cntScore.Width - 8`
- `X`: `=1`
- `Y`: `=cntOpt1.Y + cntOpt1.Height + 4`
- `ZIndex`: `=3`

Children:

##### lblOpt2 (label)

Properties:

- `AutoHeight`: `=true`
- `Color`:

```powerfx
=If(
cmpSubCriteria.SelectedIndex = 2,
Color.White,
ColorValue("#003087")
)
```
- `Font`: `=Font.'Segoe UI'`
- `LayoutMaxHeight`: `=0`
- `LayoutMaxWidth`: `=0`
- `LayoutMinHeight`: `=16`
- `LayoutMinWidth`: `=16`
- `OnSelect`:

```powerfx
=If(
cmpSubCriteria.Enable && cmpSubCriteria.OptionCount >= 2,
cmpSubCriteria.OnSelectOption2()
)
```
- `PaddingBottom`: `=2`
- `PaddingLeft`: `=2`
- `PaddingRight`: `=2`
- `PaddingTop`: `=2`
- `Text`: `=cmpSubCriteria.Option2Text`
- `Width`: `=Parent.Width - 32`
- `X`: `=16`
- `Y`: `=8`
- `ZIndex`: `=1`

#### cntOpt1 (groupContainer.horizontalAutoLayoutContainer)

Properties:

- `BorderColor`: `=ColorValue("#003087")`
- `BorderThickness`: `=1`
- `Fill`:

```powerfx
=If(
cmpSubCriteria.SelectedIndex = 1,
ColorValue("#003087"),
RGBA(0, 0, 0, 0)
)
```
- `Height`: `=lblOpt1.Height + 16`
- `LayoutAlignItems`: `=LayoutAlignItems.Center`
- `LayoutMode`: `=LayoutMode.Auto`
- `PaddingBottom`: `=8`
- `PaddingLeft`: `=8`
- `PaddingRight`: `=8`
- `PaddingTop`: `=8`
- `RadiusBottomLeft`: `=6`
- `RadiusBottomRight`: `=6`
- `RadiusTopLeft`: `=6`
- `RadiusTopRight`: `=6`
- `Width`: `=Parent.Width - cntScore.Width - 8`
- `X`: `=1`
- `Y`: `=lblCriteriaTitle.Y + lblCriteriaTitle.Height`
- `ZIndex`: `=4`

Children:

##### lblOpt1 (label)

Properties:

- `AutoHeight`: `=true`
- `Color`:

```powerfx
=If(
cmpSubCriteria.SelectedIndex = 1,
Color.White,
ColorValue("#003087")
)
```
- `Font`: `=Font.'Segoe UI'`
- `LayoutMaxHeight`: `=`
- `LayoutMaxWidth`: `=`
- `OnSelect`:

```powerfx
=If(
cmpSubCriteria.Enable && cmpSubCriteria.OptionCount >= 1,
cmpSubCriteria.OnSelectOption1()
)
```
- `PaddingBottom`: `=2`
- `PaddingLeft`: `=2`
- `PaddingRight`: `=2`
- `PaddingTop`: `=2`
- `Text`: `=cmpSubCriteria.Option1Text`
- `Width`: `=Parent.Width - 32`
- `X`: `=40`
- `Y`: `=40`
- `ZIndex`: `=1`

#### lblCriteriaTitle (label)

Properties:

- `AutoHeight`: `=true`
- `Font`: `=Font.'Segoe UI'`
- `FontWeight`: `=FontWeight.Bold`
- `Height`: `=28`
- `Size`: `=18`
- `Text`: `=cmpSubCriteria.TitleText`
- `Width`: `=Parent.Width`
- `ZIndex`: `=5`


---

## cmpYesNoQuestion (CanvasComponent)

**Explicit properties (as defined in source):**

- `ThisProperty`: ``
- `Default`: `=`
- `ContentHeight`: `=cntRow.Y + cntRow.Height + 8`
- `Enable`: `=true`
- `Fill`: `=RGBA(0, 0, 0, 0)`
- `Height`: `=120`
- `Label`: `="Label"`
- `Question`: `="Chose si or no?"`
- `SelectedValue`: `=true`
- `Width`: `=600`
- `X`: `=0`
- `Y`: `=0`
- `ZIndex`: `=1`

**Child controls:**

### lblYesNoLabel (label)

Properties:

- `Color`: `=ColorValue("#003087")`
- `Font`: `=Font.'Segoe UI'`
- `FontWeight`: `=FontWeight.Bold`
- `Size`: `=20`
- `Text`: `=Parent.Label`
- `Width`: `=Parent.Width`
- `Y`: `=8`
- `ZIndex`: `=1`

### cntRow (groupContainer.horizontalAutoLayoutContainer)

Properties:

- `DropShadow`: `=DropShadow.None`
- `Height`: `=Max(lblQuestion.Height, cntButtons.Height)`
- `LayoutGap`: `=16`
- `LayoutMode`: `=LayoutMode.Auto`
- `Width`: `=Parent.Width`
- `Y`: `=lblYesNoLabel.Y + lblYesNoLabel.Height + 8`
- `ZIndex`: `=2`

Children:

#### lblQuestion (label)

Properties:

- `AutoHeight`: `=true`
- `Color`: `=ColorValue("#003087")`
- `Font`: `=Font.'Segoe UI'`
- `LayoutMaxHeight`: `=0`
- `LayoutMaxWidth`: `=0`
- `LayoutMinHeight`: `=16`
- `LayoutMinWidth`: `=16`
- `Size`: `=15`
- `Text`: `=cmpYesNoQuestion.Question`
- `Width`: `=cntRow.Width - cntButtons.Width - cntRow.LayoutGap`
- `ZIndex`: `=1`

#### cntButtons (groupContainer.horizontalAutoLayoutContainer)

Properties:

- `AlignInContainer`: `=AlignInContainer.SetByContainer`
- `FillPortions`: `=0`
- `Height`: `=60`
- `LayoutAlignItems`: `=LayoutAlignItems.End`
- `LayoutGap`: `=8`
- `LayoutMaxHeight`: `=0`
- `LayoutMaxWidth`: `=0`
- `LayoutMinHeight`: `=16`
- `LayoutMinWidth`: `=16`
- `LayoutMode`: `=LayoutMode.Auto`
- `Width`: `=220`
- `ZIndex`: `=2`

Children:

##### btnYes (button)

Properties:

- `Color`:

```powerfx
=If(
cmpYesNoQuestion.SelectedValue = true,
Color.White,
ColorValue("#003087")
)
```
- `Fill`:

```powerfx
=If(
cmpYesNoQuestion.SelectedValue = true,
ColorValue("#003087"),
ColorValue("#E5F0FF")
)
```
- `Height`: `=60`
- `HoverBorderColor`: `=Self.BorderColor`
- `HoverColor`: `=If(cmpYesNoQuestion.Enable, RGBA(255, 255, 255, 1), Self.Color)`
- `HoverFill`: `=If(cmpYesNoQuestion.Enable, ColorValue("#003087"), Self.Fill)`
- `LayoutMaxHeight`: `=0`
- `LayoutMaxWidth`: `=0`
- `LayoutMinHeight`: `=16`
- `LayoutMinWidth`: `=16`
- `OnSelect`: `=If(cmpYesNoQuestion.Enable, cmpYesNoQuestion.OnSelectYes())`
- `PressedColor`: `=If(cmpYesNoQuestion.Enable, Self.Fill, Color.White)`
- `PressedFill`: `=If(cmpYesNoQuestion.Enable, Self.Color)`
- `RadiusBottomLeft`: `=8`
- `RadiusBottomRight`: `=8`
- `RadiusTopLeft`: `=8`
- `RadiusTopRight`: `=8`
- `Text`: `="Sí"`
- `Width`: `=100`
- `ZIndex`: `=1`

##### btnNo (button)

Properties:

- `Color`:

```powerfx
=If(
cmpYesNoQuestion.SelectedValue = false,
Color.White,
ColorValue("#003087")
)
```
- `Fill`:

```powerfx
=If(
cmpYesNoQuestion.SelectedValue = false,
ColorValue("#003087"),
ColorValue("#E5F0FF")
)
```
- `Height`: `=60`
- `HoverBorderColor`: `=Self.BorderColor`
- `HoverColor`: `=If(cmpYesNoQuestion.Enable, RGBA(255, 255, 255, 1), Self.Color)`
- `HoverFill`: `=If(cmpYesNoQuestion.Enable, ColorValue("#003087"), Self.Fill)`
- `LayoutMaxHeight`: `=0`
- `LayoutMaxWidth`: `=0`
- `LayoutMinHeight`: `=16`
- `LayoutMinWidth`: `=16`
- `OnSelect`: `=If(cmpYesNoQuestion.Enable, cmpYesNoQuestion.OnSelectNo())`
- `PressedBorderColor`: `=If(cmpYesNoQuestion.Enable, Self.Fill)`
- `PressedColor`: `=If(cmpYesNoQuestion.Enable, Self.Fill)`
- `PressedFill`: `=If(cmpYesNoQuestion.Enable, Self.Color)`
- `RadiusBottomLeft`: `=8`
- `RadiusBottomRight`: `=8`
- `RadiusTopLeft`: `=8`
- `RadiusTopRight`: `=8`
- `Text`: `="No"`
- `Width`: `=100`
- `ZIndex`: `=2`


---
