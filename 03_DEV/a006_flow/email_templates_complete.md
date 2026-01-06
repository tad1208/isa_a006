# Power Automate Flow - Email Templates Complete

## 📋 Tổng quan 3 Nhánh Email

| Nhánh | Trigger | Người nhận | Mục đích |
|-------|---------|------------|----------|
| **A** | All Submitted = true | Jefe de Área | Thông báo evaluation ready for approval |
| **B** | Rechazada = true | Técnico, SST, MA | Thông báo evaluation bị rejected |
| **C** | MA Responsible assigned | Responsable MA | Thông báo được assign làm MA evaluator |

---

## 🔵 NHÁNH A: All Submitted → Jefe de Área

### ✅ Condition Expression

**Ô trái (điều kiện 1):**
```
or(or(body('Get_changes_for_an_item_or_a_file_(properties_only)')?['HasColumn_tecnico_submitted_Changed'],body('Get_changes_for_an_item_or_a_file_(properties_only)')?['HasColumn_sst_submitted_Changed']),body('Get_changes_for_an_item_or_a_file_(properties_only)')?['HasColumn_ma_submitted_Changed'])
```

**Operator:** `is equal to`

**Ô phải:**
```
true
```

**Add row 2 (AND):**

**Ô trái:**
```
triggerOutputs()?['body/tecnico_submitted']
```

**Operator:** `is equal to`

**Ô phải:**
```
true
```

**Add row 3 (AND):**

**Ô trái:**
```
triggerOutputs()?['body/sst_submitted']
```

**Operator:** `is equal to`

**Ô phải:**
```
true
```

**Add row 4 (AND):**

**Ô trái:**
```
triggerOutputs()?['body/ma_submitted']
```

**Operator:** `is equal to`

**Ô phải:**
```
true
```

**Add row 5 (AND) - Optional anti-spam:**

**Ô trái:**
```
triggerOutputs()?['body/aprobada']
```

**Operator:** `is equal to`

**Ô phải:**
```
false
```

**Add row 6 (AND) - Optional anti-spam:**

**Ô trái:**
```
triggerOutputs()?['body/rechazada']
```

**Operator:** `is equal to`

**Ô phải:**
```
false
```

---

### 📧 Email Configuration - Approver

**To:**
```
@{triggerOutputs()?['body/email_jefe_area']}
```

**Subject:**
```
🔔 A006 - Evaluación Lista para Aprobación - Contract @{triggerOutputs()?['body/documento_compras']}
```

**Body (HTML):**

```html
<p><strong>Estimado/a @{triggerOutputs()?['body/nombre_jefe_area']},</strong></p>

<p>Le informamos que la evaluación del proveedor ha sido <strong>completada por todos los evaluadores</strong> y está lista para su revisión y aprobación.</p>

<h3 style="color: #003087;">📋 INFORMACIÓN DE LA EVALUACIÓN:</h3>

<table style="border-collapse: collapse; width: 100%; max-width: 600px; margin: 20px 0;">
    <tr style="background-color: #f2f2f2;">
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Proveedor</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{triggerOutputs()?['body/nombre_proveedor']}</td>
    </tr>
    <tr>
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Documento</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{triggerOutputs()?['body/documento_compras']}</td>
    </tr>
    <tr style="background-color: #f2f2f2;">
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Fecha Evaluación</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{formatDateTime(triggerOutputs()?['body/fecha_evaluacion'], 'dd/MM/yyyy')}</td>
    </tr>
    <tr>
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Puntaje Total</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>@{triggerOutputs()?['body/puntaje_total']}</strong> / 100</td>
    </tr>
</table>

<h3 style="color: #003087;">👥 EVALUADORES QUE HAN COMPLETADO:</h3>

<ul>
    <li><strong>Responsable Técnico:</strong> @{triggerOutputs()?['body/nombre_responsable_tecnico']}</li>
    <li><strong>Responsable SST:</strong> @{triggerOutputs()?['body/nombre_responsable_sst']}</li>
    <li><strong>Responsable MA:</strong> @{triggerOutputs()?['body/nombre_responsable_ma']}</li>
</ul>

<h3 style="color: #28a745;">✅ ACCIÓN REQUERIDA:</h3>

<p>Por favor, acceda a la aplicación de <strong>Evaluación de Proveedores</strong> para:</p>

<ul>
    <li>Revisar los criterios evaluados</li>
    <li>Verificar las observaciones de los evaluadores</li>
    <li>Proceder con la <strong>aprobación</strong> o <strong>rechazo</strong> según corresponda</li>
</ul>
```

---

## 🔴 NHÁNH B: Rechazada → Técnico, SST, MA

### ✅ Condition Expression

**Simple mode (2 rows):**

**Row 1:**
- Ô trái: `body('Get_changes_for_an_item_or_a_file_(properties_only)')?['HasColumn_rechazada_Changed']`
- Operator: `is equal to`
- Ô phải: `true`

**Row 2 (AND):**
- Ô trái: `triggerOutputs()?['body/rechazada']`
- Operator: `is equal to`
- Ô phải: `true`

---

### 📧 Email Configuration - Rejection

**To:**
```
@{triggerOutputs()?['body/email_responsable_tecnico']};@{triggerOutputs()?['body/email_responsable_sst']};@{triggerOutputs()?['body/email_responsable_ma']}
```

**Subject:**
```
⚠️ A006 - Evaluación Rechazada - Corrección Requerida - Contract @{triggerOutputs()?['body/documento_compras']}
```

**Body (HTML):**

```html
<p><strong>Estimado/a,</strong></p>

<p>Le informamos que la evaluación del proveedor ha sido <strong style="color: #d9534f;">RECHAZADA</strong> por el Jefe de Área.</p>

<h3 style="color: #003087;">📋 INFORMACIÓN DE LA EVALUACIÓN:</h3>

<table style="border-collapse: collapse; width: 100%; max-width: 600px; margin: 20px 0;">
    <tr style="background-color: #f2f2f2;">
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Proveedor</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{triggerOutputs()?['body/nombre_proveedor']}</td>
    </tr>
    <tr>
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Documento</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{triggerOutputs()?['body/documento_compras']}</td>
    </tr>
    <tr style="background-color: #f2f2f2;">
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Fecha Evaluación</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{formatDateTime(triggerOutputs()?['body/fecha_evaluacion'], 'dd/MM/yyyy')}</td>
    </tr>
</table>

<h3 style="color: #d9534f;">⚠️ OBSERVACIONES DEL JEFE DE ÁREA:</h3>

<p style="background-color: #f9f9f9; padding: 15px; border-left: 4px solid #d9534f;">
    @{triggerOutputs()?['body/observaciones_jefe_area']}
</p>

<h3 style="color: #d9534f;">📝 ACCIÓN REQUERIDA:</h3>

<p>Por favor:</p>
<ul>
    <li>Revise las observaciones del Jefe de Área</li>
    <li>Realice las correcciones necesarias en su evaluación</li>
    <li>Vuelva a enviar la evaluación cuando esté lista</li>
</ul>

<p><strong>Nota:</strong> El sistema ha restablecido los flags de envío. Deberá completar y enviar nuevamente su parte de la evaluación.</p>
```

---

## 🟢 NHÁNH C: MA Assigned → Responsable MA

### ✅ Condition Expression

**Simple mode (2 rows):**

**Row 1:**
- Ô trái: `body('Get_changes_for_an_item_or_a_file_(properties_only)')?['HasColumn_responsable_ma_responde_Changed']`
- Operator: `is equal to`
- Ô phải: `true`

**Row 2 (AND):**
- Ô trái: `triggerOutputs()?['body/responsable_ma_responde']`
- Operator: `is equal to`
- Ô phải: `true`

---

### 📧 Email Configuration - MA Assignment

**To:**
```
@{triggerOutputs()?['body/email_responsable_ma']}
```

**Subject:**
```
✅ A006 - Asignado como Responsable MA - Contract @{triggerOutputs()?['body/documento_compras']}
```

**Body (HTML):**

```html
<p><strong>Estimado/a @{triggerOutputs()?['body/nombre_responsable_ma']},</strong></p>

<p>Le informamos que usted ha sido asignado como <strong>Responsable de Medio Ambiente</strong> para la siguiente evaluación de proveedor.</p>

<h3 style="color: #003087;">📋 INFORMACIÓN DE LA EVALUACIÓN:</h3>

<table style="border-collapse: collapse; width: 100%; max-width: 600px; margin: 20px 0;">
    <tr style="background-color: #f2f2f2;">
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Proveedor</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{triggerOutputs()?['body/nombre_proveedor']}</td>
    </tr>
    <tr>
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Documento</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{triggerOutputs()?['body/documento_compras']}</td>
    </tr>
    <tr style="background-color: #f2f2f2;">
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Fecha Evaluación</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{formatDateTime(triggerOutputs()?['body/fecha_evaluacion'], 'dd/MM/yyyy')}</td>
    </tr>
    <tr>
        <td style="padding: 10px; border: 1px solid #ddd;"><strong>Sociedad</strong></td>
        <td style="padding: 10px; border: 1px solid #ddd;">@{triggerOutputs()?['body/sociedad']}</td>
    </tr>
</table>

<h3 style="color: #28a745;">✅ ACCIÓN REQUERIDA:</h3>

<p>Por favor, acceda a la aplicación de <strong>Evaluación de Proveedores</strong> para completar la evaluación del criterio de <strong>Medio Ambiente</strong>.</p>

<p style="background-color: #e7f3e7; padding: 15px; border-left: 4px solid #28a745;">
    <strong>Criterio a evaluar:</strong> Medio Ambiente (MA)<br>
    <strong>Puntaje máximo:</strong> 10 puntos
</p>

<p>Su evaluación es esencial para asegurar que nuestros proveedores mantengan los estándares ambientales de ISA INTERVIAL.</p>
```

---

## 📝 Notas de Implementación

### Orden de Conditions (If-Else If-Else)

Theo cấu trúc nested:

1. **Condition 1**: Check Nhánh B (Reject)
   - If Yes → Send email rejection
   - If No → Continue to Condition 2

2. **Condition 2** (nested trong "If No" của Condition 1): Check Nhánh A (All Submitted)
   - If Yes → Send email to approver
   - If No → Continue to Condition 3

3. **Condition 3** (nested trong "If No" của Condition 2): Check Nhánh C (MA Assigned)
   - If Yes → Send email to MA
   - If No → End flow

### Variables cần kiểm tra tên chính xác

Đảm bảo tên columns trong SharePoint list:
- `rechazada` (hoặc `rechaza`)
- `tecnico_submitted`
- `sst_submitted`
- `ma_submitted`
- `responsable_ma_responde` (hoặc `ma_responsible`)
- `aprobada`

### Testing Checklist

- [ ] Test Nhánh A: Set cả 3 submitted = true
- [ ] Test Nhánh B: Set rechazada = true
- [ ] Test Nhánh C: Set responsable_ma_responde = true
- [ ] Verify email formatting trong Outlook
- [ ] Check dynamic content hiển thị đúng
