# A006 Power App – Screen Summary: scrEvaluations

_Source: `scrEvaluations.pa.yaml` (unpacked from `.msapp` via `pac canvas unpack`)_

## Overall description

`scrEvaluations` is the main working screen where a user sees the list of **pending evaluations** assigned to them based on their role (Tecnico / SST / MA / Approver). 
It initializes user context on `OnVisible`, loads evaluation data into local collections (e.g., `colEvaluations`), and provides UI controls for navigation and actions to open an evaluation, sign letters, and access dashboard/reporting depending on permissions.

## Screen properties

- **LoadingSpinner**: `=LoadingSpinner.Data`
- **LoadingSpinnerColor**: `=RGBA(56, 96, 178, 1)`
### OnVisible
```powerfx
=// scrEvaluateProviders.OnVisible
Set(varUserEmail, gblUserEmail);
Set(varSelectedEvaluation, Blank());
Select(btnClearCollection);
Reset(cbbSelectEvaluation);

/*

*/
```


## Controls and explicit properties

### conFunctions

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **Height**: `=0`
  - **Visible**: `=false`
  - **Width**: `=0`

- **Children**: 1

#### btnClearCollection

- **Control**: `Classic/Button@2.2.0`

- **Properties (explicitly set)**:
  - **BorderColor**: `=ColorFade(Self.Fill, -15%)`
  - **Color**: `=RGBA(255, 255, 255, 1)`
  - **DisabledBorderColor**: `=RGBA(166, 166, 166, 1)`
  - **Fill**: `=RGBA(56, 96, 178, 1)`
  - **Font**: `=Font.'Open Sans'`
  - **HoverBorderColor**: `=ColorFade(Self.BorderColor, 20%)`
  - **HoverColor**: `=RGBA(255, 255, 255, 1)`
  - **HoverFill**: `=ColorFade(RGBA(56, 96, 178, 1), -20%)`
  - **OnSelect**: `=Clear(colEvaluations);
Collect(
    colEvaluations,
    // 1. Técnico
    Filter(
        ongoingEvaluations,
        email_responsable_tecnico = varUserEmail &&
        tecnico_submitted = false
    ),
    // 2. SST
    Filter(
        ongoingEvaluations,
        email_responsable_sst = varUserEmail &&
        responsable_sst_responde = true &&
        sst_submitted = false
    ),
    // 3. MA
    Filter(
        ongoingEvaluations,
        email_responsable_ma = varUserEmail &&
        responsable_ma_responde = true &&
        ma_submitted = false
    ),
    // 4. Approver
    Filter(
        ongoingEvaluations,
        email_jefe_area = varUserEmail &&
        aprobada = false &&
        tecnico_submitted = true &&
        sst_submitted = true && 
        ma_submitted = true        
    )
);`
  - **PressedBorderColor**: `=Self.Fill`
  - **PressedColor**: `=Self.Fill`
  - **PressedFill**: `=Self.Color`
### conFooter

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=60`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Horizontal`
  - **LayoutGap**: `=12`
  - **LayoutJustifyContent**: `=LayoutJustifyContent.End`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=40`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`
  - **Y**: `=Parent.Height - Self.Height`

- **Children**: 4

#### cmpReject

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Fill**: `=Color.Transparent`
  - **Height**: `=48`
  - **IsEnabled**: `=true`
  - **OnSelectAction**: `=// Patch data to SharePoint
Patch(
    ongoingEvaluations,
    varSelectedEvaluation,
    If(varCanApprove, {
        tecnico_submitted: false,
        sst_submitted: false,
        ma_submitted: false,
        aprobada: false,
        rechazada: true,
        observaciones_jefe_area: cmpObservationJefeArea.Value,       
        numero_rechazos: varNumeroRechazos + 1
    }, {})    
);
// Notification
Notify("Evaluation rejected", NotificationType.Success);
// Refresh data
Select(btnClearCollection);
// Reset selection (tuỳ chọn)
Set(varSelectedEvaluation, Blank());`
  - **Text**: `="Rechazada"`
  - **Visible**: `=varCanApprove`
  - **Width**: `=220`

#### cmpApprove

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Fill**: `=Color.Transparent`
  - **Height**: `=48`
  - **IsEnabled**: `=true`
  - **OnSelectAction**: `=// Patch data to SharePoint
Patch(
    ongoingEvaluations,
    varSelectedEvaluation,
    If(varCanApprove, {
        aprobada: true,
        rechazada: false,
        observaciones_jefe_area: cmpObservationJefeArea.Value       
    }, {})    
);
// Notification
Notify("Evaluation approved", NotificationType.Success);
// Refresh data
Select(btnClearCollection);
// Reset selection (tuỳ chọn)
Set(varSelectedEvaluation, Blank());`
  - **Text**: `="Aprobada"`
  - **Visible**: `=varCanApprove`
  - **Width**: `=220`

#### cmpReset

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Fill**: `=Color.Transparent`
  - **Height**: `=48`
  - **IsEnabled**: `=true`
  - **OnSelectAction**: `=// 1. Reset Técnico Data
If(varCanEditTecnico,
    Set(varCalidadQ1Selected, 0); // Calidad 1
    Set(varCalidadQ2Selected, 0);
    Set(varCalidadQ3Selected, 0);
    Set(varCalidadQ4Selected, 0);
    Set(varCalidadQ5Selected, 0);
    Set(varOportunidadQ1Selected, 0); // Oportunidad
    Set(varGestionQ1Selected, 0); // Gestion 1
    Set(varGestionQ2Selected, 0); // Gestion 2
    Set(varEtica, false);    
    Set(varMA, varSelectedEvaluation.responsable_ma_responde); // Revert to original flag
    Set(varObservationTecnico, "")
);
// 2. Reset SST Data
If(varCanEditSST,
    Set(varSSTQ1Selected, 0); 
    Set(varObservationSST, "")
);
// 3. Reset MA Data
If(varCanEditMA,
    Set(varMAQ1Selected, 0);
    Set(varObservationMA, "")
);
// Refresh data
Select(btnClearCollection);
// Reset selection (tuỳ chọn)
Set(varSelectedEvaluation, Blank());
`
  - **Text**: `="Limpiar"`
  - **Visible**: `=varCanEditMA || varCanEditSST || varCanEditTecnico`
  - **Width**: `=220`

#### cmpSubmit

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Fill**: `=Color.Transparent`
  - **Height**: `=48`
  - **IsEnabled**: `=// Button Enviar - IsEnabled
// Técnico validation
If(varCanEditTecnico,
    Value(cmpCalidadQ1.SelectedIndex) > 0 &&
    Value(cmpCalidadQ2.SelectedIndex) > 0 &&
    Value(cmpCalidadQ3.SelectedIndex) > 0 &&
    Value(cmpCalidadQ4.SelectedIndex) > 0 &&
    Value(cmpCalidadQ5.SelectedIndex) > 0 &&
    Value(cmpOportunidadQ1.SelectedIndex) > 0 &&
    Value(cmpGestionQ1.SelectedIndex) > 0 &&
    Value(cmpGestionQ2.SelectedIndex) > 0,
    true
) &&
// SST validation
If(varCanEditSST,
    Value(cmpSST.SelectedIndex) > 0,
    true
) &&
// MA validation
If(varCanEditMA,
    Value(cmpMA.SelectedIndex) > 0,
    true
)`
  - **OnSelectAction**: `=// Patch dữ liệu về SharePoint
Patch(
    ongoingEvaluations,
    varSelectedEvaluation,
    {
        puntaje_subtotal: If(lblSubtotalValue.Text = "-", Blank(), Value(lblSubtotalValue.Text))
    },
    If(varCanEditTecnico, {
        calidad_pregunta_1:	Value(cmpCalidadQ1.SelectedIndex),	
        calidad_pregunta_2:	Value(cmpCalidadQ2.SelectedIndex),		
        calidad_pregunta_3:	Value(cmpCalidadQ3.SelectedIndex),		
        calidad_pregunta_4:	Value(cmpCalidadQ4.SelectedIndex),		
        calidad_pregunta_5: Value(cmpCalidadQ5.SelectedIndex),
        oportunidad_pregunta_1:	Value(cmpOportunidadQ1.SelectedIndex),	
        gestion_pregunta_1:	Value(cmpGestionQ1.SelectedIndex),	
        gestion_pregunta_2:	Value(cmpGestionQ2.SelectedIndex),
        cumple_codigo_etica: varEtica,
        responsable_ma_responde: varMA,
        observaciones_tecnico: cmpObservationTecnico.Value,
        tecnico_submitted: true
    }, {}),
    If(varCanEditSST, {
        sst_pregunta_1:	Value(cmpSST.SelectedIndex),	
        observaciones_sst: cmpObservationSST.Value,	
        sst_submitted: true
    }, {}),
    If(varCanEditMA, {
        ma_pregunta_1:	Value(cmpMA.SelectedIndex),
        observaciones_ma: cmpObservationMA.Value,   
        ma_submitted: true
    }, {})
);
// Thông báo thành công
Notify("Evaluación guardada correctamente", NotificationType.Success);
// Refresh data
Select(btnClearCollection);
// Reset selection (tuỳ chọn)
Set(varSelectedEvaluation, Blank());`
  - **Text**: `="Enviar"`
  - **Visible**: `=varCanEditMA || varCanEditSST || varCanEditTecnico`
  - **Width**: `=220`
### conMain

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=App.Height - conHeader.Height - conFooter.Height`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Horizontal`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`
  - **Y**: `=conHeader.Height`

- **Children**: 2

#### conSideBar

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(225, 223, 221, 1)`
  - **BorderStyle**: `=BorderStyle.None`
  - **DropShadow**: `=DropShadow.None`
  - **Fill**: `=RGBA(255, 255, 255, 1)`
  - **FillPortions**: `=0.7`
  - **Height**: ``
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=12`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=24`
  - **PaddingLeft**: `=24`
  - **PaddingRight**: `=24`
  - **PaddingTop**: `=24
`

- **Children**: 2

##### cbbSelectEvaluation

- **Control**: `Classic/ComboBox@2.4.0`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(200, 200, 200, 1)`
  - **ChevronBackground**: `=RGBA(255, 255, 255, 1)`
  - **ChevronFill**: `=RGBA(0, 120, 212, 1)`
  - **ChevronHoverBackground**: `=ColorFade(RGBA(56, 96, 178, 1), -20%)`
  - **ChevronHoverFill**: `=RGBA(255, 255, 255, 1)`
  - **DisplayFields**: `=["DisplayText"]`
  - **Font**: `=Font.'Open Sans'`
  - **HoverFill**: `=RGBA(186, 202, 226, 1)`
  - **Items**: `=Sort(
    AddColumns(
        colEvaluations,
        DisplayText,
            Text(fecha_evaluacion, "dd/mm/yyyy") & 
            " - " & nombre_proveedor
    ),
    DisplayText,
    SortOrder.Ascending
)

/*
AddColumns(
    Filter(
        ongoingEvaluations, 
        (
            // 1. Técnico: Sees if not submitted OR if rejected (to fix)
            (email_responsable_tecnico = varUserEmail && !tecnico_submitted) ||
            // 2. SST: Sees ONLY if flag is TRUE and not submitted
            (email_responsable_sst = varUserEmail && responsable_sst_responde && !sst_submitted) ||
            // 3. MA: Sees ONLY if flag is TRUE and not submitted
            (email_responsable_ma = varUserEmail && responsable_ma_responde && !ma_submitted) ||
            // 4. Approver: Sees if ready for approval (All submitted & Not processed)
            (email_jefe_area = varUserEmail && 
                !aprobada && !rechazada &&
                tecnico_submitted && sst_submitted && ma_submitted            
            )
        )
    ),
    DisplayText,
        Text(fecha_evaluacion, "dd/mm/yyyy") & 
        " - " & nombre_proveedor
)
*/`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **OnChange**: `=Set(
    varSelectedEvaluation,
    LookUp(
        ongoingEvaluations,
        ID = Self.Selected.ID          
    )
);
// Set selected evaluation
//Set(varSelectedEvaluation, Self.Selected);

// === load value & flags from sharepoint list
Set(varCalidadQ1Selected, Coalesce(varSelectedEvaluation.calidad_pregunta_1, 0));
Set(varCalidadQ2Selected, Coalesce(varSelectedEvaluation.calidad_pregunta_2, 0));
Set(varCalidadQ3Selected, Coalesce(varSelectedEvaluation.calidad_pregunta_3, 0));
Set(varCalidadQ4Selected, Coalesce(varSelectedEvaluation.calidad_pregunta_4, 0));
Set(varCalidadQ5Selected, Coalesce(varSelectedEvaluation.calidad_pregunta_5, 0));
Set(varOportunidadQ1Selected, Coalesce(varSelectedEvaluation.oportunidad_pregunta_1, 0));
Set(varGestionQ1Selected, Coalesce(varSelectedEvaluation.gestion_pregunta_1, 0));
Set(varGestionQ2Selected, Coalesce(varSelectedEvaluation.gestion_pregunta_2, 0));
Set(varSSTQ1Selected, Coalesce(varSelectedEvaluation.sst_pregunta_1, 0));
Set(varMAQ1Selected, Coalesce(varSelectedEvaluation.ma_pregunta_1, 0));
Set(varSST, varSelectedEvaluation.responsable_sst_responde);
Set(varMA, varSelectedEvaluation.responsable_ma_responde);
Set(varEtica, Coalesce(varSelectedEvaluation.cumple_codigo_etica, false));
Set(varObservationTecnico, Coalesce(varSelectedEvaluation.observaciones_tecnico, ""));
Set(varObservationSST, Coalesce(varSelectedEvaluation.observaciones_sst, ""));
Set(varObservationMA, Coalesce(varSelectedEvaluation.observaciones_ma, ""));
Set(varObservationJefeArea, Coalesce(varSelectedEvaluation.observaciones_jefe_area, ""));
Set(varTecnicoSubmitted, varSelectedEvaluation.tecnico_submitted);
Set(varSSTSubmitted, varSelectedEvaluation.sst_submitted);
Set(varMASubmitted, varSelectedEvaluation.ma_submitted);
Set(varAprobada, varSelectedEvaluation.aprobada);
Set(varRechazada, varSelectedEvaluation.rechazada);
Set(varNumeroRechazos, Coalesce(varSelectedEvaluation.numero_rechazos, 0));
Reset(cmpObservationTecnico);
Reset(cmpObservationSST);
Reset(cmpObservationMA);
Reset(cmpObservationJefeArea);


// === PERMISSIONS LOGIC ===
// 1. Técnico Permission: Email matches AND Not Submitted Yet
Set(varCanEditTecnico, 
    varSelectedEvaluation.email_responsable_tecnico = varUserEmail &&
    !varTecnicoSubmitted
);
// 2. SST Section Permission
// Logic: (Flag=True & I am SST & !SST_Sub) OR (Flag=False & I am Técnico & !SST_Sub)
Set(varCanEditSST,
    If(varSelectedEvaluation.responsable_sst_responde,
        varSelectedEvaluation.email_responsable_sst = varUserEmail && !varSSTSubmitted,
        varSelectedEvaluation.email_responsable_tecnico = varUserEmail && !varSSTSubmitted
    )
);
// 3. MA Section Permission
// Logic: (Flag=True & I am MA & !MA_Sub) OR (Flag=False & I am Técnico & !MA_Sub)
Set(varCanEditMA,
    If(varSelectedEvaluation.responsable_ma_responde,
        varSelectedEvaluation.email_responsable_ma = varUserEmail && !varMASubmitted,
        varSelectedEvaluation.email_responsable_tecnico = varUserEmail && !varMASubmitted
    )
);
// 4. Approver Permission
Set(varCanApprove, 
    varSelectedEvaluation.email_jefe_area = varUserEmail && 
    !varSelectedEvaluation.aprobada && 
    varTecnicoSubmitted &&
    varSSTSubmitted &&
    varMASubmitted
);

`
  - **PressedColor**: `=RGBA(255, 255, 255, 1)`
  - **PressedFill**: `=RGBA(0, 18, 107, 1)`
  - **SearchFields**: `=["documento_compras","DisplayText","nombre_proveedor"]`
  - **SelectMultiple**: `=false`
  - **SelectionColor**: `=RGBA(255, 255, 255, 1)`
  - **SelectionFill**: `=RGBA(56, 96, 178, 1)`
  - **Width**: `=Parent.Width - 20`

##### galQuickNav

- **Control**: `Gallery` (Vertical)
- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(200, 200, 200, 1)`
  - **BorderStyle**: `=BorderStyle.Solid`
  - **FillPortions**: `=1`
  - **LayoutMinHeight**: `=300`
  - **LayoutMinWidth**: `=200`
  - **Items**: 
    ```powerfx
    Table(
        { Key: "C1", Name: "Calidad Q1", Score: cmpCalidadQ1.SelectedIndex },
        { Key: "C2", Name: "Calidad Q2", Score: cmpCalidadQ2.SelectedIndex },
        { Key: "C3", Name: "Calidad Q3", Score: cmpCalidadQ3.SelectedIndex },
        { Key: "C4", Name: "Calidad Q4", Score: cmpCalidadQ4.SelectedIndex },
        { Key: "C5", Name: "Calidad Q5", Score: cmpCalidadQ5.SelectedIndex },
        { Key: "O1", Name: "Oportunidad Q1", Score: cmpOportunidadQ1.SelectedIndex },
        { Key: "G1", Name: "Gestión Q1", Score: cmpGestionQ1.SelectedIndex },
        { Key: "G2", Name: "Gestión Q2", Score: cmpGestionQ2.SelectedIndex },
        { Key: "SST", Name: "SST", Score: cmpSST.SelectedIndex },
        { Key: "MA", Name: "MA", Score: cmpMA.SelectedIndex }
    )
    ```
  - **OnSelect**: 
    ```powerfx
    // Navigate/Focus on the specific control
    Switch(ThisItem.Key,
        "C1", SetFocus(cmpCalidadQ1),
        "C2", SetFocus(cmpCalidadQ2),
        "C3", SetFocus(cmpCalidadQ3),
        "C4", SetFocus(cmpCalidadQ4),
        "C5", SetFocus(cmpCalidadQ5),
        "O1", SetFocus(cmpOportunidadQ1),
        "G1", SetFocus(cmpGestionQ1),
        "G2", SetFocus(cmpGestionQ2),
        "SST", SetFocus(cmpSST),
        "MA", SetFocus(cmpMA)
    )
    ```
  - **TemplateSize**: `=40`
  - **Width**: `=Parent.Width - 20`

- **Children**: 2

###### lblNavName
- **Control**: `Label`
- **Properties**: 
  - **Text**: `=ThisItem.Name`
  - **FontWeight**: `=If(ThisItem.Score > 0, FontWeight.Bold, FontWeight.Normal)`
  - **Height**: `=Parent.TemplateHeight`
  - **Width**: `=Parent.TemplateWidth - 50`

###### lblNavScore
- **Control**: `Label`
- **Properties**: 
  - **Text**: `=ThisItem.Score`
  - **Align**: `=Align.Center`
  - **Height**: `=Parent.TemplateHeight`
  - **X**: `=Parent.TemplateWidth - 50`
  - **Width**: `=50`

#### conMainForm

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=3`
  - **Height**: `=Parent.Height`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=15`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **LayoutOverflowY**: `=LayoutOverflow.Scroll`
  - **PaddingBottom**: `=24`
  - **PaddingLeft**: `=24`
  - **PaddingRight**: `=24`
  - **PaddingTop**: `=4`
  - **Width**: `=0
`

- **Children**: 8

##### conEmptyState

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Visible**: `=IsBlank(varSelectedEvaluation)`

- **Children**: 1

###### lblEmpty

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=Color.MediumPurple`
  - **Font**: `=Font.'Open Sans'`
  - **Height**: `=49`
  - **Text**: `="Por favor, selecciona una evaluación de la lista"`
  - **Width**: `=892`
  - **X**: `=40`
  - **Y**: `=40`

##### conFormHeader

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(225, 223, 221, 1)`
  - **BorderThickness**: `=1`
  - **Fill**: `=RGBA(255, 255, 255, 1)`
  - **FillPortions**: `=0`
  - **Height**: `=190`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=16`
  - **PaddingLeft**: `=16`
  - **PaddingRight**: `=16`
  - **PaddingTop**: `=16`
  - **RadiusBottomLeft**: `=8`
  - **RadiusBottomRight**: `=8`
  - **RadiusTopLeft**: `=8`
  - **RadiusTopRight**: `=8`
  - **Visible**: `=!IsBlank(varSelectedEvaluation)`

- **Children**: 1

###### conEvaluationHeader

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=Parent.Height`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=8`
  - **PaddingBottom**: `=10`
  - **PaddingLeft**: `=10`
  - **PaddingRight**: `=10`
  - **PaddingTop**: `=10`
  - **Width**: `=Parent.Width`

- **Children**: 2

####### lblContractTitle

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Bold`
  - **Size**: `=20`
  - **Text**: `="Evaluación de Desempeño – Contrato N° " & varSelectedEvaluation.documento_compras`
  - **Width**: `=Parent.Width - 20`
  - **X**: `=40`
  - **Y**: `=40`

####### conHeaderDetails

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=220`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Horizontal`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width - 40`

- **Children**: 2

######## conDetailsCol1

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`

- **Children**: 4

######### lblSociedad

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **Text**: `="Sociedad:    " & varSelectedEvaluation.sociedad
`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

######### lblProveedor

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **Text**: `="Proveedor:  " & varSelectedEvaluation.nombre_proveedor

`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

######### lblValor

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **Text**: `="Valor:           " & varSelectedEvaluation.valor_contrato
`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

######### lblEvaluador

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **Text**: `="Evaluador:  " & varSelectedEvaluation.nombre_responsable_tecnico
`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

######## conDetailsCol2

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=1.4`
  - **Height**: `=300`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`

- **Children**: 4

######### lblEvaluation

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **LineHeight**: `=1`
  - **Text**: `="Inicio Validez:    " & varSelectedEvaluation.fecha_evaluacion
`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

######### lblInicioValidez

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **LineHeight**: `=1`
  - **Text**: `="Inicio Validez:    " & varSelectedEvaluation.fecha_inicio_validez
`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

######### lblFinValidez

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **LineHeight**: `=1`
  - **Text**: `="Fin Validez:        " & varSelectedEvaluation.fecha_fin_validez
`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

######### lblConsecutiveEvaluation

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **Height**: `=30`
  - **LineHeight**: `=1`
  - **Text**: `="Consecutivo evaluación:" & varSelectedEvaluation.consecutivo_evaluacion
`
  - **Width**: `=Parent.Width`
  - **X**: `=40`
  - **Y**: `=40`

##### conTecnicoSection

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(225, 223, 221, 1)`
  - **BorderThickness**: `=1`
  - **Fill**: `=If(varCanEditTecnico, RGBA(255, 255, 255, 1),RGBA(240, 240, 240, 1))`
  - **FillPortions**: `=0`
  - **Height**: `=conTecnicoContent.Height`
  - **PaddingBottom**: `=20`
  - **PaddingLeft**: `=20`
  - **PaddingRight**: `=20`
  - **PaddingTop**: `=20`
  - **RadiusBottomLeft**: `=8`
  - **RadiusBottomRight**: `=8`
  - **RadiusTopLeft**: `=8`
  - **RadiusTopRight**: `=8`
  - **Visible**: `=!IsBlank(varSelectedEvaluation) && (varCanEditTecnico || varCanApprove)`
  - **Width**: `=Parent.Width`

- **Children**: 1

###### conTecnicoContent

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=lblTecnicoHeading.Height + conCalidadField.Height + conOportunidadField.Height + conGestionField.Height + conCodigoEticaField.Height + conObservacionesField.Height + conMAResonsable.Height + 8*8`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=8`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`

- **Children**: 7

####### lblTecnicoHeading

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **DisplayMode**: `=DisplayMode.View`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Bold`
  - **Height**: `=36`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=20`
  - **Text**: `="TÉCNICO SECTION"`
  - **Width**: `=Parent.Width - Parent.PaddingLeft*2`

####### conCalidadField

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpCalidadQ5.Y + cmpCalidadQ5.Height + 10`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 6

######## cmpCalidadQ5

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpCalidadQ5.ContentHeight
`
  - **OnSelectOption1**: `=Set(varCalidadQ5Selected, 1)`
  - **OnSelectOption2**: `=Set(varCalidadQ5Selected, 2)`
  - **OnSelectOption3**: `=Set(varCalidadQ5Selected, 3)`
  - **Option1Text**: `="Cuando fue requerido, el contratista dispuso el personal  y / o los medios requeridos para atender la situación en el sitio requerido"`
  - **Option2Text**: `="En algunas de las ocasiones en las que fue requerido, el contratista dispuso el personal  y / o los medios requeridos para atender la situación en el sitio requerido"`
  - **Option3Text**: `="Cuando fue requerido, el contratista no dispuso el personal  y / o los medios requeridos para atender la situación en el sitio requerido. (y/o se incurran en faltas o incumplimientos asociados a la atención oportuna de los requerimientos que de lugar a la apliación de una multa) "`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varCalidadQ5Selected`
  - **TitleText**: `="Calidad de los servicios prestados: atención a los requerimientos"`
  - **TotalPoints**: `=6`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpCalidadQ4.Y + cmpCalidadQ4.Height+ 10`

######## cmpCalidadQ4

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpCalidadQ4.ContentHeight
`
  - **OnSelectOption1**: `=Set(varCalidadQ4Selected, 1)`
  - **OnSelectOption2**: `=Set(varCalidadQ4Selected, 2)`
  - **OnSelectOption3**: `=Set(varCalidadQ4Selected, 3)`
  - **Option1Text**: `="El personal asignado fue suficiente y tenía las competencias necesarias para ejecutar todas las actividades del contrato."`
  - **Option2Text**: `="El personal asignado inicialmente, no fue suficiente y/o no tenía las competencias necesarias para ejecutar todas las actividades del contrato, situación que fue subsanada oportunamente."`
  - **Option3Text**: `="El personal asignado no fue suficiente y/o no tenía las competencias necesarias para ejecutar todas las actividades del contrato, situación que no fue subsanada. (y/o se incurran en faltas o incumplimientos asociados al desempeño del personal que ejecutó el servicio que de lugar a la apliación de una multa) "`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varCalidadQ4Selected`
  - **TitleText**: `="Calidad de los Servicios Prestados: Personal que ejecutó el Servicio."`
  - **TotalPoints**: `=6`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpCalidadQ3.Y + cmpCalidadQ3.Height+ 10`

######## cmpCalidadQ3

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpCalidadQ3.ContentHeight
`
  - **OnSelectOption1**: `=Set(varCalidadQ3Selected, 1)`
  - **OnSelectOption2**: `=Set(varCalidadQ3Selected, 2)`
  - **OnSelectOption3**: `=Set(varCalidadQ3Selected, 3)`
  - **Option1Text**: `="El servicio prestado cumplió con todas las especificaciones y normas técnicas establecidas en el contrato."`
  - **Option2Text**: `="El servicio prestado tuvo algunos incumplimientos y/o fallas menores, asociadas al cumplimiento de las especificaciones y/o a las normas técnicas."`
  - **Option3Text**: `="El servicio prestado tuvo incumplimientos y/o fallas graves, asociadas al cumplimiento de las especificaciones y/o a las normas  técnicas. (y/o se incurran en faltas o incumplimientos asociados a las especificaciones de los servicios que de lugar a la apliación de una multa) "`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varCalidadQ3Selected`
  - **TitleText**: `="Calidad de los Servicios Prestados: cumplimiento de especificaciones"`
  - **TotalPoints**: `=6`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpCalidadQ2.Y + cmpCalidadQ2.Height+ 10`

######## cmpCalidadQ2

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpCalidadQ2.ContentHeight
`
  - **OnSelectOption1**: `=Set(varCalidadQ2Selected, 1)`
  - **OnSelectOption2**: `=Set(varCalidadQ2Selected, 2)`
  - **OnSelectOption3**: `=Set(varCalidadQ2Selected, 3)`
  - **Option1Text**: `="El personal demuestra, sistemática y evidentemente cooperación 
que asegura el adecuado suministro de los bienes"`
  - **Option2Text**: `="El personal demuestra poca o regular cooperación, lo que dificulta el adecuado suministro de los bienes"`
  - **Option3Text**: `="El personal no demuestra cooperación, incluso cuando se le solicita, lo que dificulta el adecuado suministro de los bienes"`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varCalidadQ2Selected`
  - **TitleText**: `="Calidad de los Bienes Suministrados: Cooperación"`
  - **TotalPoints**: `=6`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpCalidadQ1.Y + cmpCalidadQ1.Height+ 10`

######## cmpCalidadQ1

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpCalidadQ1.ContentHeight
`
  - **OnSelectOption1**: `=Set(varCalidadQ1Selected, 1)`
  - **OnSelectOption2**: `=Set(varCalidadQ1Selected, 2)`
  - **OnSelectOption3**: `=Set(varCalidadQ1Selected, 3)`
  - **Option1Text**: `="Todos los bienes se recibieron a satisfacción y sin problemas de funcionamiento."`
  - **Option2Text**: `="Algunos de los bienes recibidos a satisfacción presentaron problemas de funcionamiento después de la entrega, pero fueron intervenidos oportunamente por el contratista."`
  - **Option3Text**: `="Algunos de los bienes recibidos a satisfacción presentaron problemas de funcionamiento después de la entrega, pero NO fueron intervenidos oportunamente por el contratista. (y/o se incurran en faltas o incumplimientos asociados a las especificaciones de los bienes que de lugar a la apliación de una multa)"`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varCalidadQ1Selected`
  - **TitleText**: `="Calidad de los Bienes Suministrados: cumplimiento especificaciones"`
  - **TotalPoints**: `=6`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpTitleCalidad.Height + 10`

######## cmpTitleCalidad

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Height**: `=30`
  - **ShowSeparator**: `=true`
  - **Text**: `="Calidad"`
  - **Width**: `=500`

####### conOportunidadField

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpOportunidadQ1.Y + cmpOportunidadQ1.Height + 10`
  - **Width**: `=Parent.Width`

- **Children**: 2

######## cmpOportunidadQ1

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpOportunidadQ1.ContentHeight`
  - **OnSelectOption1**: `=Set(varOportunidadQ1Selected, 1)`
  - **OnSelectOption2**: `=Set(varOportunidadQ1Selected, 2)`
  - **OnSelectOption3**: `=Set(varOportunidadQ1Selected, 3)`
  - **Option1Text**: `="Los bienes  fueron entregados/ los servicios  fueron prestados en la fecha programada con el proveedor."`
  - **Option2Text**: `="Los bienes NO fueron entregados/ los servicios No fueron prestados en la fecha programada con el proveedor, pero NO se generaron impactos negativos para el Mandante."`
  - **Option3Text**: `="Los bienes NO fueron entregados/ los servicios No fueron prestados en la fecha programada con el proveedor y como consecuencia se generaron impactos negativos para Iel Mandante.  (y/o se incurran en faltas o incumplimientos asociados a los plazos programados de los bienes que de lugar a la apliación de una multa) "`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varOportunidadQ1Selected`
  - **TitleText**: `="Oportunidad: cumplimiento de los plazos programados"`
  - **TotalPoints**: `=30`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpTitleOportunidad.Height + 10`

######## cmpTitleOportunidad

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Height**: `=30`
  - **ShowSeparator**: `=true`
  - **Text**: `="Oportunidad"`
  - **Width**: `=500`

####### conGestionField

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpGestionQ2.Y + cmpGestionQ2.Height + 10`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 3

######## cmpGestionQ2

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpGestionQ2.ContentHeight`
  - **OnSelectOption1**: `=Set(varGestionQ2Selected, 1)`
  - **OnSelectOption2**: `=Set(varGestionQ2Selected, 2)`
  - **OnSelectOption3**: `=Set(varGestionQ2Selected, 3)`
  - **Option1Text**: `="Todas las facturas y sus soportes, la documentación contractual requerida durante la ejecución (informes, certificados, constancias, licencias, etc) y la documentación postcontractual (manuales, protocolos de pruebas, información técnica, etc) fueron entregadas de acuerdo a lo programado y sin errores."`
  - **Option2Text**: `="Algunas facturas y sus soportes, la documentación contractual requerida durante la ejecución (informes, certificados, constancias, licencias, etc) y/o la documentación postcontractual (manuales, protocolos de pruebas, información técnica, etc) fueron entregadas de acuerdo a lo programado pero con errores y como consecuencia se realizó su corrección."`
  - **Option3Text**: `="Algunas facturas y sus soportes, la documentación contractual requerida durante la ejecución (informes, certificados, constancias, licencias, etc) y/o la documentación postcontractual (manuales, protocolos de pruebas, información técnica, etc) NO fueron entregadas de acuerdo con lo programado y con errores.  (y/o se incurran en faltas o incumplimientos asociados a toda la documentación contractual que de lugar a la apliación de una multa) "`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varGestionQ2Selected`
  - **TitleText**: `="Entrega de Documentos Contractuales."`
  - **TotalPoints**: `=10`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpGestionQ1.Y + cmpGestionQ1.Height+ 10`

######## cmpGestionQ1

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpGestionQ1.ContentHeight`
  - **OnSelectOption1**: `=Set(varGestionQ1Selected, 1)`
  - **OnSelectOption2**: `=Set(varGestionQ1Selected, 2)`
  - **OnSelectOption3**: `=Set(varGestionQ1Selected, 3)`
  - **Option1Text**: `="El tiempo de entrega al Mandante de las Pólizas y demás documentos para dar la Orden de Inicio, fue menor o igual al tiempo establecido en el Contrato."`
  - **Option2Text**: `="El tiempo de entrega al Mandante de las Pólizas y demás documentos para dar la Orden de Inicio, fue mayor al tiempo establecido en el Contrato."`
  - **Option3Text**: ``
  - **OptionCount**: `=2`
  - **SelectedIndex**: `=varGestionQ1Selected`
  - **TitleText**: `="Entrega de Documentos para Orden de Inicio."`
  - **TotalPoints**: `=10`
  - **Width**: `=Parent.Width`
  - **Y**: `=cmpTitleGestion.Height + 10`

######## cmpTitleGestion

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Height**: `=30`
  - **ShowSeparator**: `=true`
  - **Text**: `="Gestión"`
  - **Width**: `=500`

####### conObservacionesField

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpObservationTecnico.Height`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 1

######## cmpObservationTecnico

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Default**: `=varObservationTecnico`
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=200`
  - **LabelText**: `="Observation Tecnico"`
  - **Width**: `=Parent.Width`

####### conCodigoEticaField

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpCodigoEtica.Height`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 1

######## cmpCodigoEtica

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpCodigoEtica.ContentHeight`
  - **Label**: `="Código de ética"`
  - **OnSelectNo**: `=Set(varEtica, false)`
  - **OnSelectYes**: `=Set(varEtica, true)`
  - **Question**: `="El contratista incurrió en faltas al código de ética:"`
  - **SelectedValue**: `=varEtica`
  - **Width**: `=Parent.Width`

####### conMAResonsable

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpMAResonsable.Height`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`

- **Children**: 1

######## cmpMAResonsable

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditTecnico && !varTecnicoSubmitted`
  - **Height**: `=cmpMAResonsable.ContentHeight`
  - **Label**: `="Medioambiente"`
  - **OnSelectNo**: `=Set(varMA, false);
Set(varCanEditMA, If(varMASubmitted, false, true));`
  - **OnSelectYes**: `=Set(varMA, true);
Set(varCanEditMA,false)`
  - **Question**: `="Responsable de criterio de medioambiente debe responder:"`
  - **SelectedValue**: `=varMA`
  - **Width**: `=Parent.Width`

##### conSSTSection

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(225, 223, 221, 1)`
  - **BorderThickness**: `=1`
  - **Fill**: `=If(varCanEditSST, RGBA(255, 255, 255, 1),RGBA(240, 240, 240, 1))`
  - **FillPortions**: `=0`
  - **Height**: `=conSSTSectionContent.Height`
  - **PaddingBottom**: `=20`
  - **PaddingLeft**: `=20`
  - **PaddingRight**: `=20`
  - **PaddingTop**: `=20`
  - **RadiusBottomLeft**: `=8`
  - **RadiusBottomRight**: `=8`
  - **RadiusTopLeft**: `=8`
  - **RadiusTopRight**: `=8`
  - **Visible**: `=!IsBlank(varSelectedEvaluation) && (varCanEditSST || varCanApprove) `
  - **Width**: `=Parent.Width`

- **Children**: 1

###### conSSTSectionContent

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=lblSSTHeading.Height + 
conCalificacionSST.Height + conObservacionesSST.Height + 8*4`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=8`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`

- **Children**: 3

####### lblSSTHeading

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Bold`
  - **Height**: `=36`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=20`
  - **Text**: `="SST SECTION"`
  - **Width**: `=Parent.Width - 16`

####### conCalificacionSST

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpSST.Y + cmpSST.Height + 10`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 1

######## cmpSST

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditSST && !varSSTSubmitted`
  - **Height**: `=cmpSST.ContentHeight`
  - **OnSelectOption1**: `=Set(varSSTQ1Selected, 1)`
  - **OnSelectOption2**: `=Set(varSSTQ1Selected, 2)`
  - **OnSelectOption3**: `=Set(varSSTQ1Selected, 3)`
  - **Option1Text**: `="El contratista durante la ejecución del contrato cumplió con los temas administrativos y de terreno de Seguridad y Salud en el Trabajo (carpetas de arranque, procedimientos, MIPER, entrega de información, respuesta de inspecciones en terreno, etc.) en acuerdo a todas las actividades del objeto del contrato y el reglamento especial de empresas contratistas vigente. 
Nota: Si por cualquier causa o motivo se presenta algún accidente grave o mortal aunque el contratista haya cumplido a cavalidad lo antes mencionado su nota total será 0. "`
  - **Option2Text**: `="El contratista durante la ejecución del contrato presento algunos incumplimientos con los temas administrativos y de terreno de Seguridad y Salud en el Trabajo (carpetas de arranque, procedimientos, MIPER, entrega de información, respuesta de inspecciones etc.) en acuerdo a todas las actividades del objeto del contrato y el reglamento especial de empresas contratistas vigente."`
  - **Option3Text**: `="El contratista durante la ejecución del contrato presento incumplimientos reiterativos con temas administrativos y de terreno de Seguridad y Salud en el Trabajo (carpetas de arranque, procedimientos, MIPER, entrega de información, respuesta de inspecciones etc.), o se presentaron accidentes graves o mortales derivados de una deficiente administracion y gestion de los riesgos, y/o se incurrieron en faltas o incumplimientos asociados a medidas de control, procedimientos de trabajos, capacitación, normas y todo lo inherente a la seguridad y salud en el trabajo que de lugar a la apliación de una multa en acuerdo a todas las actividades del objeto del contrato y el reglamento especial de empresas contratistas vigente."`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varSSTQ1Selected`
  - **TitleText**: `="Requisitos en Salud Ocupacional"`
  - **TotalPoints**: `=10`
  - **Width**: `=Parent.Width`
  - **Y**: `=10`

####### conObservacionesSST

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpObservationSST.Height`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 1

######## cmpObservationSST

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Default**: `=varObservationSST`
  - **Enable**: `=varCanEditSST && !varSSTSubmitted`
  - **Height**: `=200`
  - **LabelText**: `="Observation SST"`
  - **Width**: `=Parent.Width`

##### conMASection

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(225, 223, 221, 1)`
  - **BorderThickness**: `=1`
  - **Fill**: `=If(varCanEditMA, RGBA(255, 255, 255, 1),RGBA(240, 240, 240, 1))`
  - **FillPortions**: `=0`
  - **Height**: `=conMASectionContent.Height`
  - **PaddingBottom**: `=20`
  - **PaddingLeft**: `=20`
  - **PaddingRight**: `=20`
  - **PaddingTop**: `=20`
  - **RadiusBottomLeft**: `=8`
  - **RadiusBottomRight**: `=8`
  - **RadiusTopLeft**: `=8`
  - **RadiusTopRight**: `=8`
  - **Visible**: `=!IsBlank(varSelectedEvaluation) && (varCanEditMA || varCanApprove)`
  - **Width**: `=Parent.Width`

- **Children**: 1

###### conMASectionContent

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Fill**: `=RGBA(0,0,0,0)`
  - **Height**: `=lblMAHeading.Height + conCalificacionMA.Height + conObservacionesMA.Height + 8*4`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=8`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`

- **Children**: 3

####### lblMAHeading

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Bold`
  - **Height**: `=36`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=20`
  - **Text**: `="MA SECTION"`
  - **Width**: `=Parent.Width - 16`

####### conCalificacionMA

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpMA.Y + cmpMA.Height + 10`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 1

######## cmpMA

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Enable**: `=varCanEditMA && !varMASubmitted`
  - **Height**: `=cmpMA.ContentHeight`
  - **OnSelectOption1**: `=Set(varCalidadQ1Selected, 1)`
  - **OnSelectOption2**: `=Set(varCalidadQ1Selected, 2)`
  - **OnSelectOption3**: `=Set(varCalidadQ1Selected, 3)`
  - **Option1Text**: `="El contratista cumple con todas las exigencias MedioAmnbientales Exigidas por el Mandante."`
  - **Option2Text**: `="El contratista presento incumplimientos de las Exigencias Medio Ambientales, durante la ejecucion del contrato. Corrigio y gestiono adecuadamente y oportunamente los Impactos Ambientales"`
  - **Option3Text**: `="El contratista presento incumplimientos de las Exigencias Medio Ambientales. Generando impactos ambientales significativos y consecuencias para el Mandante como multas, sanciones, afectación de reputación, entre otras."`
  - **OptionCount**: `=3`
  - **SelectedIndex**: `=varCalidadQ1Selected`
  - **TitleText**: `="Requisitos Ambientales"`
  - **TotalPoints**: `=10`
  - **Width**: `=Parent.Width`
  - **Y**: `=10`

####### conObservacionesMA

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpObservationMA.Height`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 1

######## cmpObservationMA

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Default**: `=varObservationMA`
  - **Enable**: `=varCanEditMA && !varMASubmitted`
  - **Height**: `=200`
  - **LabelText**: `="Observation MA"`
  - **Width**: `=Parent.Width`

##### conJefeArea

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(225, 223, 221, 1)`
  - **BorderThickness**: `=1`
  - **Fill**: `=If(varCanApprove, RGBA(255, 255, 255, 1),RGBA(240, 240, 240, 1))`
  - **FillPortions**: `=0`
  - **Height**: `=conJefeAreaSectionContent.Height`
  - **PaddingBottom**: `=20`
  - **PaddingLeft**: `=20`
  - **PaddingRight**: `=20`
  - **PaddingTop**: `=20`
  - **RadiusBottomLeft**: `=8`
  - **RadiusBottomRight**: `=8`
  - **RadiusTopLeft**: `=8`
  - **RadiusTopRight**: `=8`
  - **Visible**: `=!IsBlank(varSelectedEvaluation)`
  - **Width**: `=Parent.Width`

- **Children**: 1

###### conJefeAreaSectionContent

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Fill**: `=RGBA(0,0,0,0)`
  - **Height**: `=lblJefeAreaHeading.Height + conObservacionesJefeArea.Height + 8*3`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=8`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`

- **Children**: 2

####### lblJefeAreaHeading

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Bold`
  - **Height**: `=36`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=20`
  - **Text**: `="JEFE AREA SECTION"`
  - **Width**: `=Parent.Width - 16`

####### conObservacionesJefeArea

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=cmpObservationJefeArea.Height`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Width**: `=Parent.Width`

- **Children**: 1

######## cmpObservationJefeArea

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Default**: `=varObservationJefeArea`
  - **Enable**: `=varCanApprove && !varAprobada`
  - **Height**: `=200`
  - **LabelText**: `="Observation Jefe Area"`
  - **Width**: `=Parent.Width`

##### conResumen

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=0`
  - **Height**: `=288`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Visible**: `=!IsBlank(varSelectedEvaluation)`
  - **Width**: `=Parent.Width`

- **Children**: 2

###### conResumenMain

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=240`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`
  - **Y**: `=lblResumenTitle.Y + lblResumenTitle.Height + 8`

- **Children**: 2

####### conCriteriaScores

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **Height**: `=100`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Horizontal`
  - **LayoutGap**: `=48`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`

- **Children**: 6

######## cntCalidadScore

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 2

######### lblScoreTitle

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Text**: `="Calidad"`
  - **Width**: `=Parent.Width`

######### lblScoreDetails

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=16`
  - **Text**: `=If(
    cmpCalidadQ1.SelectedIndex = 0 ||
    cmpCalidadQ2.SelectedIndex = 0 ||
    cmpCalidadQ3.SelectedIndex = 0 ||
    cmpCalidadQ4.SelectedIndex = 0 ||
    cmpCalidadQ5.SelectedIndex = 0,
    "-",
    Text(cmpCalidadQ1.Score + cmpCalidadQ2.Score + cmpCalidadQ3.Score + cmpCalidadQ4.Score + cmpCalidadQ5.Score)
) & " / 30"`
  - **Width**: `=Parent.Width`

######## cntOportunidadScore

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 2

######### lblScoreTitle_1

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1.2`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Text**: `="Oportunidad"`
  - **Width**: `=Parent.Width`

######### lblScoreDetails_1

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=16`
  - **Text**: `=If(
    cmpOportunidadQ1.SelectedIndex = 0,
    "-",
    Text(cmpOportunidadQ1.Score)
) & " / 30"
`
  - **Width**: `=Parent.Width`

######## cntGestionScore

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 2

######### lblScoreTitle_2

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Text**: `="Gestión"`
  - **Width**: `=Parent.Width`

######### lblScoreDetails_2

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=16`
  - **Text**: `=If(
    cmpGestionQ1.SelectedIndex = 0 || cmpGestionQ2.SelectedIndex = 0,
    "-",
    Text(cmpGestionQ1.Score + cmpGestionQ2.Score)
) & " / 20"`
  - **Width**: `=Parent.Width`

######## cntSSTScore

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 2

######### lblScoreTitle_3

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Text**: `="SST"`
  - **Width**: `=Parent.Width`

######### lblScoreDetails_3

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=16`
  - **Text**: `=If(
    cmpSST.SelectedIndex = 0,
    "-",
    Text(cmpSST.Score)
) & " / 10"
`
  - **Width**: `=Parent.Width`

######## cntMAScore

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=1.2
`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 2

######### lblScoreTitle_4

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Text**: `="Medioambiente"`
  - **Width**: `=Parent.Width`

######### lblScoreDetails_4

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=16`
  - **Text**: `=If(
    cmpMA.SelectedIndex = 0,
    "-",
    Text(cmpMA.Score)
) & " / 10"`
  - **Width**: `=Parent.Width`

######## cntEticaScore

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=1.2
`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 2

######### lblScoreTitle_5

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Text**: `="Código de ética"`
  - **Width**: `=Parent.Width`

######### lblScoreDetails_5

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=16`
  - **Text**: `=If(varEtica, "Yes", "No")`
  - **Width**: `=Parent.Width`

####### conSummaryScores

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=1.2`
  - **Height**: `=100`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Horizontal`
  - **LayoutGap**: `=8`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`

- **Children**: 3

######## cntPlaceHolder

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **FillPortions**: `=3`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`

######## cntSubtotal

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 3

######### lblSubtotal

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=16`
  - **Text**: `="Subtotal"`
  - **Width**: `=Parent.Width`

######### lblSubtotalValue

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=20`
  - **Text**: `=If(
    cmpCalidadQ1.SelectedIndex = 0 ||
    cmpCalidadQ2.SelectedIndex = 0 ||
    cmpCalidadQ3.SelectedIndex = 0 ||
    cmpCalidadQ4.SelectedIndex = 0 ||
    cmpCalidadQ5.SelectedIndex = 0 ||
    cmpOportunidadQ1.SelectedIndex = 0 ||
    cmpGestionQ1.SelectedIndex = 0 ||
    cmpGestionQ2.SelectedIndex = 0 ||
    cmpSST.SelectedIndex = 0 ||
    cmpMA.SelectedIndex = 0,
    "-",
    Text(cmpCalidadQ1.Score + cmpCalidadQ2.Score + cmpCalidadQ3.Score + cmpCalidadQ4.Score + cmpCalidadQ5.Score + cmpOportunidadQ1.Score + cmpGestionQ1.Score + cmpGestionQ2.Score + cmpSST.Score + cmpMA.Score)
)`
  - **Visible**: `=false`
  - **Width**: `=Parent.Width`

######### lblSubtotalValueDisplay

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Semibold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=20`
  - **Text**: `=lblSubtotalValue.Text & " / 100"`
  - **Width**: `=Parent.Width`

######## cntTotal

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Vertical`
  - **LayoutGap**: `=4`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=110`

- **Children**: 3

######### lblTotal

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Bold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=24`
  - **Text**: `="Total"`
  - **Width**: `=Parent.Width`

######### lblTotalValue

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Bold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=24`
  - **Text**: `=If(varEtica = true, "0", lblSubtotalValue.Text)`
  - **Visible**: `=false`
  - **Width**: `=Parent.Width`

######### lblTotalValueDisplay

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **Align**: `=Align.Center`
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **FillPortions**: `=1`
  - **Font**: `=Font.'Open Sans'`
  - **FontWeight**: `=FontWeight.Bold`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **Size**: `=24`
  - **Text**: `=lblTotalValue.Text & " / 100"`
  - **Width**: `=Parent.Width`

###### lblResumenTitle

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Color**: `=ColorValue("#003087")`
  - **Font**: `=Font.'Segoe UI'`
  - **FontWeight**: `=FontWeight.Bold`
  - **Size**: `=28`
  - **Text**: `="Resumen"`
  - **Width**: `=Parent.Width`

##### lblBottomSpacer

- **Control**: `Label@2.5.1`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(200, 200, 200, 1)`
  - **Font**: `=Font.'Open Sans'`
  - **Height**: `=20`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=20`
  - **PaddingLeft**: `=20`
  - **PaddingRight**: `=20`
  - **PaddingTop**: `=20`
  - **Text**: `=""`
  - **Width**: `=Parent.Width`
### conHeader_1

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `ManualLayout`

- **Properties (explicitly set)**:
  - **Fill**: `=ColorValue("#003087")`
  - **Height**: `=100`
  - **Width**: `=Parent.Width`

- **Children**: 1

#### conHeaderContent_1

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **Height**: `=Parent.Height`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Horizontal`
  - **LayoutGap**: `=20`
  - **LayoutJustifyContent**: `=LayoutJustifyContent.SpaceBetween`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=40`
  - **PaddingRight**: `=40`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width`

- **Children**: 2

##### imgLogo_1

- **Control**: `Image@2.2.3`

- **Properties (explicitly set)**:
  - **BorderColor**: `=RGBA(0, 18, 107, 1)`
  - **Height**: `=150`
  - **Image**: `=logo2`
  - **Width**: `=150`
  - **X**: `=60`

##### conMenuGroup_1

- **Control**: `GroupContainer@1.3.0`
- **Variant**: `AutoLayout`

- **Properties (explicitly set)**:
  - **DropShadow**: `=DropShadow.None`
  - **LayoutAlignItems**: `=LayoutAlignItems.Center`
  - **LayoutDirection**: `=LayoutDirection.Horizontal`
  - **LayoutGap**: `=40`
  - **LayoutJustifyContent**: `=LayoutJustifyContent.End`
  - **LayoutMaxHeight**: `=0`
  - **LayoutMaxWidth**: `=0`
  - **LayoutMinHeight**: `=16`
  - **LayoutMinWidth**: `=16`
  - **PaddingBottom**: `=8`
  - **PaddingLeft**: `=8`
  - **PaddingRight**: `=8`
  - **PaddingTop**: `=8`
  - **Width**: `=Parent.Width - imgLogo_1.Width`

- **Children**: 3

###### btnEvaluateProviders_1

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Destination**: `=scrEvaluations`
  - **Fill**: `=Color.Transparent`
  - **Height**: `=80`
  - **IsActive**: `=App.ActiveScreen = scrEvaluations`
  - **Label**: `="Evaluacions"`
  - **Width**: `=180`

###### btnSignLetters_1

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Destination**: `=scrFirmarCartas`
  - **Fill**: `=Color.Transparent`
  - **Height**: `=80`
  - **IsActive**: `=App.ActiveScreen = scrFirmarCartas`
  - **Label**: `="Firmar Cartas"`
  - **Width**: `=180`

###### btnDashboard_1

- **Control**: `CanvasComponent`

- **Properties (explicitly set)**:
  - **Destination**: `=scrDashboard`
  - **Fill**: `=Color.Transparent`
  - **Height**: `=80`
  - **IsActive**: `=App.ActiveScreen = scrDashboard`
  - **Label**: `="Dashboard"`
  - **Width**: `=180`