## configuration - Copy.xlsx
**Path:** `configuration - Copy.xlsx`

### base
```
key,env,value,item_class,description
FRAMEWORK_VERSION,,standalone_v4.1.0,string,Framework version
PROCESS_CODE,,p001,string,Process code
PROCESS_NAME_ID,,p001_test_framework,string,Process name - (also Storage Bucket name)
SISUA_DEVELOPER,,duc.trananh,string,Sisua Main Developer
SISUA_PROJECT_MANAGER,,duc.trananh,string,Sisua Project Manager
SISUA_SOLUTION_CONSULTANT,,duc.trananh,string,Sisua Solution Consultant
CLIENT_NAME,,sisuavn,string,ClickUp Company code of the Process owner (company name)
PROCESS_MAIN_FOLDER,,D:\uipath\test_cl_framework,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
REPLACE_WITH_SESSION_USER,,TRUE,boolean,Default: True. Replace all paths containing user with the session user.
PROCESS_DATA,,{PROCESS_MAIN_FOLDER}\process_data,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
OUTPUTS,,{PROCESS_MAIN_FOLDER}\out,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
EXCEPTIONS,,{OUTPUTS}\_exceptions,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
EXCEPTION_ROW_FILE_PATH,,{PROCESS_DATA}\row_exceptions\{0}_{1}_{2}_{3}.png,absolute_path,"0: Now (yyyyMMddHHmm, 1: intRowIdx (00X) , 2: exc_type (BE, SYS), 3: iteration"
MAX_STATE_TRIES_NUMBER,,2,int,Robot attempts if a system exception occurs. Resets on each state. Default: 2
ENVIRONMENT,,10,int,"DEV/NP: 10, QA: 20, PRD: 30"
## Process global parameters,,,,
WORKTRAY_TEMPLATE,,inputs\templates\_worktray_template.xlsx,relative_path,Process dependent
WORKTRAY_FILE,,{PROCESS_DATA}\worktray.xlsx,absolute_path,Main process file
## Memory parameters,,,,
MEMORY_KEY_COLUMNS,,item_id;,arrary,"Worktray/Mem columns that generate a unique row ID. Separated by: "";"""
MEMORY_DATE_COLUMN,,processing_date,string,Worktray column that allows memory to be separated by dates
MEMORY_TYPE,,monthly,string,"Options: daily, monthly, yearly, standalone (one file)"
MEMORY_FILES_TO_MERGE,,3,int,"Strongly suggested value: Daily: 15, Monthly: 3, Yearly: 2, standalone: 1. \nIt will be used in the workflow: fmw_concat_process_memory &  fmw_download_process_memory "
MEMORY_FOLDER,,{OUTPUTS}\{PROCESS_CODE}_memory,absolute_path,Process memory folder
MEMORY_FILE_NAME,,{PROCESS_CODE}_memory.xlsx,absolute_path,Same structure as worktray. Name without date
UPLOAD_MEM_TO_ORCHESTRATOR,10,FALSE,boolean,Default: False. Memory files and worktray will be uploaded to a Storage Bucket
UPLOAD_MEM_TO_ORCHESTRATOR,20,TRUE,boolean,Default: True. Memory files and worktray will be uploaded to a Storage Bucket
UPLOAD_MEM_TO_ORCHESTRATOR,30,TRUE,boolean,Default: True. Memory files and worktray will be uploaded to a Storage Bucket
DOWNLOAD_MEM_FROM_ORCH,10,FALSE,boolean,Default: False. Memory files will be downloaded from Storage Bucket to overwrite the local files
DOWNLOAD_MEM_FROM_ORCH,30,FALSE,boolean,Default: False. Memory files will be downloaded from Storage Bucket to overwrite the local files
1_state_name,,,,
INPUT_FILE,,inputs\examples\input_file.xlsx,relative_path,Replace  this
PARAMETER_TEST,10,This value will be used in DEV execution (ENVIRONMENT=10),string,Remove this
PARAMETER_TEST,30,This value will be used in PRD execution (ENVIRONMENT=30),string,Remove this
2_state_name,,,,
,,,,
3_state_name,,,,
,,,,
n_send_execution_report,,,,
EXECUTION_REPORT_TEMPLATE,,inputs\templates\execution_report_template.xlsx,relative_path,Process dependent
EXECUTION_REPORT_FILE,,{PROCESS_DATA}\{0}_execution_report.xlsx,absolute_path,0: Now (yyyyMMdd)
TRANSFORM_DTA_COLS_EXE_REPORT,,inputs\transform_dta_columns_exe_report.xlsx,relative_path,File to transform worktray to execution_report
## Emails paramaters,,,,
RECIPIENTS_FILE,,inputs\_recipients.xlsx,relative_path,Framework file
EMAIL_WRAPPER_MAIN,,inputs\email_files\email_wrapper_main.html,relative_path,Change for each client
BODY_EXECUTION_REPORT,,inputs\email_files\body_execution_report.txt,relative_path,Process dependent. Successful execution
BODY_BUSINESS_EXCEPTION,,inputs\email_files\body_business_exception.txt,relative_path,Process dependent
BODY_SYSTEM_EXCEPTION,,inputs\email_files\body_system_exception.txt,relative_path,"Framework file. 0: robot_state, 1: exception_source, 2: exception_msg"
EMAIL_SUBJECT_REPORT,,[{1}][{0}] Reporte de ejecución,string,"0: PROCESS_CODE, 1: Now (dd-MM-yyyy) ; [{0}] Execution report"
EMAIL_SUBJECT_SYSTEM,,[{1}][{0}] Excepción en la ejecución,string,"0: PROCESS_CODE, 1: Now (dd-MM-yyyy) ; [{0}] Check execution"
EMAIL_SUBJECT_BUSINESS,,[{1}][{0}] Excepción regla de negocio,string,"0: PROCESS_CODE, 1: Now (dd-MM-yyyy) ; [{0}] Business Rule exception"
EMAIL_SERVER_TYPE,,int-service-office365,string,Options: smtp ; app-scope-office365 ; int-service-office365 ; int-service-gmail
EMAIL_CREDENTIALS,,robot_email_account,string,Only if EMAIL_SERVER_TYPE=smtp or app-scope-office365. Saved in Orch Assets
EMAIL_OFFICE365_APP_ID,,O365_APP_ID,string,Only if EMAIL_SERVER_TYPE=app-scope-office365. Saved in Orch Assets
EMAIL_OFFICE365_TENANT_ID,,O365_TENANT_ID,string,Only if EMAIL_SERVER_TYPE=app-scope-office365. Saved in Orch Assets
EMAIL_SMTP_SERVER,,none,string,Only if EMAIL_SERVER_TYPE=smtp. Default: smtp.office365.com
EMAIL_SMTP_PORT,,587,int,Only if EMAIL_SERVER_TYPE=smtp. Default: 587
ROBOT_NAME,,ROBOT,string,Name that appears in sent emails (only if EMAIL_SERVER_TYPE=smtp)
HTML_TABLE_HEADER_BKG_COLOR,,#3b82f6,string,Hex color. Change for each client if needed
HTML_TABLE_HEADER_FONT_COLOR,,#ffffff,string,Hex color. Change for each client if needed
HTML_TABLE_STRIPES_COLOR,,#f9fafb,string,Hex color. Change for each client if needed
EMAIL_CLIENT_LOGO_URL,,,string,Change for each client. arrExtraWrapperFields: 1
EMAIL_ROBOT_SIGNATURE,,SISUA_BOT,string,Change for each client. arrExtraWrapperFields: 2
```

## configuration.xlsx
**Path:** `configuration.xlsx`

### base
```
key,env,value,item_class,description
FRAMEWORK_VERSION,,standalone_v4.1.0,string,Framework version
PROCESS_CODE,,p001,string,Process code
PROCESS_NAME_ID,,p001_test_framework,string,Process name - (also Storage Bucket name)
SISUA_DEVELOPER,,duc.trananh,string,Sisua Main Developer
SISUA_PROJECT_MANAGER,,duc.trananh,string,Sisua Project Manager
SISUA_SOLUTION_CONSULTANT,,duc.trananh,string,Sisua Solution Consultant
CLIENT_NAME,,sisuavn,string,ClickUp Company code of the Process owner (company name)
PROCESS_MAIN_FOLDER,,D:\uipath\test_cl_framework,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
REPLACE_WITH_SESSION_USER,,TRUE,boolean,Default: True. Replace all paths containing user with the session user.
PROCESS_DATA,,{PROCESS_MAIN_FOLDER}\process_data,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
OUTPUTS,,{PROCESS_MAIN_FOLDER}\out,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
EXCEPTIONS,,{OUTPUTS}\_exceptions,absolute_path,It must be an absolute path to save files on the local machine. Created by robot.
EXCEPTION_ROW_FILE_PATH,,{PROCESS_DATA}\row_exceptions\{0}_{1}_{2}_{3}.png,absolute_path,"0: Now (yyyyMMddHHmm, 1: intRowIdx (00X) , 2: exc_type (BE, SYS), 3: iteration"
MAX_STATE_TRIES_NUMBER,,2,int,Robot attempts if a system exception occurs. Resets on each state. Default: 2
ENVIRONMENT,,10,int,"DEV/NP: 10, QA: 20, PRD: 30"
## Process global parameters,,,,
WORKTRAY_TEMPLATE,,inputs\templates\_worktray_template.xlsx,relative_path,Process dependent
WORKTRAY_FILE,,{PROCESS_DATA}\worktray.xlsx,absolute_path,Main process file
## Memory parameters,,,,
MEMORY_KEY_COLUMNS,,item_id;,arrary,"Worktray/Mem columns that generate a unique row ID. Separated by: "";"""
MEMORY_DATE_COLUMN,,processing_date,string,Worktray column that allows memory to be separated by dates
MEMORY_TYPE,,monthly,string,"Options: daily, monthly, yearly, standalone (one file)"
MEMORY_FILES_TO_MERGE,,3,int,"Strongly suggested value: Daily: 15, Monthly: 3, Yearly: 2, standalone: 1. \nIt will be used in the workflow: fmw_concat_process_memory &  fmw_download_process_memory "
MEMORY_FOLDER,,{OUTPUTS}\{PROCESS_CODE}_memory,absolute_path,Process memory folder
MEMORY_FILE_NAME,,{PROCESS_CODE}_memory.xlsx,absolute_path,Same structure as worktray. Name without date
UPLOAD_MEM_TO_ORCHESTRATOR,10,TRUE,boolean,Default: False. Memory files and worktray will be uploaded to a Storage Bucket
UPLOAD_MEM_TO_ORCHESTRATOR,20,TRUE,boolean,Default: True. Memory files and worktray will be uploaded to a Storage Bucket
UPLOAD_MEM_TO_ORCHESTRATOR,30,TRUE,boolean,Default: True. Memory files and worktray will be uploaded to a Storage Bucket
DOWNLOAD_MEM_FROM_ORCH,10,TRUE,boolean,Default: False. Memory files will be downloaded from Storage Bucket to overwrite the local files
DOWNLOAD_MEM_FROM_ORCH,30,FALSE,boolean,Default: False. Memory files will be downloaded from Storage Bucket to overwrite the local files
1_state_name,,,,
INPUT_FILE,,inputs\examples\input_file.xlsx,relative_path,Replace  this
PARAMETER_TEST,10,This value will be used in DEV execution (ENVIRONMENT=10),string,Remove this
PARAMETER_TEST,30,This value will be used in PRD execution (ENVIRONMENT=30),string,Remove this
2_state_name,,,,
,,,,
3_state_name,,,,
,,,,
n_send_execution_report,,,,
EXECUTION_REPORT_TEMPLATE,,inputs\templates\execution_report_template.xlsx,relative_path,Process dependent
EXECUTION_REPORT_FILE,,{PROCESS_DATA}\{0}_execution_report.xlsx,absolute_path,0: Now (yyyyMMdd)
TRANSFORM_DTA_COLS_EXE_REPORT,,inputs\transform_dta_columns_exe_report.xlsx,relative_path,File to transform worktray to execution_report
## Emails paramaters,,,,
RECIPIENTS_FILE,,inputs\_recipients.xlsx,relative_path,Framework file
EMAIL_WRAPPER_MAIN,,inputs\email_files\email_wrapper_main.html,relative_path,Change for each client
BODY_EXECUTION_REPORT,,inputs\email_files\body_execution_report.txt,relative_path,Process dependent. Successful execution
BODY_BUSINESS_EXCEPTION,,inputs\email_files\body_business_exception.txt,relative_path,Process dependent
BODY_SYSTEM_EXCEPTION,,inputs\email_files\body_system_exception.txt,relative_path,"Framework file. 0: robot_state, 1: exception_source, 2: exception_msg"
EMAIL_SUBJECT_REPORT,,[{1}][{0}] Reporte de ejecución,string,"0: PROCESS_CODE, 1: Now (dd-MM-yyyy) ; [{0}] Execution report"
EMAIL_SUBJECT_SYSTEM,,[{1}][{0}] Excepción en la ejecución,string,"0: PROCESS_CODE, 1: Now (dd-MM-yyyy) ; [{0}] Check execution"
EMAIL_SUBJECT_BUSINESS,,[{1}][{0}] Excepción regla de negocio,string,"0: PROCESS_CODE, 1: Now (dd-MM-yyyy) ; [{0}] Business Rule exception"
EMAIL_SERVER_TYPE,,int-service-office365,string,Options: smtp ; app-scope-office365 ; int-service-office365 ; int-service-gmail
EMAIL_CREDENTIALS,,robot_email_account,string,Only if EMAIL_SERVER_TYPE=smtp or app-scope-office365. Saved in Orch Assets
EMAIL_OFFICE365_APP_ID,,O365_APP_ID,string,Only if EMAIL_SERVER_TYPE=app-scope-office365. Saved in Orch Assets
EMAIL_OFFICE365_TENANT_ID,,O365_TENANT_ID,string,Only if EMAIL_SERVER_TYPE=app-scope-office365. Saved in Orch Assets
EMAIL_SMTP_SERVER,,none,string,Only if EMAIL_SERVER_TYPE=smtp. Default: smtp.office365.com
EMAIL_SMTP_PORT,,587,int,Only if EMAIL_SERVER_TYPE=smtp. Default: 587
ROBOT_NAME,,ROBOT,string,Name that appears in sent emails (only if EMAIL_SERVER_TYPE=smtp)
HTML_TABLE_HEADER_BKG_COLOR,,#3b82f6,string,Hex color. Change for each client if needed
HTML_TABLE_HEADER_FONT_COLOR,,#ffffff,string,Hex color. Change for each client if needed
HTML_TABLE_STRIPES_COLOR,,#f9fafb,string,Hex color. Change for each client if needed
EMAIL_CLIENT_LOGO_URL,,,string,Change for each client. arrExtraWrapperFields: 1
EMAIL_ROBOT_SIGNATURE,,SISUA_BOT,string,Change for each client. arrExtraWrapperFields: 2
```

## _recipients.xlsx
**Path:** `inputs\_recipients.xlsx`

### base
```
email_address,environment,recipient_type,execution_report,business_exception,system_exception,observation
duc.trananh@sisuadigital.com,10,to,TRUE,TRUE,TRUE,DEV
sisua.developer@sisuadigital.com,20,cc,TRUE,TRUE,TRUE,QA/UAT
sisua.project_manager@sisuadigital.com,20,to,TRUE,TRUE,TRUE,QA/UAT
sisua.solution_consultant@sisuadigital.com,20,cc,TRUE,TRUE,TRUE,QA/UAT
client_1@company.com,30,to,TRUE,TRUE,TRUE,PRD - client
client_2@company.com,30,to,TRUE,TRUE,TRUE,PRD - client
process_reports@sisuadigital.com,30,cc,TRUE,TRUE,TRUE,PRD - Sisua (mandatory)
sisua.solution_consultant@sisuadigital.com,30,cc,TRUE,TRUE,TRUE,PRD - Sisua
sisua.developer@sisuadigital.com,30,cc,TRUE,TRUE,TRUE,PRD - Sisua
```

### _internal
```
,,,,,
,environments,,type,,boolean
,10,,to,,TRUE
,20,,cc,,FALSE
,30,,bcc,,
```

## utils_transform_dta_columns.xlsx
**Path:** `inputs\_utils\utils_transform_dta_columns.xlsx`

### base
```
original_name,index,action,new_column_name,default_value
Original Column 1,0,RENAME,renamed_column_1,
Original Column 2,1,RENAME,renamed_column_2,
Deleted Column,2,DELETE,,
,3,ADD,new_column_1,
```

### _rules
```
,
,RENAME
,DELETE
,ADD
```

## input_file.xlsx
**Path:** `inputs\examples\input_file.xlsx`

### base
```
item_id,outcome_state_1,outcome_state_2
id_1,r1,
id_2,r2,
id_3,r3,
id_4,r4,
id_5,r5,
id_6,r6,
id_7,r7,
id_8,r8,
id_9,r9,
id_10,r10,
id_11,r11,
```

## _worktray_template.xlsx
**Path:** `inputs\templates\_worktray_template.xlsx`

### base
```
processing_date,item_id,observation,outcome_state_1,outcome_state_2
```

### _internal
```
,,,,,
,environments,,type,,boolean
,10,,to,,TRUE
,20,,cc,,FALSE
,30,,bcc,,
```

## execution_report_template.xlsx
**Path:** `inputs\templates\execution_report_template.xlsx`

### base
```
processing_date,item_id,observation,outcome_state_1,outcome_state_2
```

## transform_dta_columns_exe_report.xlsx
**Path:** `inputs\transform_dta_columns_exe_report.xlsx`

### base
```
original_name,index,action,new_column_name,default_value
processing_date,0,RENAME,Fecha,
item_id,1,RENAME,item_id,
observation,2,RENAME,observation,
outcome_state_1,3,RENAME,renamed_column_1,
outcome_state_2,4,RENAME,renamed_column_2,
```

### _rules
```
,
,RENAME
,DELETE
,ADD
```

