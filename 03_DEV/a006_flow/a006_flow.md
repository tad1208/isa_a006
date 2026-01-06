dev flow power automate trong solution này để khi 
- có thay đổi ở 1 trong 3 cột tecnico-submitted, sst_submitted, ma_submitted và 3 cột này đều = true thì gửi mail thông báo đến jefe area để check và approve 
- nếu cột rechaza chuyển từ false thành true thì gửi mail thông báo đến tecnico, sst, ma để review lại evaluation 
- thông báo đến ma khi tecnico chuyển cờ ma_responsible từ false thành true

Flow: A006_Notify_Evaluation_Events
0) Trigger

SharePoint → When an item is created or modified (V3)

Site Address: site của bạn

List Name: thường là ongoingEvaluations (vì các cờ submitted / reject / responsible nằm ở item evaluation)

1) Bắt “cột nào đã đổi” (để detect transition)

SharePoint → Get changes for an item or a file (properties only)

Site Address / List Name: như trigger

Id: ID từ trigger

Since: dùng Trigger Window Start Token (dynamic content)

Mục tiêu: lấy được các boolean kiểu Has Column Changed: <tên cột>.

Nhánh A — Khi 1 trong 3 cột submitted thay đổi, và cả 3 đều = true → mail cho jefe area (Area Chief/Manager)

Điều kiện (Condition):

OR(HasChanged(tecnico-submitted), HasChanged(sst_submitted), HasChanged(ma_submitted))

AND (tecnico-submitted = true)

AND (sst_submitted = true)

AND (ma_submitted = true)

Action: Send email

Office 365 Outlook → Send an email (V2)

To: dùng cột email của jefe area trong item: email_jefe_area

Subject gợi ý: A006 - Evaluation ready for approval - Contract {documento_compras}

Body gợi ý: include documento_compras, nombre_proveedor, nombre_area, và link tới item/Power App (dynamic content “Link to item”)

✅ Mẹo chống spam: thêm check phụ rechazada = false và aprobada = false (nếu app có) để không gửi lại khi item đã kết thúc.

Nhánh B — Khi rechaza/rechazada chuyển từ false → true → mail cho tecnico + sst + ma

Trong data model list có cờ rechazada (bool) . (Bạn đang gọi “rechaza”, còn list đang mô tả “rechazada” — coi như cùng ý: Rejected.)

Điều kiện (Condition):

HasChanged(rechazada) (hoặc rechaza theo đúng internal name của bạn)

AND (rechazada = true)

Action: Send email

To:

email_responsable_tecnico

email_responsable_sst

email_resopnsable_ma

Subject gợi ý: A006 - Evaluation rejected - Please review - Contract {documento_compras}

Body: nêu rõ “rechazada = true”, kèm observaciones_jefe_area (nếu có), và link mở form để sửa.

Nhánh C — Thông báo MA khi tecnico đổi cờ ma_responsible từ false → true

Trong list đang có cờ kiểu “MA responsible responds” là responsable_ma_responde (bool) . Nếu Power App/SharePoint của bạn đang đặt tên là ma_responsible thì bạn map theo internal name thực tế của cột.

Điều kiện (Condition):

HasChanged(ma_responsible) (hoặc responsable_ma_responde)

AND (ma_responsible = true)

Action: Send email

To: email_resopnsable_ma

Subject gợi ý: A006 - You are assigned as MA responsible - Contract {documento_compras}

Body: contract info + link item.

Gợi ý cấu trúc Flow (để không gửi trùng trong 1 lần update)

Sau “Get changes…”, làm 3 Condition độc lập theo thứ tự:

Reject (Nhánh B)

All submitted → jefe area (Nhánh A)

MA responsible assigned (Nhánh C)

Nếu có case 1 update vừa reject vừa đổi submitted… bạn có thể:

dùng Terminate sau khi gửi mail reject (vì reject ưu tiên cao), hoặc

vẫn cho chạy tiếp (tuỳ business).