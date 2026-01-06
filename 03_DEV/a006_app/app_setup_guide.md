# App Setup Guide - A006 Power App

Tài liệu này hướng dẫn thiết lập **App.OnStart** và global variables cho ứng dụng A006.

---

## 1. App.OnStart Configuration

### 1.1. Truy cập App.OnStart

1. Trong Power Apps Studio, chọn **App** object (ở Tree View)
2. Tìm property **OnStart** trong dropdown properties
3. Nhập code theo hướng dẫn dưới đây

---

## 2. Development Mode Setup (DEV)

### 2.1. Mock User Configuration

Trong môi trường **DEV/TEST**, sử dụng mock user để dễ dàng test các roles khác nhau:

```powerfx
// App.OnStart - DEVELOPMENT MODE

// ========================================
// DEV MODE - MOCK USER CONFIGURATION
// Comment out các dòng này khi deploy PRODUCTION
// ========================================
Set(gblUserEmail, "juan.perez@isa.com");  // ← MOCK USER EMAIL
Set(gblUserName, "Juan Pérez");           // ← MOCK USER NAME
// ========================================
```

### 2.2. Danh sách Mock Users (Test Accounts)

Dùng các mock emails sau để test từng role:

#### Responsable Técnico (Technical Lead)
```powerfx
Set(gblUserEmail, "responsable.tecnico@isa.com");
Set(gblUserName, "Roberto Técnico");
```
**Permissions**: Đánh giá Technico Section, có thể đánh giá SST/MA nếu không có người chuyên trách

#### Responsable SST (Safety & Health)
```powerfx
Set(gblUserEmail, "responsable.sst@isa.com");
Set(gblUserName, "Sara SST");
```
**Permissions**: Đánh giá SST Section (nếu `responsable_sst_responde = true`)

#### Responsable MA (Environment)
```powerfx
Set(gblUserEmail, "responsable.ma@isa.com");
Set(gblUserName, "Mario Ambiente");
```
**Permissions**: Đánh giá MA Section (nếu `responsable_ma_responde = true`)

#### Jefe de Área (Approver)
```powerfx
Set(gblUserEmail, "jefe.area@isa.com");
Set(gblUserName, "José Jefe");
```
**Permissions**: Phê duyệt evaluations

#### Multi-role User (Testing edge case)
```powerfx
Set(gblUserEmail, "admin@isa.com");
Set(gblUserName, "Admin User");
```
**Note**: Gán email này cho nhiều roles trong test data để test edge cases

---

## 3. Production Mode Setup (PROD)

### 3.1. Real User Configuration

Khi deploy lên **PRODUCTION**, **BẮT BUỘC** sử dụng real user credentials:

```powerfx
// App.OnStart - PRODUCTION MODE

// ========================================
// PRODUCTION MODE - REAL USER
// Uncomment các dòng này khi deploy PRODUCTION
// ========================================
Set(gblUserEmail, User().Email);
Set(gblUserName, User().FullName);
// ========================================

// ========================================
// DEV MODE - ĐỪNG QUÊN COMMENT OUT TRƯỚC KHI DEPLOY!
// ========================================
// Set(gblUserEmail, "juan.perez@isa.com");  // ← MOCK USER - MUST BE COMMENTED!
// Set(gblUserName, "Juan Pérez");           // ← MOCK USER - MUST BE COMMENTED!
// ========================================
```

> [!CAUTION]
> **CRITICAL DEPLOYMENT CHECKLIST**:
> - [ ] Comment out mock user lines (DEV MODE section)
> - [ ] Uncomment real user lines (PRODUCTION MODE section)
> - [ ] Test với real user account trước khi release
> - [ ] Verify không có hardcoded emails trong filters

---

## 4. Additional Global Variables (Optional)

### 4.1. Screen Tracking

Để track màn hình hiện tại (cho header highlighting):

```powerfx
Set(gblCurrentScreen, "");
```

Mỗi screen sẽ set trong OnVisible:
```powerfx
Set(gblCurrentScreen, scrLanding);
```

### 4.2. Environment Variables (Recommended)

Nếu sử dụng Environment Variables cho SharePoint connections:

```powerfx
// Lấy SharePoint List URLs từ Environment Variables
Set(gblOngoingListUrl, EnvironmentVariable("OngoingEvaluationListUrl"));
Set(gblCompletedListUrl, EnvironmentVariable("CompletedEvaluationListUrl"));
```

---

## 5. Complete App.OnStart Template

### 5.1. Development Version

```powerfx
// ========================================
// A006 APP - DEVELOPMENT MODE
// ========================================

// Mock User (Comment out in PRODUCTION)
Set(gblUserEmail, "responsable.tecnico@isa.com");
Set(gblUserName, "Roberto Técnico");

// Screen tracking
Set(gblCurrentScreen, "");

// Environment Variables (Optional)
// Set(gblOngoingListUrl, EnvironmentVariable("OngoingEvaluationListUrl"));
// Set(gblCompletedListUrl, EnvironmentVariable("CompletedEvaluationListUrl"));
```

### 5.2. Production Version

```powerfx
// ========================================
// A006 APP - PRODUCTION MODE
// ========================================

// Real User
Set(gblUserEmail, User().Email);
Set(gblUserName, User().FullName);

// Screen tracking
Set(gblCurrentScreen, "");

// Environment Variables (Optional)
// Set(gblOngoingListUrl, EnvironmentVariable("OngoingEvaluationListUrl"));
// Set(gblCompletedListUrl, EnvironmentVariable("CompletedEvaluationListUrl"));
```

---

## 6. Using Global Variables in Screens

### 6.1. Screen OnVisible Pattern

Tất cả screens nên follow pattern này:

```powerfx
// scrLanding.OnVisible, scrEvaluaciones.OnVisible, etc.

// Set local variables from global
Set(varUserEmail, gblUserEmail);
Set(varUserName, gblUserName);

// Set current screen
Set(gblCurrentScreen, scrLanding);  // Thay đổi tên screen tương ứng

// ... rest of OnVisible logic
```

### 6.2. Filters and Lookups

Sử dụng `gblUserEmail` trong tất cả filters:

```powerfx
// Filter evaluations - WITH flag checks for SST/MA
Filter(
    ongoingEvaluations,
    (
        // Técnico - always included if assigned
        email_responsable_tecnico = gblUserEmail ||
        
        // SST - only if flag is ON and email matches
        (responsable_sst_responde && email_responsable_sst = gblUserEmail) ||
        
        // MA - only if flag is ON and email matches
        (responsable_ma_responde && email_responsable_ma = gblUserEmail) ||
        
        // Jefe Area (Approver) - always included if assigned
        email_jefe_area = gblUserEmail
    ) &&
    aprobada = false  // Only pending evaluations
)
```

> [!TIP]
> **Không dùng trực tiếp `User().Email` trong screens**. Luôn dùng `gblUserEmail` để dễ switch giữa dev/prod modes.

---

## 7. Testing Workflow

### 7.1. Testing Different Roles

1. **Thay đổi mock email** trong App.OnStart
2. **Save** app
3. **Reload** app (F5 hoặc click Refresh icon)
4. App sẽ chạy với role mới

### 7.2. Test Data Setup

Đảm bảo SharePoint có test data với các email tương ứng:

```
Evaluation 1:
- email_responsable_tecnico: responsable.tecnico@isa.com
- email_jefe_area: jefe.area@isa.com

Evaluation 2:
- email_responsable_tecnico: responsable.tecnico@isa.com
- email_responsable_sst: responsable.sst@isa.com
- responsable_sst_responde: true
```

---

## 8. Deployment Checklist

### 8.1. Pre-deployment

- [ ] Review App.OnStart code
- [ ] Verify mock user lines are commented out
- [ ] Verify real user lines are active
- [ ] Test với 1 real user account
- [ ] Check không có hardcoded emails trong code

### 8.2. Post-deployment

- [ ] Verify user thấy đúng data (filter by their email)
- [ ] Test permissions (chỉ edit được sections được gán)
- [ ] Verify submit data có đúng user email trong audit fields

---

## 9. Troubleshooting

### 9.1. User không thấy data

**Symptom**: Dropdown/Gallery trống

**Check**:
1. `gblUserEmail` có match với email trong SharePoint không?
2. Filter logic có đúng không?
3. SharePoint data có records nào match user email không?

**Debug**:
```powerfx
// Thêm label để debug
lbl_Debug.Text = "User: " & gblUserEmail & 
                 " | Count: " & CountRows(Filter(ongoingEvaluations, ...))
```

### 9.2. Forgot to switch to Production mode

**Symptom**: Production app vẫn hiển thị data của mock user

**Solution**:
1. Edit app trong Power Apps Studio
2. Sửa App.OnStart về Production mode
3. **Save** và **Publish** lại

---

## 10. Best Practices

1. **Comment rõ ràng**: Luôn comment sections DEV vs PROD trong App.OnStart
2. **Consistent naming**: Dùng `gbl` prefix cho global variables
3. **No hardcoding**: Tránh hardcode emails trong screens, luôn dùng variables
4. **Test before deploy**: Luôn test với real account trước khi deploy production
5. **Version control**: Track changes trong App.OnStart qua git commits hoặc version notes

---

## Appendix: Quick Reference

### Global Variables

| Variable | Type | Purpose | Set in |
|----------|------|---------|--------|
| `gblUserEmail` | Text | Email của user hiện tại | App.OnStart |
| `gblUserName` | Text | Tên đầy đủ của user | App.OnStart |
| `gblCurrentScreen` | Screen | Screen hiện tại (tracking) | Each Screen.OnVisible |

### Mock Users Quick Copy

```powerfx
// Técnico
Set(gblUserEmail, "responsable.tecnico@isa.com"); Set(gblUserName, "Roberto Técnico");

// SST
Set(gblUserEmail, "responsable.sst@isa.com"); Set(gblUserName, "Sara SST");

// MA
Set(gblUserEmail, "responsable.ma@isa.com"); Set(gblUserName, "Mario Ambiente");

// Approver
Set(gblUserEmail, "jefe.area@isa.com"); Set(gblUserName, "José Jefe");
```
