# UiPath + DocuSign Integration Service - Implementation Guide

## Tổng quan

Hướng dẫn này mô tả chi tiết cách tích hợp DocuSign vào Robot 2 của dự án A006 để:
1. Tạo envelope DocuSign với PDF letter đánh giá
2. Gửi đến 2 người ký: Responsable Técnico và Jefe de Área
3. Monitor trạng thái ký
4. Download signed PDF khi hoàn tất
5. Upload lên SharePoint

---

## 📋 Prerequisites

### 1. DocuSign Account
- **Account Type**: Business Pro hoặc cao hơn
- **Admin Access**: Cần quyền admin để tạo Integration Key
- **Số lượng users**: Ít nhất 2 (hoặc unlimited)

### 2. UiPath Licenses
- **UiPath Studio**: Pro hoặc Enterprise
- **Integration Service**: License active
- **Orchestrator**: Cloud hoặc On-premise

### 3. Thông tin cần có
- DocuSign Account ID
- DocuSign Integration Key (Client ID)
- DocuSign Secret Key
- Email tài khoản DocuSign admin

---

## 🔧 PHASE 1: Setup DocuSign Integration Service

### Step 1.1: Get DocuSign Credentials

#### 1.1.1 Lấy Account ID

1. Login vào DocuSign: https://account.docusign.com
2. Click vào **avatar** (góc phải trên)
3. Chọn **Admin** (nếu bạn là admin)
4. Hoặc: Settings → **API and Keys**
5. Copy **Account ID** (format: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`)

#### 1.1.2 Tạo Integration Key

1. Trong trang **API and Keys**
2. Click **Add App and Integration Key**
3. Điền thông tin:
   - **App Name**: `A006 UiPath Integration`
   - **Description**: `Integration for A006 Provider Evaluation Letter Signing`
4. Click **Create App**
5. Copy **Integration Key** (Client ID)
6. Click **Add Secret Key** → Copy **Secret Key** ngay (chỉ hiện 1 lần!)
7. Trong **Redirect URIs**, thêm:
   ```
   https://cloud.uipath.com/portal_/integrationservice/redirect
   ```
   (hoặc URL của Orchestrator nếu on-premise)

#### 1.1.3 Grant Consent (Quan trọng!)

1. Trong trang App details
2. Click **Generate URI** dưới phần **Grant Scopes**
3. Scopes cần chọn:
   - ✅ `signature` - Create and send envelopes
   - ✅ `impersonation` - For API calls
4. Copy URI được generate
5. Paste vào browser → Login → Click **Allow Access**
6. **QUAN TRỌNG**: Phải làm bước này, nếu không API calls sẽ bị lỗi 401

---

### Step 1.2: Configure trong UiPath Orchestrator

#### 1.2.1 Add DocuSign Connector

1. Login vào **UiPath Orchestrator**
2. Menu: **Admin** → **Integration Service** → **Connectors**
3. Click **Configure Connector**
4. Tìm và chọn **DocuSign**
5. Click **Configure**

#### 1.2.2 Fill in Connection Details

```yaml
Connection Name: DocuSign_A006
Description: DocuSign connector for A006 evaluation letters

Authentication Type: OAuth 2.0 Authorization Code

Configuration:
  Account ID: [Paste Account ID từ step 1.1.1]
  Integration Key: [Paste Integration Key từ step 1.1.2]
  Secret Key: [Paste Secret Key từ step 1.1.2]
  Admin Email: [Email của DocuSign admin account]
  
Environment:
  ☑ Production  (hoặc ☐ Demo nếu đang test)
```

6. Click **Save**
7. Click **Test Connection** → Should return "Success"

---

### Step 1.3: Grant Orchestrator Access

1. Trong Orchestrator, sau khi save connector
2. Click **Grant Access** button
3. Browser sẽ mở trang DocuSign login
4. Login với DocuSign admin account
5. Click **Allow** để authorize UiPath
6. Browser redirect về Orchestrator
7. Status connector should be: **✅ Connected**

---

## 📦 PHASE 2: Setup UiPath Project

### Step 2.1: Install Activity Package

1. Mở **UiPath Studio**
2. Open project **Robot 2** (hoặc tạo project mới để test)
3. **Manage Packages** → **Official**
4. Search: `UiPath.DocuSign.Activities`
5. Install latest version (e.g., v1.x.x)
6. Accept dependencies

### Step 2.2: Configure Connection in Studio

#### 2.2.1 Add Integration Service Connection

1. Trong Studio, mở panel **Integration Service**
   - Menu: **Design** → **Integration Service** (hoặc Ctrl+Alt+I)
2. Click **Authenticate**
3. Login với Orchestrator credentials
4. Select **Tenant** và **Folder** chứa connector
5. Connector **DocuSign_A006** sẽ hiện trong list

#### 2.2.2 Test Connection trong Studio

1. Drag activity **Get Envelopes** vào workflow (để test)
2. Properties:
   - **Connection**: Chọn `DocuSign_A006` từ dropdown
   - Không cần config gì thêm
3. Run workflow → Xem Output panel
   - Nếu thành công: Không có error
   - Nếu lỗi 401: Quay lại step 1.1.3 (Grant Consent)

---

## 🏗️ PHASE 3: Implement Workflow

### Workflow Overview

```
Main Workflow
├── Generate PDF Letter (existing logic)
├── Create DocuSign Envelope
│   ├── Create Envelope Using Template (or Create Envelope)
│   ├── Add Document to Envelope
│   ├── Add Recipient - Técnico
│   ├── Add Recipient - Jefe
│   └── Send Envelope
├── Update SharePoint (envelope_id, sent_date)
└── Monitor Signing Status (separate workflow or same)
    ├── Get Envelope
    ├── Check Status
    └── If "completed" → Download and Upload
```

---

### Step 3.1: Create Envelope Sequence

Tạo một **Sequence** mới tên `CreateDocuSignEnvelope.xaml`

#### Variables cần có:

```vb
Name                        Type            Scope       Default
─────────────────────────────────────────────────────────────
in_PDFFilePath              String          Sequence    (argument in)
in_NombreProveedor          String          Sequence    (argument in)
in_DocumentoCompras         String          Sequence    (argument in)
in_EmailTecnico             String          Sequence    (argument in)
in_NombreTecnico            String          Sequence    (argument in)
in_EmailJefe                String          Sequence    (argument in)
in_NombreJefe               String          Sequence    (argument in)
out_EnvelopeID              String          Sequence    (argument out)
var_Envelope                DocuSign.Envelope  Sequence
var_DocumentResource        IResource       Sequence
var_EmailSubject            String          Sequence
var_EmailMessage            String          Sequence
```

---

#### Activity 1: Build Email Subject & Message

**Assign** activity:

```vb
var_EmailSubject = String.Format("Firma Requerida - Carta de Evaluación - {0}", in_NombreProveedor)

var_EmailMessage = String.Format("Estimado/a, por favor firme la carta de evaluación para el proveedor {0} (Contrato {1}). Acceda al enlace abajo para firmar el documento.", in_NombreProveedor, in_DocumentoCompras)
```

---

#### Activity 2: Create Envelope

**Activity**: `Create Envelope` (UiPath.DocuSign.Activities)

**Properties**:
```yaml
Connection: DocuSign_A006

Email Subject: var_EmailSubject
Email Message: var_EmailMessage

Output:
  Envelope: var_Envelope
```

**Lấy Envelope ID** (ngay sau Create Envelope):

**Assign**:
```vb
out_EnvelopeID = var_Envelope.EnvelopeId
```

---

#### Activity 3: Add Document to Envelope

**Prepare Document Resource** (Assign trước):

```vb
var_DocumentResource = New FileResource(in_PDFFilePath)
```

**Activity**: `Add Document to Envelope`

**Properties**:
```yaml
Connection: DocuSign_A006

Envelope Id: out_EnvelopeID

Document:
  Document: var_DocumentResource
  Document Id: "1"
  Document Name: String.Format("Carta_Evaluacion_{0}.pdf", in_DocumentoCompras)
  File Extension: "pdf"
```

**⚠️ Note**: Nếu bạn đã có template trong DocuSign với PDF sẵn, có thể skip step này.

---

#### Activity 4: Add Recipient - Responsable Técnico

**Activity**: `Add Recipient to Envelope`

**Properties**:
```yaml
Connection: DocuSign_A006

Envelope Id: out_EnvelopeID

Recipient:
  Recipient Type: Signer
  
Signer Details:
  Email: in_EmailTecnico
  Name: in_NombreTecnico
  Recipient Id: "1"
  Routing Order: 1
  
Email Notification:
  ☑ Send Email Notification
  Email Subject: (optional override)
  Email Body: (optional override)
```

**Advanced Settings** (Optional):
- **Tabs**: Nếu cần add signature fields dynamically
  - Position X, Y
  - Page Number
  - Tab Type: "SignHere", "DateSigned"

---

#### Activity 5: Add Recipient - Jefe de Área

**Activity**: `Add Recipient to Envelope` (lần 2)

**Properties**: Giống activity 4 nhưng:
```yaml
Signer Details:
  Email: in_EmailJefe
  Name: in_NombreJefe
  Recipient Id: "2"
  Routing Order: 2      ← Ký SAU Técnico
```

---

#### Activity 6: Send Envelope

**Activity**: `Update Envelope Status`

**Properties**:
```yaml
Connection: DocuSign_A006

Envelope Id: out_EnvelopeID
Status: "sent"

Output:
  Envelope: var_Envelope (update)
```

**Kết quả**: Email được gửi đến 2 recipients. Técnico nhận trước, Jefe nhận sau khi Técnico ký xong.

---

### Step 3.2: Update SharePoint After Sending

Trong **Main.xaml** (hoặc Process.xaml của REFramework):

```vb
' After CreateDocuSignEnvelope sequence
SharePoint_UpdateItem(
    ItemID: in_TransactionItem("ID").ToString,
    FieldUpdates: New Dictionary(Of String, Object) From {
        {"docusign_envelope_id", out_EnvelopeID},
        {"envelope_sent_date", Now.ToString("yyyy-MM-dd HH:mm:ss")},
        {"envelope_sent", True}
    }
)
```

**Columns cần add vào SharePoint list** (nếu chưa có):
- `docusign_envelope_id` (Single line text)
- `envelope_sent_date` (Date and time)
- `envelope_sent` (Yes/No)

---

## 🔍 PHASE 4: Monitor Signing Status

### Option A: Separate Scheduled Workflow (RECOMMENDED)

Tạo workflow mới: `MonitorDocuSignEnvelopes.xaml`

#### Workflow Structure:

```
Main
├── Get SharePoint Items
│   Filter: envelope_sent = true AND url_carta_firmada is empty
│
└── For Each Item in Items
    ├── Get Envelope Status
    ├── If Status = "completed"
    │   ├── Download Signed Document
    │   ├── Upload to SharePoint Document Library
    │   └── Update SharePoint Item (flags)
    └── If Status = "declined" or "voided"
        └── Log and notify admin
```

---

#### Activity: Get Envelope

**Activity**: `Get Envelope`

**Properties**:
```yaml
Connection: DocuSign_A006

Envelope Id: currentItem("docusign_envelope_id").ToString

Output:
  Envelope: var_Envelope
```

---

#### Check Status

**If** activity:

**Condition**:
```vb
var_Envelope.Status = "completed"
```

**Then branch**:

---

#### Activity: Download Signed Document

**Activity**: `Get Envelope Document`

**Properties**:
```yaml
Connection: DocuSign_A006

Envelope Id: var_Envelope.EnvelopeId
Document Id: "combined"    ← Downloads all documents merged

Output:
  Document Bytes: var_DocumentBytes (Byte array)
```

**Save to local file**:

**Assign**:
```vb
var_SignedPDFPath = Path.Combine(Config("OutputFolder"), String.Format("Carta_Firmada_{0}_{1}.pdf", currentItem("documento_compras").ToString, DateTime.Now.ToString("yyyyMMdd_HHmmss")))
```

**Invoke Code** (or WriteAllBytes):
```vb
File.WriteAllBytes(var_SignedPDFPath, var_DocumentBytes)
```

---

#### Upload to SharePoint

**Activity**: `Upload File` (SharePoint activities)

**Properties**:
```yaml
Site URL: Config("SharePoint_SiteURL")
Document Library: "Cartas Firmadas"
Local File Path: var_SignedPDFPath
File Name: Path.GetFileName(var_SignedPDFPath)

Output:
  File URL: var_UploadedFileURL
```

---

#### Update SharePoint Item

```vb
SharePoint_UpdateItem(
    ItemID: currentItem("ID").ToString,
    FieldUpdates: New Dictionary(Of String, Object) From {
        {"url_carta_firmada", var_UploadedFileURL},
        {"firma_responsable_tecnico", True},
        {"firma_jefe_area", True},
        {"fecha_firma_completada", Now.ToString("yyyy-MM-dd HH:mm:ss")}
    }
)
```

---

#### Schedule trong Orchestrator

1. Publish workflow **MonitorDocuSignEnvelopes** to Orchestrator
2. Create **Trigger**:
   - Type: **Time Trigger**
   - Schedule: Every **30 minutes** (hoặc 1 hour)
   - Active hours: 24/7 hoặc business hours only
3. Assign to **Robot**

---

### Option B: Long-Running Workflow (Advanced)

Thêm vào **CreateDocuSignEnvelope** sequence:

```vb
Do While var_Envelope.Status <> "completed" And 
         var_Envelope.Status <> "declined" And
         var_Envelope.Status <> "voided"
    
    ' Wait 5 minutes
    Delay(TimeSpan.FromMinutes(5))
    
    ' Refresh envelope status
    Get Envelope(var_Envelope.EnvelopeId) → var_Envelope
    
    ' Log status
    Log Message: "Envelope status: " + var_Envelope.Status
Loop

If var_Envelope.Status = "completed" Then
    ' Download and upload logic here
End If
```

**⚠️ Cons**: 
- Block robot slot cho đến khi ký xong (có thể vài ngày!)
- Không recommend cho production

---

## 📊 PHASE 5: Error Handling & Logging

### Common Errors

#### Error 1: `401 Unauthorized`

**Cause**: Chưa grant consent hoặc token expired

**Solution**:
1. Quay lại Orchestrator → Integration Service → Connector
2. Click **Refresh Token** hoặc **Re-authenticate**
3. Grant consent lại (step 1.1.3)

---

#### Error 2: `INVALID_EMAIL_ADDRESS`

**Cause**: Email recipient không valid hoặc không tồn tại

**Solution**:
- Validate email format trước khi gọi DocuSign
- Regex: `^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`

**Try-Catch**:
```vb
Try
    Add Recipient to Envelope
Catch ex As Exception
    If ex.Message.Contains("INVALID_EMAIL") Then
        Log Error: "Invalid email for recipient"
        ' Update SharePoint with error status
    End If
    Rethrow
End Try
```

---

#### Error 3: `ENVELOPE_CANNOT_BE_SENT`

**Cause**: 
- Envelope không có recipients
- Envelope không có documents
- Routing order conflicts

**Solution**:
- Đảm bảo add ít nhất 1 recipient
- Add document trước khi send
- Routing order phải unique và sequential

---

### Logging Best Practices

**Add Log Message** activities tại các điểm:

```vb
Log "INFO: Creating envelope for contract: " + in_DocumentoCompras
Log "INFO: Envelope created: " + out_EnvelopeID
Log "INFO: Adding document: " + in_PDFFilePath
Log "INFO: Adding recipient 1: " + in_EmailTecnico
Log "INFO: Adding recipient 2: " + in_EmailJefe
Log "INFO: Envelope sent successfully"
Log "INFO: Envelope status: " + var_Envelope.Status
```

**Lưu vào Orchestrator Logs**:
- Level: Info, Warn, Error, Fatal
- Include: Envelope ID, Contract number, Timestamp

---

## 🧪 PHASE 6: Testing

### Test Plan

#### Test Case 1: Happy Path - Successful Signing

**Steps**:
1. Create 1 evaluation trong SharePoint
2. Set `aprobada = true`
3. Trigger Robot 2
4. Robot tạo PDF và gửi DocuSign
5. Check email của 2 recipients
6. Sign as Técnico → Verify Jefe nhận email
7. Sign as Jefe → Verify status = "completed"
8. Run Monitor workflow → Verify signed PDF uploaded
9. Check SharePoint flags updated

**Expected Result**: ✅ Signed PDF trong SharePoint, flags = true

---

#### Test Case 2: Recipient Declines

**Steps**:
1. Send envelope
2. Click "Decline to Sign" trong DocuSign email
3. Run Monitor workflow

**Expected Result**: 
- Status = "declined"
- Log warning
- Admin được notify (optional)

---

#### Test Case 3: Invalid Email

**Steps**:
1. Set `email_responsable_tecnico = "invalid-email"`
2. Trigger workflow

**Expected Result**:
- Catch exception
- Log error "INVALID_EMAIL_ADDRESS"
- Không crash robot

---

#### Test Case 4: Envelope Void

**Steps**:
1. Admin login DocuSign portal
2. Void envelope manually
3. Run Monitor workflow

**Expected Result**:
- Status = "voided"
- Log info
- No download attempt

---

### Debugging Tips

**Enable detailed logging**:

1. Trong Studio, set **Log Level = Trace**
2. Run workflow
3. Xem **Output panel** → Tab **DocuSign**
4. Check Orchestrator Logs → Filter by Process name

**Common issues**:
- PDF file không tồn tại → Check file path
- Connection timeout → Check network/firewall
- Token expired → Re-authenticate connector

---

## 📈 PHASE 7: Production Deployment

### Pre-Deployment Checklist

- [ ] **Connector configured** trong Production Orchestrator
- [ ] **Grant consent** cho production environment
- [ ] **Activity package** installed trong production robots
- [ ] **SharePoint columns** created (envelope_id, etc.)
- [ ] **Document library** "Cartas Firmadas" created
- [ ] **Workflows tested** trong DEV/TEST environment
- [ ] **Error handling** implemented và tested
- [ ] **Logging** configured properly
- [ ] **Monitor workflow** scheduled trong Orchestrator
- [ ] **Runbook** documented cho support team

---

### Deployment Steps

1. **Publish workflows** to Production Orchestrator
2. **Create Process** trong Orchestrator
3. **Assign to Robot**
4. **Configure Triggers**:
   - Main workflow: Queue trigger (hoặc scheduled)
   - Monitor workflow: Time trigger (every 30-60 min)
5. **Test với 1 evaluation** (dry run)
6. **Monitor first runs** closely
7. **Enable for all evaluations**

---

## 📚 Reference

### DocuSign Status Codes

| Status | Meaning | Action |
|--------|---------|--------|
| `created` | Envelope created, not sent | - |
| `sent` | Sent to recipients | Monitor |
| `delivered` | Email delivered | Monitor |
| `signed` | All signed (temp status) | Download |
| `completed` | Fully completed | Download ✅ |
| `declined` | Recipient declined | Log warning |
| `voided` | Admin voided | Log info |

### UiPath DocuSign Activities

| Activity | Purpose |
|----------|---------|
| Create Envelope | Create new envelope |
| Create Envelope Using Template | Use DocuSign template |
| Add Document to Envelope | Attach PDF |
| Add Recipient to Envelope | Add signer |
| Update Envelope Status | Send envelope |
| Get Envelope | Check status |
| Get Envelope Document | Download signed PDF |
| Get Envelopes | List envelopes (for monitoring) |

### Useful Links

- [UiPath DocuSign Activities Docs](https://docs.uipath.com/activities/docs/docusign-activities)
- [Integration Service Setup](https://docs.uipath.com/integration-service/docs/docusign-authentication)
- [DocuSign API Reference](https://developers.docusign.com/docs/esign-rest-api/)
- [Envelope Status Guide](https://developers.docusign.com/docs/esign-rest-api/esign101/concepts/envelopes/status/)

---

## 🎯 Quick Start Summary

**Minimum steps để chạy được**:

1. Get DocuSign credentials (Account ID, Integration Key, Secret)
2. Configure connector trong Orchestrator
3. Grant consent
4. Install activity package trong Studio
5. Create sequence với 6 activities:
   - Create Envelope
   - Add Document
   - Add Recipient x2
   - Send Envelope
6. Create monitor workflow với Get Envelope + Download
7. Test!

Good luck! 🚀
