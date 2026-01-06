# A006 Power App – Landing Screen (scrLanding)

Source: `Src/scrLanding.fx.yaml`

## Screen: scrLanding

**Explicit screen properties:**

```powerfx
Fill: =RGBA(248, 249, 250, 1)
OnVisible: |-
    =// scrLanding.OnVisible
    // 1. Get user info from global variables (set in App.OnStart)
    Set(varUserEmail, gblUserEmail);
    Set(varUserName, gblUserName);
    
    // 2. Count pending evaluations for this user
    Clear(colPending);
    Collect(
        colPending,
        Filter(ongoingEvaluations,
            email_responsable_tecnico = varUserEmail &&
            tecnico_submitted = false
        )
    );
    Collect(
        colPending,
        Filter(ongoingEvaluations,
            email_responsable_sst = varUserEmail &&
            responsable_sst_responde = true &&
            sst_submitted = false
        )
    );
    Collect(
        colPending,
        Filter(ongoingEvaluations,
            email_responsable_ma = varUserEmail &&
            responsable_ma_responde = true &&
            ma_submitted = false
        )
    );
    Collect(
        colPending,
        Filter(ongoingEvaluations,
            email_jefe_area = varUserEmail &&
            aprobada = false &&
            tecnico_submitted = true &&
            sst_submitted = true &&
            ma_submitted = true
        )
    );
    Set(varPendingCount, CountRows(Distinct(colPending, ID)));

    /*// 2. Count pending evaluations for this user
    Set(
        varPendingCount,
        CountIf(
            ongoingEvaluations,
            (
                // 1. Técnico: sees rejected items OR items they haven't submitted yet
                (email_responsable_tecnico = varUserEmail && !tecnico_submitted) ||
                
                // 2. SST: sees non-rejected items where they are responsible and haven't submitted
                (email_responsable_sst = varUserEmail && responsable_sst_responde && !sst_submitted) ||
                
                // 3. MA: sees non-rejected items where they are responsible and haven't submitted
                (email_responsable_ma = varUserEmail && responsable_ma_responde && !ma_submitted) ||
                
                // 4. Approver: sees items ready for approval
                (
                    email_jefe_area = varUserEmail && 
                    !aprobada && 
                    tecnico_submitted && sst_submitted && ma_submitted
                )
            )
        )
    );*/
    

```

## Controls on scrLanding

### conHeader (groupContainer.manualLayoutContainer)

- **Parent:** `scrLanding`

**Explicit properties / formulas:**

```powerfx
Fill: |-
    =ColorValue("#003087")
Height: =100
Width: =Parent.Width
ZIndex: =1

```

### conHeaderContent (groupContainer.horizontalAutoLayoutContainer)

- **Parent:** `conHeader`

**Explicit properties / formulas:**

```powerfx
DropShadow: =DropShadow.None
Height: =Parent.Height
LayoutAlignItems: =LayoutAlignItems.Center
LayoutGap: =20
LayoutJustifyContent: =LayoutJustifyContent.SpaceBetween
LayoutMode: =LayoutMode.Auto
PaddingBottom: =8
PaddingLeft: =40
PaddingRight: =40
PaddingTop: =8
Width: =Parent.Width
ZIndex: =3

```

### imgLogo (image)

- **Parent:** `conHeaderContent`

**Explicit properties / formulas:**

```powerfx
Height: =150
Image: =logo2
LayoutMaxHeight: =
LayoutMaxWidth: =
Width: =150
X: =60
ZIndex: =1

```

### conMenuGroup (groupContainer.horizontalAutoLayoutContainer)

- **Parent:** `conHeaderContent`

**Explicit properties / formulas:**

```powerfx
DropShadow: =DropShadow.None
LayoutAlignItems: =LayoutAlignItems.Center
LayoutGap: =40
LayoutJustifyContent: =LayoutJustifyContent.End
LayoutMaxHeight: =0
LayoutMaxWidth: =0
LayoutMinHeight: =16
LayoutMinWidth: =16
LayoutMode: =LayoutMode.Auto
PaddingBottom: =8
PaddingLeft: =8
PaddingRight: =8
PaddingTop: =8
Width: =Parent.Width - imgLogo.Width
ZIndex: =2

```

### btnEvaluateProviders (cmpHeaderButton)

- **Parent:** `conMenuGroup`

**Explicit properties / formulas:**

```powerfx
Destination: =scrEvaluations
IsActive: =App.ActiveScreen = scrEvaluations
Label: ="Evaluacions"
LayoutMaxHeight: =
LayoutMaxWidth: =
LayoutMinHeight: =640
LayoutMinWidth: =640

```

### btnSignLetters (cmpHeaderButton)

- **Parent:** `conMenuGroup`

**Explicit properties / formulas:**

```powerfx
ChildTabPriority: =true
ContentLanguage: =""
Destination: =scrFirmarCartas
EnableChildFocus: =true
IsActive: =App.ActiveScreen = scrFirmarCartas
Label: ="Firmar Cartas"
LayoutMaxHeight: =
LayoutMaxWidth: =
LayoutMinHeight: =640
LayoutMinWidth: =640
Visible: =true
ZIndex: =3

```

### btnDashboard (cmpHeaderButton)

- **Parent:** `conMenuGroup`

**Explicit properties / formulas:**

```powerfx
ChildTabPriority: =true
ContentLanguage: =""
Destination: =scrDashboard
EnableChildFocus: =true
IsActive: =App.ActiveScreen = scrDashboard
Label: ="Dashboard"
LayoutMaxHeight: =
LayoutMaxWidth: =
LayoutMinHeight: =640
LayoutMinWidth: =640
Visible: =true
ZIndex: =4

```

### lblWelcome (label)

- **Parent:** `scrLanding`

**Explicit properties / formulas:**

```powerfx
Align: =Align.Center
Color: |-
    =ColorValue("#003087")
Font: =Font.'Segoe UI'
FontWeight: =FontWeight.Bold
Height: =60
Size: =24
Text: ="Welcome " & gblUserName & ", you have " & varPendingCount & " pending evaluations to complete"
Width: =Parent.Width
Y: =300
ZIndex: =2

```

