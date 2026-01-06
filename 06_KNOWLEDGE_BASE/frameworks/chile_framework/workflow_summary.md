# UiPath Workflow Summary (non-StateMachine)
_Project: `D:\uipath_code\CL\test_cl_framework\framework`_

## fmw_send_email
**Path:** `wfs\_fmw\fmw_send_email.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strSubject | `InArgument(x:String)` | In |
| _arrBodyFields | `InArgument(s:String[])` | In |
| _arrRecipients | `InArgument(s:String[])` | In |
| _strSenderName | `InArgument(x:String)` | In |
| _arrAttachmentsFiles | `InArgument(s:String[])` | In |
| _strEmailWrapperFile | `InArgument(x:String)` | In |
| _strBodyMsgFile | `InArgument(x:String)` | In |
| _arrExtraWrapperFields | `InArgument(s:String[])` | In |
| _intMaxTimeToSendEmail | `InArgument(x:Int32)` | In |
| _arrRecipientsCC | `InArgument(s:String[])` | In |
| _strRecipientsColumn | `InArgument(x:String)` | In |
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |
| _boolSendToClient | `InArgument(x:Boolean)` | In |

### Variables

| Name | Type |
|---|---|
| strEmailUser | `x:String` |
| dtaRecipients | `sd:DataTable` |
| strTo | `x:String` |
| strCc | `x:String` |
| strBcc | `x:String` |
| strDelimiter | `x:String` |
| strBodyMsg | `x:String` |
| strEmailBody | `x:String` |
| arrBcc | `s:String[]` |
| arrCc | `s:String[]` |
| arrTo | `s:String[]` |
| arrWrapperExtraFields | `s:String[]` |
| strEmailCreds | `x:String` |
| intEnv | `x:Int32` |
| strRecipientsFile | `x:String` |
| strEmailServerType | `x:String` |
| strSmtpServer | `x:String` |
| intSmtpPort | `x:Int32` |
| strO365AppId | `x:String` |
| strO365TenantId | `x:String` |
| strExceptionsFolder | `x:String` |
| jobInfo | `ui:CurrentJobInfo` |
| arrToRecipientsFile | `s:String[]` |
| arrCcRecipientsFile | `s:String[]` |
| arrBccRecipientsFile | `s:String[]` |
| arrrowRecipients | `sd:DataRow[]` |
| arrRecipientCols | `s:String[]` |
| intEnvLevel | `x:Int32` |
| strExceptionsAttachment | `x:String` |
| arrExceptionFiles | `s:String[]` |
| boolExcFolderExists | `x:Boolean` |
| dblFileSize | `x:Double` |
| dblMaxSizeToZip | `x:Double` |
| intFilesToZip | `x:Int32` |
| arrFilesToZip | `s:String[]` |
| boolSentEmail | `x:Boolean` |
| sstrEmailPassword | `ss:SecureString` |

### Logic
- Members
  - _strSubject (Type=InArgument(x:String))
  - _arrBodyFields (Type=InArgument(s:String[]))
  - _arrRecipients (Type=InArgument(s:String[]))
  - _strSenderName (Type=InArgument(x:String))
  - _arrAttachmentsFiles (Type=InArgument(s:String[]))
  - _strEmailWrapperFile (Type=InArgument(x:String))
  - _strBodyMsgFile (Type=InArgument(x:String))
  - _arrExtraWrapperFields (Type=InArgument(s:String[]))
  - _intMaxTimeToSendEmail (Type=InArgument(x:Int32))
  - _arrRecipientsCC (Type=InArgument(s:String[]))
  - _strRecipientsColumn (Type=InArgument(x:String))
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
  - _boolSendToClient (Type=InArgument(x:Boolean))
- Sequence: fmw_send_email
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - assign_test_arguments_variables:
        - assign_test_arguments_default:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=1178,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_parameters:
  - if_np_execution_add_test_to_subject:
  - LogMessage: log_subject (Level=Info, Message=["Subject: " + _strSubject], ViewState=False)
  - Sequence: get_recipients
    - initialize_recipients_arrays:
    - If (Condition: [not String.IsNullOrEmpty(strRecipientsFile)])
      - Then:
        - Sequence: get_recipients_from_file
          - read_recipients (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaRecipients], VirtualizedContainerService.HintSize=479,120, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[strRecipientsFile], ViewState=True)
          - fe_email_format_env_col (ColumnNames={x:Null}, CurrentIndex={x:Null}, DataTable=[dtaRecipients], VirtualizedContainerService.HintSize=479,247, WorkflowViewState.IdRef=ForEachRow_3)
            - ForEachRow.Body
          - sort_recipients_by_env (ColumnIndex={x:Null}, DataColumn={x:Null}, Annotation.AnnotationText=Column = environment
Order     = Ascending 
, ColumnName=environment, DataTable=[dtaRecipients], VirtualizedContainerService.HintSize=479,141, WorkflowViewState.IdRef=SortDataTable_1, Order=Ascending, SortOrder=Ascending, ViewState=False; OutputDataTable -> [dtaRecipients])
          - filter_active_recipients_by_email_type:
          - get_recipients_according_env:
          - get_recipients_by_type:
          - append_file_recipients_and_remove_empty_values:
      - Else:
        - Sequence: Else
    - filter_client_emails:
    - assign_recipients_strings:
    - LogMessage: log_to_recipients (Level=Info, Message=["To: " + strTo], ViewState=False)
    - LogMessage: log_cc_recipients (Level=Info, Message=["Cc: " + strCc], ViewState=False)
    - LogMessage: log_bcc_recipients (Level=Info, Message=["Bbc: " + strBcc], ViewState=False)
  - Sequence: build_email_body
    - read_template_wrapper (File={x:Null}, Content=[strEmailBody], Encoding=utf-8, FileName=[_strEmailWrapperFile], VirtualizedContainerService.HintSize=479,112, WorkflowViewState.IdRef=ReadTextFile_2, ViewState=True)
    - read_body_file (File={x:Null}, Content=[strBodyMsg], Encoding=utf-8, FileName=[_strBodyMsgFile], VirtualizedContainerService.HintSize=479,112, WorkflowViewState.IdRef=ReadTextFile_1, ViewState=True)
    - join_email_body_message:
    - Assign:  = 
  - Sequence: format_attachments
    - folder_exception_exists (VirtualizedContainerService.HintSize=515,57, WorkflowViewState.IdRef=FolderExistsX_1, Path=[strExceptionsFolder], ViewState=False; Exists -> [boolExcFolderExists])
    - remove_empty_attachments:
    - If (Condition: [arrExceptionFiles.Count > 0])
      - Then:
        - Sequence: zip_and_attach_exception_folder
          - assign_max_files_to_zip:
          - LogMessage: log_files_to_zip (Level=Info, Message=[String.Format("Zipping exception folder. Files: {0}/{1}",arrFilesToZip.Count, arrExceptionFiles.Count)], ViewState=False)
          - compress_zip_exception_folders (CompressedFileInfo={x:Null}, CompressedResource={x:Null}, Password={x:Null}, ResourcesToArchive={x:Null}, SecurePassword={x:Null}, AllowDuplicateContentNames=False, CodePage=Default, CompressedFileName=[strExceptionsAttachment], CompressionLevel=Normal, EncryptionAlgorithm=Classic, VirtualizedContainerService.HintSize=479,247, WorkflowViewState.IdRef=CompressFiles_1, OverrideExistingFile=True, ContentToArchive=[String.Join("|", arrFilesToZip)])
          - append_exception_folder:
      - Else:
        - Sequence: Else
  - LogMessage: log_num_attachments (Level=Info, Message=["Attachments files: " + _arrAttachmentsFiles.Length.ToString], ViewState=False)
  - Sequence: send_email
    - assign_sent_email_init:
    - Parallel: parallel_send_email_and_timeout_timer (VirtualizedContainerService.HintSize=1144,1753, WorkflowViewState.IdRef=Parallel_1)
      - Sequence: send_email
        - If (Condition: [{"smtp", "app-scope-office365"}.contains(strEmailServerType)])
          - Then:
            - Sequence: Then
          - Else:
            - Sequence: Else
        - LogMessage: log_send_email (Level=Info, Message=[String.Format("Sending email ({0}) ...", strEmailServerType)], ViewState=False)
        - Switch: sw_email_server_type (TypeArguments=x:String, Expression=[strEmailServerType], VirtualizedContainerService.HintSize=486,1145, WorkflowViewState.IdRef=Switch`1_3, ViewState=True)
          - send_smtp_email_outlook (ResourceAttachmentList={x:Null}, Key=smtp, Annotation.AnnotationText=SMTP server, AttachmentInputMode=FilePaths, AttachmentsCollection=[_arrAttachmentsFiles], Bcc=[strBcc], Body=[strEmailBody], Cc=[strCc], ContinueOnError={x:Null}, Email={x:Null}, EnableSSL=True, From={x:Null}, VirtualizedContainerService.HintSize=334,476, WorkflowViewState.IdRef=SendMail_1, IgnoreCRL={x:Null}, IsBodyHtml=True, MailMessage={x:Null}, Password={x:Null}, Port={x:Null}, ReplyTo={x:Null}, ResourceAttachments={x:Null}, SecureConnection=Auto, SecurePassword={x:Null}, Server={x:Null}, Subject=[_strSubject], TimeoutMS={x:Null}, To=[strTo], UseISConnection=True, UseOAuth={x:Null}, ViewState=True; Result -> {x:Null})
          - InvokeWorkflowFile: inv_office365_send_email.xaml -> wfs\_fmw\office365_send_email.xaml
            - in _strEmailAddress: [strEmailUser]
            - in _strTenantId: [strO365TenantId]
            - in _strAppId: [strO365AppId]
            - in _strSubject: [_strSubject]
            - in _arrAttachmentsFiles: [_arrAttachmentsFiles]
            - in _strEmailBody: [strEmailBody]
            - in _arrTo: [arrTo]
            - in _arrCc: [arrCc]
            - in _arrBcc: [arrBcc]
          - send_email_o365_int_service (ArgumentAttachmentPaths={x:Null}, AttachmentList={x:Null}, ConnectionAccountName={x:Null}, MailboxBackup={x:Reference __ReferenceID0}, Key=int-service-office365, AttachmentInputMode=Existing, AttachmentPaths={x:Null}, Attachments=[_arrAttachmentsFiles.Select(Function(path) LocalResource.FromPath(path))], AuthScopesInvalid=False, Bcc=[arrBcc], Body=[strEmailBody], Cc=[arrCc], ConnectionId=71421464-bb0f-4d64-8620-d40f2c4572bc, ContinueOnError={x:Null}, VirtualizedContainerService.HintSize=334,508, WorkflowViewState.IdRef=SendMailConnections_1, Importance=Normal, InputType=HTML, Mailbox={x:Null}, ReplyTo={x:Null}, SaveAsDraft=False, SingleAttachment={x:Null}, Subject=[_strSubject], TextBody={x:Null}, To=[arrTo], UseConnectionService=True, UseSharedMailbox=False, ViewState=True)
          - comment_add_wf (Key=int-service-gmail, VirtualizedContainerService.HintSize=334,193, WorkflowViewState.IdRef=Comment_1; Text -> // Add wf located in "SD Chile Developers - General\utils_uipath\wfs\Emails\gsuite_send_email.xaml". 
Add the 7 arguments:
1. _dicConfig = _dicConfig
2. _arrBcc = arrBcc
3. _arrCc = arrCc
4. _ArrTo = arrTo
5. _strEmailBody = strEmailBody
6. _arrAttachmentsFiles = _arrAttachmentsFiles
7. _strSubject = _strSubject)
        - assign_sent_email_true:
      - Sequence: execute_activity_timer
        - ForEach<> in [Enumerable.Range(0, _intMaxTimeToSendEmail)]
          - ActivityAction (TypeArguments=x:Int32)
            - If (Condition: [not boolSentEmail])
              - Then:
              - Else:
        - If (Condition: [not boolSentEmail])
          - Then:
            - Throw: throw_timeout_exception (Exception=[New System.Exception(String.Format("Send email activity timeout reached ({0} [s])", _intMaxTimeToSendEmail))], VirtualizedContainerService.HintSize=200,22, WorkflowViewState.IdRef=Throw_2)
    - LogMessage: log_sent_email (Level=Info, Message=["Email sent successfully"], ViewState=False)
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## fmw_concat_process_memory
**Path:** `wfs\_fmw\memory\fmw_concat_process_memory.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |
| _intExecutionDayOffset | `InArgument(x:Int32)` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dtaMemory | `OutArgument(sd:DataTable)` | Out |
| _arrMemoryFiles | `OutArgument(s:String[])` | Out |

### Variables

| Name | Type |
|---|---|
| boolFileExists | `x:Boolean` |
| strMemoryType | `x:String` |
| intFilesToMerge | `x:Int32` |
| strMemoryFolder | `x:String` |
| strMemFileName | `x:String` |
| strMemoryFile | `x:String` |
| strMemFileNameBase | `x:String` |
| boolTestWf | `x:Boolean` |
| jobInfo | `ui:CurrentJobInfo` |
| arrdtiMemoryDates | `s:DateTime[]` |
| dtaPeriodMem | `sd:DataTable` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
  - _dtaMemory (Type=OutArgument(sd:DataTable))
  - _arrMemoryFiles (Type=OutArgument(s:String[]))
  - _intExecutionDayOffset (Annotation.AnnotationText=Default: 0. Always negative value, Type=InArgument(x:Int32))
- Sequence: fmw_concat_process_memory
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict_v3.1 workflow -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [{"base"}]
        - assign_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=517,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_parameters:
  - LogMessage: log_mem_type_and_files_to_merge (Level=Info, Message=[String.Format("Mem type: {0} | Max files to merge: {1}", strMemoryType, intFilesToMerge)], ViewState=False)
  - Switch: sw_memory_type_assign_mem_files (TypeArguments=x:String, Expression=[strMemoryType], VirtualizedContainerService.HintSize=517,1030, WorkflowViewState.IdRef=Switch`1_2, ViewState=True)
    - assign_mem_file_specs_daily:
    - assign_mem_file_specs_monthly:
    - assign_mem_file_specs_yearly:
    - assign_mem_file_specs_standalone:
  - assign_memory_files:
  - LogMessage: log_mem_files_to_merge (Level=Info, Message=["Mem files to merge: " + _arrMemoryFiles.Count.ToString], ViewState=False)
  - ForEach<> in [_arrMemoryFiles]
    - ActivityAction (TypeArguments=x:String)
      - Sequence: Body
        - LogMessage: log_mem_file (Level=Info, Message=["Memory file: " + mem_file], ViewState=False)
        - pe_period_memory_file (Resource={x:Null}, VirtualizedContainerService.HintSize=481,147, WorkflowViewState.IdRef=PathExists_2, Path=[mem_file], PathType=File, ViewState=True; Exists -> [boolFileExists])
        - If (Condition: [not boolFileExists])
          - Then:
            - Sequence: Then
          - Else:
            - Sequence: Else
        - read_period_memory_file (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaPeriodMem], VirtualizedContainerService.HintSize=481,120, WorkflowViewState.IdRef=ReadRange_3, SheetName=base, WorkbookPath=[mem_file])
        - LogMessage: log_period_memory_rows (Level=Info, Message=["Rows in period memory file: " + dtaPeriodMem.RowCount.ToString], ViewState=False)
        - If (Condition: [_dtaMemory isNot Nothing])
          - Then:
            - merge_period_mem_with_memory (Destination=[_dtaMemory], VirtualizedContainerService.HintSize=479,133, WorkflowViewState.IdRef=MergeDataTable_2, MissingSchemaAction=Add, Source=[dtaPeriodMem])
          - Else:
            - assign_period_mem_as_memory:
  - If (Condition: [_dtaMemory isNot Nothing])
    - Then:
      - LogMessage: log_memory_rows (Level=Info, Message=["Memory rows: " + _dtaMemory.RowCount.ToString], ViewState=False)
    - Else:
      - LogMessage: log_no_mem_files (Level=Warn, Message=["Could not found any memory file!"], ViewState=False)
  - If (Condition: [boolTestWf])
    - Then:
      - Sequence: write_test_memory
        - write_test_memory (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[_dtaMemory], VirtualizedContainerService.HintSize=382,139, WorkflowViewState.IdRef=WriteRange_3, SheetName=base, StartingCell=A1, WorkbookPath=test_memory.xlsx)
    - Else:
      - Sequence: Else
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## fmw_download_process_memory_orch
**Path:** `wfs\_fmw\memory\fmw_download_process_memory_orch.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |
| _intExecutionDayOffset | `InArgument(x:Int32)` | In |

### Variables

| Name | Type |
|---|---|
| strMemoryFolder | `x:String` |
| jobInfo | `ui:CurrentJobInfo` |
| arrMemFiles | `s:String[]` |
| strStorageBucketName | `x:String` |
| boolDownloadMemFiles | `x:Boolean` |
| enBucketFiles | `scg:IEnumerable(ucas:StorageFileInfo)` |
| strMemFileNameBase | `x:String` |
| strMemoryType | `x:String` |
| intFilesToMergeDownload | `x:Int32` |
| strMemoryFile | `x:String` |
| strMemFileName | `x:String` |
| arrdtiMemoryDates | `s:DateTime[]` |
| intIdx | `x:Int32` |
| strOrchFilePath | `x:String` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
  - _intExecutionDayOffset (Annotation.AnnotationText=Default: 0. Always negative value, Type=InArgument(x:Int32))
- Sequence: fmw_download_process_memory_orch
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict_v3.1 workflow -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [{"base"}]
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=551,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_parameters_mem:
  - list_storage_bucket_process_files (Filter={x:Null}, TimeoutMS={x:Null}, Directory=\, VirtualizedContainerService.HintSize=551,57, WorkflowViewState.IdRef=ListStorageFiles_2, Recursive=True, StorageBucketName=[strStorageBucketName], ViewState=False; Result -> [enBucketFiles])
  - LogMessage: log_sb_and_files_count (Level=Info, Message=[String.Format("Storage bucket: {0} | Files: {1}", strStorageBucketName, enBucketFiles.Count)], ViewState=False)
  - Switch: sw_memory_type_assign_mem_files (TypeArguments=x:String, Expression=[strMemoryType], VirtualizedContainerService.HintSize=551,57, WorkflowViewState.IdRef=Switch`1_1, ViewState=False)
    - assign_mem_file_specs_daily:
    - assign_mem_file_specs_monthly:
    - Null (Key=yearly)
    - Null (Key=standalone)
  - assign_memory_files:
  - LogMessage: log_memory_files (Level=Info, Message=[String.Format("Memory files to update: {0}", arrMemFiles.Count.ToString)], ViewState=False)
  - If (Condition: [boolDownloadMemFiles])
    - Then:
      - Sequence: Then
        - ForEach<> in [arrMemFiles]
          - ActivityAction (TypeArguments=x:String)
            - Sequence: Body
    - Else:
      - Sequence: Else
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## fmw_update_process_memory
**Path:** `wfs\_fmw\memory\fmw_update_process_memory.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |

### Variables

| Name | Type |
|---|---|
| strWorktray | `x:String` |
| dtaWorktray | `sd:DataTable` |
| strMemoryFile | `x:String` |
| boolFileExists | `x:Boolean` |
| strMemoryTemplate | `x:String` |
| arrKeyColumns | `s:String[]` |
| arrMemoryFiles | `s:String[]` |
| arrdtiWorktrayDates | `s:DateTime[]` |
| intIdx | `x:Int32` |
| strMemoryFolder | `x:String` |
| strMemFileNameBase | `x:String` |
| strMemoryType | `x:String` |
| strMemFileName | `x:String` |
| strDateFormat | `x:String` |
| strDateFilterCol | `x:String` |
| arrWorktrayDates | `s:String[]` |
| jobInfo | `ui:CurrentJobInfo` |
| boolExistsWorktray | `x:Boolean` |
| dtiMemPeriod | `s:DateTime` |
| arrrowPeriodWorktray | `sd:DataRow[]` |
| dtaPeriodWorktray | `sd:DataTable` |
| dtaPeriodMem | `sd:DataTable` |
| arrrowPeriodMem | `sd:DataRow[]` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
- Sequence: fmw_update_process_memory
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict_v3.1 workflow -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [{"base"}]
  - get_current_job_info (VirtualizedContainerService.HintSize=683,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_parameters:
  - assign_wf_config_parameters:
  - file_exists_worktray (VirtualizedContainerService.HintSize=683,165, WorkflowViewState.IdRef=FileExistsX_1, Path=[strWorktray]; Exists -> [boolExistsWorktray])
  - If (Condition: [boolExistsWorktray])
    - Then:
      - Sequence: update_memory_with_worktray
        - read_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=615,120, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[strWorktray])
        - LogMessage: log_worktray_rows (Level=Info, Message=["Worktray rows: " + dtaWorktray.RowCount.ToString], ViewState=False)
        - Switch: sw_memory_type_assign_mem_files (TypeArguments=x:String, Expression=[strMemoryType], VirtualizedContainerService.HintSize=615,429, WorkflowViewState.IdRef=Switch`1_1, ViewState=True)
          - assign_mem_file_specs_daily:
          - assign_mem_file_specs_monthly:
          - assign_mem_file_specs_yearly:
          - assign_mem_file_specs_standalone:
        - assign_worktray_dates:
        - assign_memory_files:
        - LogMessage: log_mem_files_to_update (Level=Info, Message=[String.Format("Mem type: {0} | Memory files to update: {1}", strMemoryType, arrMemoryFiles.Count)], ViewState=False)
        - ForEach<> in [arrMemoryFiles]
          - ActivityAction (TypeArguments=x:String)
            - Sequence: Body
    - Else:
      - LogMessage: log_worktray_exists (Level=Warn, Message=["Worktray exists: " + boolExistsWorktray.ToString], ViewState=False)
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## fmw_upload_process_memory
**Path:** `wfs\_fmw\memory\fmw_upload_process_memory.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |

### Variables

| Name | Type |
|---|---|
| strMemoryFolder | `x:String` |
| jobInfo | `ui:CurrentJobInfo` |
| arrMemFiles | `s:String[]` |
| strStorageBucketName | `x:String` |
| intIdx | `x:Int32` |
| boolUploadMem | `x:Boolean` |
| strWorktray | `x:String` |
| boolExistsWorktray | `x:Boolean` |
| arrFilesToUpload | `s:String[]` |
| intMaxDaysToUpload | `x:Int32` |
| strDestination | `x:String` |
| dtiModifiedDate | `s:DateTime` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
- Sequence: fmw_upload_process_memory
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict_v3.1 workflow -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [{"base"}]
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=550,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=True)
  - assign_wf_config_parameters:
  - If (Condition: [boolUploadMem])
    - Then:
      - Sequence: upload_files_to_orch
        - file_exists_worktray (VirtualizedContainerService.HintSize=514,57, WorkflowViewState.IdRef=FileExistsX_2, Path=[strWorktray], ViewState=False; Exists -> [boolExistsWorktray])
        - assign_files_to_upload:
        - LogMessage: log_memory_files (Level=Info, Message=[String.Format("Memory files: {0} | Worktray exists: {1} | Upload files: {2}", arrMemFiles.Count.ToString, boolExistsWorktray, boolUploadMem)], ViewState=True)
        - ForEach<> in [arrFilesToUpload]
          - ActivityAction (TypeArguments=x:String)
            - Sequence: fe_file_upload_to_orch
    - Else:
      - Sequence: log_upload_disable
        - LogMessage: log_upload_disable (Level=Warn, Message=["Upload memory files is disabled by config"], ViewState=False)
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## office365_send_emails
**Path:** `wfs\_fmw\office365_send_email.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strEmailAddress | `InArgument(x:String)` | In |
| _strTenantId | `InArgument(x:String)` | In |
| _strAppId | `InArgument(x:String)` | In |
| _strSubject | `InArgument(x:String)` | In |
| _arrAttachmentsFiles | `InArgument(s:String[])` | In |
| _strEmailBody | `InArgument(x:String)` | In |
| _arrTo | `InArgument(s:String[])` | In |
| _arrCc | `InArgument(s:String[])` | In |
| _arrBcc | `InArgument(s:String[])` | In |

### Variables

| Name | Type |
|---|---|
| boolTestWf | `x:Boolean` |
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Members
  - _strEmailAddress (Type=InArgument(x:String))
  - _strTenantId (Type=InArgument(x:String))
  - _strAppId (Type=InArgument(x:String))
  - _strSubject (Type=InArgument(x:String))
  - _arrAttachmentsFiles (Type=InArgument(s:String[]))
  - _strEmailBody (Type=InArgument(x:String))
  - _arrTo (Type=InArgument(s:String[]))
  - _arrCc (Type=InArgument(s:String[]))
  - _arrBcc (Type=InArgument(s:String[]))
- Sequence: office365_send_emails
  - If (Condition: [_strEmailAddress is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - assign_test_args:
        - assign_test_args_default:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=470,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - If (Condition: [boolTestWf])
    - Then:
      - Sequence: log_email_info
        - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("Sending email -> Subject: {0}", _strSubject)], ViewState=False)
        - LogMessage: log_from_email (Level=Info, Message=[String.Format("From: {0}", _strEmailAddress)], ViewState=False)
        - LogMessage: log_recipients (Level=Info, Message=[String.Format("Recipients -> To: {0} | Cc: {1} | Bcc: {2}", String.Join(";", _arrTo), String.Join(";", _arrCc), String.Join(";", _arrBcc))], ViewState=True)
    - Else:
      - Sequence: Sequence
  - office365_scope (ApplicationSecret={x:Null}, BrowserItemFriendlyName={x:Null}, BrowserItemFullPath={x:Null}, BrowserItemId={x:Null}, BrowserParentItemId={x:Null}, BrowserRuntimeItemFriendlyName={x:Null}, BrowserRuntimeItemFullPath={x:Null}, BrowserRuntimeItemId={x:Null}, BrowserRuntimeParentItemId={x:Null}, CertificateAsBase64={x:Null}, CertificatePassword={x:Null}, ConnectionAccountName={x:Null}, ConnectionId={x:Null}, Connector={x:Null}, ContinueOnError={x:Null}, ImpersonatedUserEmailAddress={x:Null}, ManualRuntimeItemFullPath={x:Null}, SecureApplicationSecret={x:Null}, SecurePassword={x:Null}, Timeout={x:Null}, Account=Please select an account., Annotation.AnnotationText=Authentication type = Interactive Token
OAuthApplication    = Custom, ApplicationId=[_strAppId], AuthenticationType=InteractiveToken, ConfigLocation=PropertiesPanel, Environment=Global, VirtualizedContainerService.HintSize=470,733, WorkflowViewState.IdRef=Office365ApplicationScope_2, OAuthApplication=Custom, Password=a, RuntimeItemInputMode=Browse, TenantId=[_strTenantId], UseConnectionService=False, Username=[_strEmailAddress], AuthenticationScopes=mail.read, ViewState=True)
    - Office365ApplicationScope.Body
      - ActivityAction (TypeArguments=x:Object)
        - Sequence: send_office_365_emails
          - send_email_office365 (ContinueOnError={x:Null}, From={x:Null}, ReplyTo={x:Null}, Account=[_strEmailAddress], AttachmentsCollection=[_arrAttachmentsFiles], AuthScopesInvalid=False, Bcc=[_arrBcc], Body=[_strEmailBody], Cc=[_arrCc], VirtualizedContainerService.HintSize=434,374, WorkflowViewState.IdRef=SendMail_1, Importance=Normal, IsBodyHTML=True, IsDraft=False, Subject=[_strSubject], To=[_arrTo], ViewState=True)
  - If (Condition: [boolTestWf])
    - Then:
      - LogMessage: log_wf_finished (Level=Info, Message=["The email was sent successfully"], ViewState=False)
    - Else:
      - Sequence: Sequence
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## save_exception_screenshot
**Path:** `wfs\_fmw\save_exception_screenshot.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |
| _intRowIdx | `InArgument(x:Int32)` | In |
| _strExcType | `InArgument(x:String)` | In |
| _intTry | `InArgument(x:Int32)` | In |
| _boolSaveScreenshot | `InArgument(x:Boolean)` | In |

### Variables

| Name | Type |
|---|---|
| strExceptionFileTemplate | `x:String` |
| strExceptionPath | `x:String` |
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
  - _intRowIdx (Type=InArgument(x:Int32))
  - _strExcType (Type=InArgument(x:String))
  - _intTry (Type=InArgument(x:Int32))
  - _boolSaveScreenshot (Type=InArgument(x:Boolean))
- Sequence: save_exception_screenshot
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict_v3.1 workflow -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [{"base"}]
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=515,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - assign_wf_config_parameters:
  - If (Condition: [_boolSaveScreenshot])
    - Then:
      - Sequence: execute_wf
        - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
        - assign_exc_screenshot_path:
        - InvokeWorkflowFile: inv_wfs\_fmw\utils_take_screenshot.xaml -> wfs\_fmw\utils_take_screenshot.xaml
          - in _strScreenshotPath: [strExceptionPath]
        - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)
    - Else:
      - Sequence: Else

## utils_close_apps
**Path:** `wfs\_fmw\utils_close_apps.xaml`

### Arguments
_No arguments found_
### Variables

| Name | Type |
|---|---|
| arrProcessesToKill | `s:String[]` |
| intTimeoutClose | `x:Int32` |
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Sequence: utils_close_apps
  - get_current_job_info (VirtualizedContainerService.HintSize=479,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_test_arguments:
  - InvokeWorkflowFile: inv_utils_kill_processes.xaml -> wfs\_fmw\utils_kill_processes.xaml
    - in _arrProcessesToKill: [arrProcessesToKill]
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_dta_to_html
**Path:** `wfs\_fmw\utils_dta_to_html.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dtaTable | `InArgument(sd:DataTable)` | In |
| _strHeaderColor | `InArgument(x:String)` | In |
| _strStripesColor | `InArgument(x:String)` | In |
| _strBorderColor | `InArgument(x:String)` | In |
| _boolColBorders | `InArgument(x:Boolean)` | In |
| _intTableWidth | `InArgument(x:Int32)` | In |
| _strFont | `InArgument(x:String)` | In |
| _strHeaderFontColor | `InArgument(x:String)` | In |
| _strFirstColAlignment | `InArgument(x:String)` | In |
| _strColAlignnment | `InArgument(x:String)` | In |
| _boolCenterTable | `InArgument(x:Boolean)` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strHtmlTable | `OutArgument(x:String)` | Out |

### Variables

| Name | Type |
|---|---|
| strTableFormat | `x:String` |
| arrHeaders | `s:String[]` |
| strHeaderFormat | `x:String` |
| strHeaderData | `x:String` |
| strBodyData | `x:String` |
| boolTestWf | `x:Boolean` |
| jobInfo | `ui:CurrentJobInfo` |
| strEnhancedStyles | `x:String` |
| intHeaderIndex | `x:Int32` |
| strTextAlign | `x:String` |
| strRowData | `x:String` |
| intRowIndex | `x:Int32` |
| arrRow | `s:Object[]` |
| intItemIndex | `x:Int32` |
| strTextAlign | `x:String` |

### Logic
- Members
  - _dtaTable (Type=InArgument(sd:DataTable))
  - _strHeaderColor (Type=InArgument(x:String))
  - _strStripesColor (Type=InArgument(x:String))
  - _strBorderColor (Type=InArgument(x:String))
  - _boolColBorders (Type=InArgument(x:Boolean))
  - _strHtmlTable (Type=OutArgument(x:String))
  - _intTableWidth (Type=InArgument(x:Int32))
  - _strFont (Type=InArgument(x:String))
  - _strHeaderFontColor (Type=InArgument(x:String))
  - _strFirstColAlignment (Type=InArgument(x:String))
  - _strColAlignnment (Type=InArgument(x:String))
  - _boolCenterTable (Type=InArgument(x:Boolean))
- Sequence: utils_dta_to_html
  - If (Condition: [_dtaTable is Nothing])
    - Then:
      - Sequence: Then
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - build_test_dta (Annotation.AnnotationText=Only for wf test, DataTable=[_dtaTable], VirtualizedContainerService.HintSize=479,123, WorkflowViewState.IdRef=BuildDataTable_1, TableInfo=<NewDataSet>
  <xs:schema id="NewDataSet" xmlns="" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata">
    <xs:element name="NewDataSet" msdata:IsDataSet="true" msdata:MainDataTable="TableName" msdata:UseCurrentLocale="true">
      <xs:complexType>
        <xs:choice minOccurs="0" maxOccurs="unbounded">
          <xs:element name="TableName">
            <xs:complexType>
              <xs:sequence>
                <xs:element name="PARAMETER" msdata:Caption="" minOccurs="0">
                  <xs:simpleType>
                    <xs:restriction base="xs:string">
                      <xs:maxLength value="100" />
                    </xs:restriction>
                  </xs:simpleType>
                </xs:element>
                <xs:element name="VALUE" msdata:Caption="" type="xs:string" minOccurs="0" />
              </xs:sequence>
            </xs:complexType>
          </xs:element>
        </xs:choice>
      </xs:complexType>
    </xs:element>
  </xs:schema>
  <TableName>
    <PARAMETER>param 1</PARAMETER>
    <VALUE>38%</VALUE>
  </TableName>
  <TableName>
    <PARAMETER>param 2</PARAMETER>
    <VALUE>123</VALUE>
  </TableName>
  <TableName>
    <PARAMETER>param 3</PARAMETER>
    <VALUE>FALSE</VALUE>
  </TableName>
</NewDataSet>, ViewState=True)
        - assign_test_args:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=585,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - If (Condition: [_boolCenterTable])
    - Then:
      - Assign:  = 
    - Else:
      - Assign:  = 
  - Assign:  = 
  - Sequence: process_headers
    - header_info:
    - ForEach<> in [arrHeaders]
      - ActivityAction (TypeArguments=x:String)
        - Sequence: fe_header
          - If (Condition: [intHeaderIndex = 0])
            - Then:
            - Else:
          - If (Condition: [_boolColBorders])
            - Then:
            - Else:
    - Assign:  = 
  - Sequence: process_body
    - Assign:  = 
    - fe_row_add_to_body (ColumnNames={x:Null}, CurrentIndex=[intRowIndex], DataTable=[_dtaTable], VirtualizedContainerService.HintSize=551,2103, WorkflowViewState.IdRef=ForEachRow_1)
      - ForEachRow.Body
        - ActivityAction (TypeArguments=sd:DataRow)
          - Sequence: fe_row_add_to_body
            - Assign:  = 
            - If (Condition: [intRowIndex mod 2 = 1])
              - Then:
              - Else:
            - Assign:  = 
            - ForEach<> in [arrRow]
            - Assign:  = 
            - Assign:  = 
    - Assign:  = 
  - Assign:  = 
  - If (Condition: [boolTestWf])
    - Then:
      - write_test_table (File={x:Null}, Annotation.AnnotationText=Only for Test, FileName=_test_table.html, VirtualizedContainerService.HintSize=416,164, WorkflowViewState.IdRef=WriteTextFile_1, ViewState=True; Text -> [_strHtmlTable])
    - Else:
      - Sequence: Else
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_kill_processes
**Path:** `wfs\_fmw\utils_kill_processes.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _arrProcessesToKill | `InArgument(s:String[])` | In |

### Variables

| Name | Type |
|---|---|
| lstProcesses | `sco:Collection(sd:Process)` |
| boolLogActiveProcesses | `x:Boolean` |
| jobInfo | `ui:CurrentJobInfo` |
| boolProcessToKill | `x:Boolean` |

### Logic
- Members
  - _arrProcessesToKill (Type=InArgument(s:String[]))
- Sequence: utils_kill_processes
  - If (Condition: [_arrProcessesToKill is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - assign_wf_test_arguments:
  - get_current_job_info (VirtualizedContainerService.HintSize=634,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - LogMessage: log_processes_to_kill (Level=Info, Message=["Processes to kill: " + String.Join("; ", _arrProcessesToKill)], ViewState=False)
  - get_processes (ContinueOnError={x:Null}, VirtualizedContainerService.HintSize=634,57, WorkflowViewState.IdRef=GetProcesses_1, Processes=[lstProcesses])
  - If (Condition: [boolLogActiveProcesses])
    - Then:
      - LogMessage: log_all_processes (Level=Trace, Message=[String.Join(vbNewLine, lstProcesses.Select(function (prc) prc.ProcessName))])
  - ForEach<> in [lstProcesses]
    - ActivityAction (TypeArguments=sd:Process)
      - Sequence: fe_process_check_if_it_must_be_killed
        - assign_process_to_kill:
        - If (Condition: [boolProcessToKill])
          - Then:
            - Sequence: kill_process
          - Else:
            - Sequence: Else
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_read_config_dict
**Path:** `wfs\_fmw\utils_read_config_dict.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strConfigFile | `InArgument(x:String)` | In |
| _strarrConfigSheets | `InArgument(s:String[])` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfiguration | `OutArgument(scg:Dictionary(x:String, x:Object))` | Out |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |
| dtaConfigTable | `sd:DataTable` |
| intExeEnv | `x:Int32` |
| strKeyEnv | `x:String` |
| strKeyMainFolder | `x:String` |
| arrConfigCols | `s:String[]` |
| boolEnvColExists | `x:Boolean` |
| strColumnEnv | `x:String` |
| arrConfigKeys | `s:String[]` |
| boolReplaceUser | `x:Boolean` |
| strKey | `x:String` |
| strValue | `x:String` |
| intEnv | `x:Int32` |
| strValue | `x:String` |
| arrPathParts | `s:String[]` |
| intUserIdx | `x:Int32` |
| strSessionUser | `x:String` |
| strNewValue | `x:String` |
| strFormatKey | `x:String` |
| strFormatValue | `x:String` |
| strTagKey | `x:String` |
| strTagValue | `x:String` |

### Logic
- Members
  - _dicConfiguration (Type=OutArgument(scg:Dictionary(x:String, x:Object)))
  - _strConfigFile (Type=InArgument(x:String))
  - _strarrConfigSheets (Type=InArgument(s:String[]))
- Sequence: utils_read_config_dict
  - get_current_job_info (VirtualizedContainerService.HintSize=585,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - Sequence: create_dict_config
    - assign_wf_parameters:
    - read_config_file (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaConfigTable], VirtualizedContainerService.HintSize=551,120, WorkflowViewState.IdRef=ReadRange_3, SheetName=base, WorkbookPath=[_strConfigFile])
    - assign_execution_environment:
    - LogMessage: log_exe_env (Level=Trace, Message=["Execution environment: " + intExeEnv.ToString], ViewState=False)
    - check_if_env_column_exists:
    - remove_empty_rows:
    - fer_format_key_and_env (ColumnNames={x:Null}, CurrentIndex={x:Null}, DataTable=[dtaConfigTable], VirtualizedContainerService.HintSize=551,1012, WorkflowViewState.IdRef=ForEachRow_3, ViewState=True)
      - ForEachRow.Body
        - ActivityAction (TypeArguments=sd:DataRow)
          - Sequence: Body
            - assign_row_info:
            - update_config_parameters:
            - If (Condition: [boolEnvColExists])
              - Then:
              - Else:
    - If (Condition: [boolEnvColExists])
      - Then:
        - Sequence: Then
          - sort_params_by_env (ColumnIndex={x:Null}, DataColumn={x:Null}, Annotation.AnnotationText=Order = Ascending, ColumnName=env, DataTable=[dtaConfigTable], VirtualizedContainerService.HintSize=479,88, WorkflowViewState.IdRef=SortDataTable_3, Order=Ascending, SortOrder=Ascending, ViewState=False; OutputDataTable -> [dtaConfigTable])
          - select_filter_env_parameters:
      - Else:
        - Sequence: Else
    - convert_dtaConfig_to_dicConfig:
    - ForEach<> in [arrConfigKeys]
      - ActivityAction (TypeArguments=x:String)
        - Sequence: fe_key_format_value_with_other_keys
          - assign_value:
          - If (Condition: [boolReplaceUser and ((strValue.ToLower.Contains("\users\") or strValue.ToLower.Contains("\usuarios\")) and not (strValue.ToLower.Contains("\public\") or strValue.ToLower.Contains("\acceso público\")))])
            - Then:
            - Else:
          - If (Condition: [_dicConfiguration.Keys.Any(function (key_2) strValue.Contains(key_2+"+"))])
            - Then:
            - Else:
          - ForEach<> in [Enumerable.Range(1,5)]
            - ActivityAction (TypeArguments=x:Object)
    - comment_out_log_config (VirtualizedContainerService.HintSize=551,210, WorkflowViewState.IdRef=CommentOut_1)
      - CommentOut.Body
        - Sequence: Ignored Activities
          - Sequence: test_log_config
            - LogMessage: log_main_folder (Level=Trace, Message=["Process main folder: " + _dicConfiguration(strKeyMainFolder).ToString], ViewState=False)
            - ForEach<> in [_dicConfiguration.keys]
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_take_screenshot
**Path:** `wfs\_fmw\utils_take_screenshot.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strScreenshotPath | `InArgument(x:String)` | In |

### Variables

| Name | Type |
|---|---|
| imgScreenshot | `ui:Image` |
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Members
  - _strScreenshotPath (Type=InArgument(x:String))
- Sequence: utils_take_screenshot
  - get_current_job_info (VirtualizedContainerService.HintSize=416,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - create_screenshot_folder (ContinueOnError={x:Null}, VirtualizedContainerService.HintSize=416,112, WorkflowViewState.IdRef=CreateDirectory_1, Path=[Directory.GetParent(_strScreenshotPath).ToString]; Output -> {x:Null})
  - take_screenshot (WaitBefore={x:Null}, VirtualizedContainerService.HintSize=416,139, WorkflowViewState.IdRef=TakeScreenshot_2, Screenshot=[imgScreenshot])
  - LogMessage: log_screenshot_saved_as (Level=Info, Message=["Screenshot saved as: " + _strScreenshotPath], ViewState=False)
  - save_image (FileName=[_strScreenshotPath], VirtualizedContainerService.HintSize=416,165, WorkflowViewState.IdRef=SaveImage_1, Image=[imgScreenshot])
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## p001_build_worktray
**Path:** `wfs\_templates\p001_build_worktray.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |
| _intExecutionDayOffset | `InArgument(x:Int32)` | In |

### Variables

| Name | Type |
|---|---|
| strWorktray | `x:String` |
| dtaWorktray | `sd:DataTable` |
| intRowIdx | `x:Int32` |
| strInputFile | `x:String` |
| dtaInputFile | `sd:DataTable` |
| strWorktrayTemplate | `x:String` |
| dtaMemory | `sd:DataTable` |
| jobInfo | `ui:CurrentJobInfo` |
| arrMemFile | `s:String[]` |
| drowWt | `sd:DataRow` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
  - _intExecutionDayOffset (Annotation.AnnotationText=Default: 0. Always negative value, Type=InArgument(x:Int32))
- Sequence: p001_build_worktray
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - assign_wf_test_arguments:
  - get_current_job_info (VirtualizedContainerService.HintSize=515,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_parameters:
  - InvokeWorkflowFile: inv_fmw_concat_process_memory.xaml -> wfs\_fmw\memory\fmw_concat_process_memory.xaml
    - in _dicConfig: [_dicConfig]
    - out _dtaMemory: [dtaMemory]
    - out _arrMemoryFiles: [arrMemFile]
    - in _intExecutionDayOffset: [_intExecutionDayOffset]
  - read_input_file (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaInputFile], VirtualizedContainerService.HintSize=515,120, WorkflowViewState.IdRef=ReadRange_2, SheetName=base, WorkbookPath=[strInputFile])
  - LogMessage: log_rows_in_input_file (Level=Info, Message=["Rows in input file: " + dtaInputFile.RowCount.ToString], ViewState=False)
  - read_worktray_template (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=515,120, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[strWorktrayTemplate])
  - fer_add_to_worktray (ColumnNames={x:Null}, CurrentIndex=[intRowIdx], DataTable=[dtaInputFile], VirtualizedContainerService.HintSize=515,916, WorkflowViewState.IdRef=ForEachRow_1)
    - ForEachRow.Body
      - ActivityAction (TypeArguments=sd:DataRow)
        - Sequence: fer_add_to_worktray
          - assign_row_info:
          - comment_fmw_instruction (VirtualizedContainerService.HintSize=479,107, WorkflowViewState.IdRef=Comment_2; Text -> // Filter, clean and format input rows to add them to the worktray)
          - assign_worktray_row:
          - add_data_row_to_worktray (DataRow={x:Null}, ArrayRow=[drowWt.ItemArray], DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=479,209, WorkflowViewState.IdRef=AddDataRow_1)
  - LogMessage: log_rows_in_worktray (Level=Info, Message=["Rows in worktray: " + dtaWorktray.RowCount.ToString], ViewState=False)
  - copy_worktray_template_to_worktray_path (ContinueOnError={x:Null}, Destination=[strWorktray], VirtualizedContainerService.HintSize=515,57, WorkflowViewState.IdRef=CopyFile_3, Overwrite=True, Path=[strWorktrayTemplate], ViewState=False)
  - write/update_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=515,148, WorkflowViewState.IdRef=WriteRange_2, SheetName=base, StartingCell=A1, WorkbookPath=[strWorktray])
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## p001_main_state_wf_2.0
**Path:** `wfs\_templates\p001_main_state_wf_2.0.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |

### Variables

| Name | Type |
|---|---|
| strWorktray | `x:String` |
| dtaWorktray | `sd:DataTable` |
| intCurrentIdx | `x:Int32` |
| intRowsToProcess | `x:Int32` |
| intMaxRowTries | `x:Int32` |
| jobInfo | `ui:CurrentJobInfo` |
| intMaxRowsToProcess | `x:Int32` |
| boolTakeScreenshotSysExc | `x:Boolean` |
| boolTakeScreenshotBE | `x:Boolean` |
| listrowsWtToProcess | `scg:List(sd:DataRow)` |
| strPreviousStateCol | `x:String` |
| strCurrentSateCol | `x:String` |
| strSuccess | `x:String` |
| strObs | `x:String` |
| intRowIdx | `x:Int32` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
- Sequence: p001_main_state_wf_2.0
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=587,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("----- Starting: {0} -----", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_config_parameters:
  - read_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=587,120, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[strWorktray])
  - assign_wf_loop_parameters:
  - assign_wf_filter_parameters:
  - LogMessage: log_rows_to_process (Level=Info, Message=[String.Format("Rows to process: {0}/{1}",  intRowsToProcess, dtaWorktray.RowCount)], ViewState=False)
  - ForEach<> in [listrowsWtToProcess]
    - ActivityAction (TypeArguments=sd:DataRow)
      - Sequence: fe_process_row
        - If (Condition: [intMaxRowsToProcess >= 0  AndAlso intCurrentIdx > intMaxRowsToProcess])
          - Then:
            - Sequence: log_and_break
          - Else:
            - Sequence: Else
        - assign_row_info:
        - LogMessage: log_row_info (Level=Info, Message=[String.Format("[{0}/{1}] {2}. Add row info here", intCurrentIdx+1, intRowsToProcess, intRowIdx+1)], ViewState=False)
        - ForEach<> in [Enumerable.Range(1, intMaxRowTries)]
          - ActivityAction (TypeArguments=x:Int32)
            - Sequence: fe_try_process_row
        - assign_row_outcome:
        - write/update_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=551,139, WorkflowViewState.IdRef=WriteRange_1, SheetName=base, StartingCell=A1, WorkbookPath=[strWorktray])
  - write/update_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=587,148, WorkflowViewState.IdRef=WriteRange_2, SheetName=base, StartingCell=A1, WorkbookPath=[strWorktray])
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## p001_main_state_wf
**Path:** `wfs\_templates\p001_main_state_wf_old.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |

### Variables

| Name | Type |
|---|---|
| strWorktray | `x:String` |
| dtaWorktray | `sd:DataTable` |
| intRowIdx | `x:Int32` |
| intCurrentIdx | `x:Int32` |
| intRowsToProcess | `x:Int32` |
| intMaxRowTries | `x:Int32` |
| jobInfo | `ui:CurrentJobInfo` |
| intMaxRowsToProcess | `x:Int32` |
| boolTakeScreenshotSysExc | `x:Boolean` |
| boolTakeScreenshotBE | `x:Boolean` |
| strSuccess | `x:String` |
| strObs | `x:String` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
- Sequence: p001_main_state_wf
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=577,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("----- Starting: {0} -----", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_config_parameters:
  - read_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=577,120, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[strWorktray])
  - assign_wf_loop_parameters:
  - LogMessage: log_rows_to_process (Level=Info, Message=[String.Format("Rows to process: {0}/{1}",  intRowsToProcess, dtaWorktray.RowCount)], ViewState=False)
  - fer_process_row (ColumnNames={x:Null}, CurrentIndex=[intRowIdx], DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=577,2993, WorkflowViewState.IdRef=ForEachRow_1)
    - ForEachRow.Body
      - ActivityAction (TypeArguments=sd:DataRow)
        - Sequence: fer_process_row
          - If (Condition: [row("outcome_state_1").ToString.ToUpper <> "TRUE"])
            - Then:
            - Else:
          - If (Condition: [intCurrentIdx > intMaxRowsToProcess and intMaxRowsToProcess >= 0])
            - Then:
            - Else:
          - assign_row_info:
          - LogMessage: log_row_info (Level=Info, Message=[String.Format("[{0}/{1}] {2}. Add row info here", intCurrentIdx, intRowsToProcess, intRowIdx+1)], ViewState=False)
          - ForEach<> in [Enumerable.Range(1, intMaxRowTries)]
            - ActivityAction (TypeArguments=x:Int32)
          - assign_row_outcome:
          - write/update_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=541,139, WorkflowViewState.IdRef=WriteRange_1, SheetName=base, StartingCell=A1, WorkbookPath=[strWorktray])
  - write/update_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=577,148, WorkflowViewState.IdRef=WriteRange_2, SheetName=base, StartingCell=A1, WorkbookPath=[strWorktray])
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## p001_send_execution_report
**Path:** `wfs\_templates\p001_send_execution_report.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |
| _boolSendToClient | `InArgument(x:Boolean)` | In |

### Variables

| Name | Type |
|---|---|
| strWorktray | `x:String` |
| dtaWorktray | `sd:DataTable` |
| dtaExeReport | `sd:DataTable` |
| strHtmlExeReport | `x:String` |
| strExeReportFile | `x:String` |
| strTransformColsExeReport | `x:String` |
| strExeReportTemplate | `x:String` |
| dtiExecutionDate | `s:DateTime` |
| jobInfo | `ui:CurrentJobInfo` |
| strHeaderBkgColor | `x:String` |
| strHeaderFontColor | `x:String` |
| strStripesColor | `x:String` |
| intTableWidth | `x:Int32` |
| strParameter | `x:String` |
| strTotal | `x:String` |
| strSuccess | `x:String` |
| strExceptions | `x:String` |
| strParameter | `x:String` |
| strTotal | `x:String` |
| strSuccess | `x:String` |
| strExceptions | `x:String` |
| strSubject | `x:String` |
| strBodyFile | `x:String` |
| arrAttachments | `s:String[]` |
| arrRecipients | `s:String[]` |
| arrBodyFields | `s:String[]` |
| arrWrapperFields | `s:String[]` |
| arrRecipientsCC | `s:String[]` |
| strRobotName | `x:String` |
| boolSendEmail | `x:Boolean` |
| strEmailWrapper | `x:String` |
| intTimeoutSendEmail | `x:Int32` |
| strRecipientsCol | `x:String` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
  - _boolSendToClient (Type=InArgument(x:Boolean))
- Sequence: p001_send_execution_report
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=581,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_arguments:
  - read_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=581,120, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[strWorktray])
  - InvokeWorkflowFile: inv_utils_transform_dta_columns.xaml -> wfs\_utils\utils_transform_dta_columns.xaml
    - in _strTransformColsFile: [strTransformColsExeReport]
    - in _dtaIn: [dtaWorktray]
    - out _dtaOut: [dtaExeReport]
  - copy_exe_report_template_to_exe_report_path (ContinueOnError={x:Null}, Destination=[strExeReportFile], VirtualizedContainerService.HintSize=581,84, WorkflowViewState.IdRef=CopyFile_1, Overwrite=True, Path=[strExeReportTemplate], ViewState=False)
  - write_execution_report (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaExeReport], VirtualizedContainerService.HintSize=581,148, WorkflowViewState.IdRef=WriteRange_1, SheetName=base, StartingCell=A1, WorkbookPath=[strExeReportFile])
  - Sequence: build_exe_report
    - Sequence: build_summary_execution_report
      - build_dtaExeReport (DataTable=[dtaExeReport], VirtualizedContainerService.HintSize=513,92, WorkflowViewState.IdRef=BuildDataTable_1, TableInfo=<NewDataSet>
  <xs:schema id="NewDataSet" xmlns="" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata">
    <xs:element name="NewDataSet" msdata:IsDataSet="true" msdata:MainDataTable="TableName" msdata:UseCurrentLocale="true">
      <xs:complexType>
        <xs:choice minOccurs="0" maxOccurs="unbounded">
          <xs:element name="TableName">
            <xs:complexType>
              <xs:sequence>
                <xs:element name="PARAMETER" msdata:Caption="" type="xs:string" minOccurs="0" />
                <xs:element name="SUCCESS" msdata:Caption="" type="xs:string" minOccurs="0" />
                <xs:element name="EXCEPTION" msdata:Caption="" type="xs:string" minOccurs="0" />
                <xs:element name="TOTAL" msdata:Caption="" type="xs:string" minOccurs="0" />
              </xs:sequence>
            </xs:complexType>
          </xs:element>
        </xs:choice>
      </xs:complexType>
    </xs:element>
  </xs:schema>
</NewDataSet>)
      - Sequence: row_parameter_1
        - assign_parameter_and_value:
        - add_row_to_dtaExeReport (DataRow={x:Null}, ArrayRow=[{strParameter, strSuccess, strExceptions, strTotal}], DataTable=[dtaExeReport], VirtualizedContainerService.HintSize=479,209, WorkflowViewState.IdRef=AddDataRow_1)
      - Sequence: row_parameter_2
        - assign_parameter_and_value:
        - add_row_to_dtaExeReport (DataRow={x:Null}, ArrayRow=[{strParameter, strSuccess, strExceptions, strTotal}], DataTable=[dtaExeReport], VirtualizedContainerService.HintSize=479,209, WorkflowViewState.IdRef=AddDataRow_13)
    - assign_html_table_colors:
    - InvokeWorkflowFile: inv_utils_dta_to_html workflow -> wfs\_fmw\utils_dta_to_html.xaml
      - in _dtaTable: [dtaExeReport]
      - out _strHtmlTable: [strHtmlExeReport]
      - in _strHeaderColor: [strHeaderBkgColor]
      - in _strHeaderFontColor: [strHeaderFontColor]
      - in _strStripesColor: [strStripesColor]
      - in _strBorderColor: #000000
      - in _boolColBorders: False
      - in _intTableWidth: [intTableWidth]
      - in _strFont: Helvetica
      - in _strFirstColAlignment: left
      - in _strColAlignnment: center
      - in _boolCenterTable: False
  - Sequence: send_email_exe_report
    - assign_email_parameters_exe_report:
    - InvokeWorkflowFile: inv_fmw_send_email -> wfs\_fmw\fmw_send_email.xaml
      - in _strSubject: [strSubject]
      - in _arrBodyFields: [arrBodyFields]
      - in _arrRecipients: [arrRecipients]
      - in _strSenderName: [strRobotName]
      - in _arrAttachmentsFiles: [arrAttachments]
      - in _strEmailWrapperFile: [strEmailWrapper]
      - in _strBodyMsgFile: [strBodyFile]
      - in _arrExtraWrapperFields: [arrWrapperFields]
      - in _intMaxTimeToSendEmail: [intTimeoutSendEmail]
      - in _arrRecipientsCC: [arrRecipientsCC]
      - in _strRecipientsColumn: [strRecipientsCol]
      - in _dicConfig: [_dicConfig]
      - in _boolSendToClient: [_boolSendToClient]
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## process_row_template
**Path:** `wfs\_templates\process_row_template.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |
| _drowWt | `InArgument(sd:DataRow)` | In |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |
| intRowIdx | `x:Int32` |
| dtaWorktray | `sd:DataTable` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
  - _drowWt (Type=InArgument(sd:DataRow))
- Sequence: process_row_template
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - read_worktray (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaWorktray], VirtualizedContainerService.HintSize=479,57, WorkflowViewState.IdRef=ReadRange_2, SheetName=base, WorkbookPath=[_dicConfig("WORKTRAY_FILE").ToString], ViewState=False)
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=479,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_config_parameters:
  - assign_row_parameters:
  - comment_below_this_message (VirtualizedContainerService.HintSize=479,88, WorkflowViewState.IdRef=Comment_1; Text -> // Code below this message)
  - assign_row_outcome:
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## workflow_basic_template
**Path:** `wfs\_templates\workflow_basic_template.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
- Sequence: workflow_basic_template
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=434,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_config_parameters:
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## test_wf
**Path:** `wfs\_test\test_wf.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dicConfig | `InArgument(scg:Dictionary(x:String, x:Object))` | In |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Members
  - _dicConfig (Type=InArgument(scg:Dictionary(x:String, x:Object)))
- Sequence: test_wf
  - If (Condition: [_dicConfig is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - InvokeWorkflowFile: inv_utils_read_config_dict.xaml -> wfs\_fmw\utils_read_config_dict.xaml
          - out _dicConfiguration: [_dicConfig]
          - in _strConfigFile: configuration.xlsx
          - in _strarrConfigSheets: [New String(){"base"}]
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=434,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_test_parameters:
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## chrome_save_file_as
**Path:** `wfs\_utils\chrome_save_file_as.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strFilePath | `InArgument(x:String)` | In |
| _intWaitingSeconds | `InArgument(x:Int32)` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _boolFileDownloaded | `OutArgument(x:Boolean)` | Out |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Members
  - _boolFileDownloaded (Type=OutArgument(x:Boolean))
  - _strFilePath (Type=InArgument(x:String))
  - _intWaitingSeconds (Type=InArgument(x:Int32))
- Sequence: chrome_save_file_as
  - If (Condition: [_strFilePath is Nothing])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Sequence
  - get_current_job_info (VirtualizedContainerService.HintSize=494,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - LogMessage: log_file_path (Level=Info, Message=["File path: "+_strFilePath], ViewState=False)
  - check_app_state_save_as_window_open (EnableIfExists=False, VirtualizedContainerService.HintSize=494,426, WorkflowViewState.IdRef=NCheckState_2, Timeout=30, Version=V4)
  - use_app_chrome_save_as (AttachMode=ByInstance, VirtualizedContainerService.HintSize=494,348, WorkflowViewState.IdRef=NApplicationCard_1, ScopeGuid=a04be482-c4ae-42f1-afba-12111b51e416, Timeout=10, Version=V2)
    - NApplicationCard.Body
      - ActivityAction (TypeArguments=x:Object)
        - Sequence: download_file
          - type_file_name (ActivateBefore=True, ClickBeforeMode=None, EmptyFieldMode=SingleLine, VirtualizedContainerService.HintSize=388,218, WorkflowViewState.IdRef=NTypeInto_1, InteractionMode=Simulate, ScopeIdentifier=a04be482-c4ae-42f1-afba-12111b51e416, Timeout=3, Version=V4; Text -> [_strFilePath])
          - delete_previous_file_if_exists (VirtualizedContainerService.HintSize=388,80, WorkflowViewState.IdRef=DeleteFileX_7, Path=[_strFilePath])
          - click_save (ActivateBefore=True, ClickType=Single, VirtualizedContainerService.HintSize=388,157, WorkflowViewState.IdRef=NClick_1, InteractionMode=Simulate, KeyModifiers=None, MouseButton=Left, ScopeIdentifier=a04be482-c4ae-42f1-afba-12111b51e416, Timeout=3, Version=V4)
  - create_file_parent_folder (ContinueOnError={x:Null}, VirtualizedContainerService.HintSize=494,112, WorkflowViewState.IdRef=CreateDirectory_2, Path=[Directory.GetParent(_strFilePath).ToString]; Output -> {x:Null})
  - LogMessage: log_downloading_file (Level=Info, Message=["Downloading file ..."], ViewState=False)
  - ForEach<> in [Enumerable.Range(0, _intWaitingSeconds*10)]
    - ActivityAction (TypeArguments=x:Object)
      - Sequence: check_file_exists_wait
        - path_exists_file (Resource={x:Null}, VirtualizedContainerService.HintSize=418,147, WorkflowViewState.IdRef=PathExists_3, Path=[_strFilePath], PathType=File, ViewState=True; Exists -> [_boolFileDownloaded])
        - If (Condition: [_boolFileDownloaded])
          - Then:
            - Break: break_wait_loop (VirtualizedContainerService.HintSize=416,25, WorkflowViewState.IdRef=Break_1)
          - Else:
            - Sequence: Else
        - Delay: delay_100ms (Duration=[TimeSpan.FromMilliseconds(100)])
  - If (Condition: [_boolFileDownloaded])
    - Then:
      - LogMessage: log_file_downloaded_info (Level=Info, Message=["File downloaded: "+ _boolFileDownloaded.ToString], ViewState=True)
    - Else:
      - LogMessage: log_file_downloaded_warn (Level=Warn, Message=["File downloaded: "+ _boolFileDownloaded.ToString], ViewState=True)
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## excel_execute_macro
**Path:** `wfs\_utils\excel_execute_macro.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strExcelFilePath | `InArgument(x:String)` | In |
| _strMacroFunctionName | `InArgument(x:String)` | In |
| _strMacroFilePath | `InArgument(x:String)` | In |
| _arrMacroArguments | `InArgument(s:Object[])` | In |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |
| objMacroOutput | `x:Object` |

### Logic
- Members
  - _strExcelFilePath (Type=InArgument(x:String))
  - _strMacroFunctionName (Type=InArgument(x:String))
  - _strMacroFilePath (Type=InArgument(x:String))
  - _arrMacroArguments (Type=InArgument(s:Object[]))
- Sequence: excel_execute_macro
  - If (Condition: [_strExcelFilePath is Nothing])
    - Then:
      - assign_test_params:
  - get_current_job_info (VirtualizedContainerService.HintSize=506,25, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - LogMessage: log_macro_name_and_file_path (Level=Info, Message=[String.Format("Macro name: {0} | File path: {1}", _strMacroFunctionName, _strExcelFilePath)], ViewState=False)
  - ExcelApplicationScope: eas_execute_macro (WorkbookPath=[_strExcelFilePath])
    - ExcelApplicationScope.Body
      - ActivityAction (TypeArguments=ui:WorkbookApplication)
        - Sequence: execute_macro_in_excel_file
          - invoke_vba_macro (CodeFilePath=[_strMacroFilePath], EntryMethodName=[_strMacroFunctionName], EntryMethodParameters=[_arrMacroArguments], VirtualizedContainerService.HintSize=410,88, WorkflowViewState.IdRef=InvokeVBA_1; OutputValue -> [objMacroOutput])
          - If (Condition: [not String.IsNullOrEmpty(objMacroOutput.ToString)])
            - Then:
            - Else:
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_clean_folder
**Path:** `wfs\_utils\utils_clean_folder.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strFolderToClean | `InArgument(x:String)` | In |

### Variables

| Name | Type |
|---|---|
| boolProcessDataExist | `x:Boolean` |
| boolFolderExists | `x:Boolean` |
| jobInfo | `ui:CurrentJobInfo` |

### Logic
- Members
  - _strFolderToClean (Type=InArgument(x:String))
- Sequence: utils_clean_folder
  - get_current_job_info (VirtualizedContainerService.HintSize=486,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - LogMessage: log_folder_to_clean (Level=Info, Message=[String.Format("Folder to clean: {0}", _strFolderToClean)], ViewState=False)
  - pe_folder_exists (Resource={x:Null}, VirtualizedContainerService.HintSize=486,147, WorkflowViewState.IdRef=PathExists_2, Path=[_strFolderToClean], PathType=Folder; Exists -> [boolFolderExists])
  - LogMessage: log_folder_exists (Level=Trace, Message=["Folder exists: " + boolFolderExists.ToString], ViewState=False)
  - If (Condition: [boolFolderExists])
    - Then:
      - Sequence: Then
        - delete_folder (ContinueOnError={x:Null}, ResourceFile={x:Null}, VirtualizedContainerService.HintSize=382,149, WorkflowViewState.IdRef=Delete_2, Path=[_strFolderToClean])
        - LogMessage: log_folder_empty (Level=Trace, Message=["Folder is now empty"], ViewState=True)
    - Else:
      - Sequence: Else
  - ForEach<> in [Enumerable.Range(0, 3)]
    - ActivityAction (TypeArguments=x:Int32)
      - Sequence: fe_iteration_try_to_create_folder
        - Delay: delay_1s (Duration=00:00:01)
        - create_process_data (ContinueOnError={x:Null}, VirtualizedContainerService.HintSize=418,112, WorkflowViewState.IdRef=CreateDirectory_1, Path=[_strFolderToClean]; Output -> {x:Null})
        - pe_folder (Resource={x:Null}, VirtualizedContainerService.HintSize=418,147, WorkflowViewState.IdRef=PathExists_1, Path=[_strFolderToClean], PathType=Folder; Exists -> [boolProcessDataExist])
        - If (Condition: [boolProcessDataExist])
          - Then:
            - Sequence: log_and_break
          - Else:
            - Sequence: Else
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_create_dictionary
**Path:** `wfs\_utils\utils_create_dictionary.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strDictFilePath | `InArgument(x:String)` | In |
| _strKeyColumn | `InArgument(x:String)` | In |
| _strValueColumn | `InArgument(x:String)` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dictOut | `OutArgument(scg:Dictionary(x:String, x:String))` | Out |

### Variables

| Name | Type |
|---|---|
| dtaDict | `sd:DataTable` |
| jobInfo | `ui:CurrentJobInfo` |
| strKey | `x:String` |
| strValue | `x:String` |

### Logic
- Members
  - _strDictFilePath (Type=InArgument(x:String))
  - _strKeyColumn (Type=InArgument(x:String))
  - _strValueColumn (Type=InArgument(x:String))
  - _dictOut (Type=OutArgument(scg:Dictionary(x:String, x:String)))
- Sequence: utils_create_dictionary
  - LogMessage: log_starting_wf (Level=Info, Message=["Creating dictionary from: " + _strDictFilePath.Split("\"c).Last.ToString], ViewState=False)
  - get_current_job_info (VirtualizedContainerService.HintSize=522,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - LogMessage: log_key_value_columns (Level=Trace, Message=[String.Format("Key col: {0} | Value col: {1}", _strKeyColumn, _strValueColumn)], ViewState=False)
  - read_dict_file (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaDict], VirtualizedContainerService.HintSize=522,120, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[_strDictFilePath])
  - assign_init_dic_out:
  - fer_assign/append_key_value_pair (ColumnNames={x:Null}, CurrentIndex={x:Null}, DataTable=[dtaDict], VirtualizedContainerService.HintSize=522,687, WorkflowViewState.IdRef=ForEachRow_1)
    - ForEachRow.Body
      - ActivityAction (TypeArguments=sd:DataRow)
        - Sequence: fer_assign/append_key_value_pair
          - assign/append_key_value_pair:
          - comment_out_log_key_values (VirtualizedContainerService.HintSize=486,210, WorkflowViewState.IdRef=CommentOut_1)
            - CommentOut.Body
  - LogMessage: log_dictionary_count (Level=Trace, Message=["Dictionary key-pair values: " + _dictOut.Count.ToString], ViewState=False)
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_get_country_working_days
**Path:** `wfs\_utils\utils_get_country_working_days.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dtiStartDate | `InArgument(s:DateTime)` | In |
| _intMaxDays | `InArgument(x:Int32)` | In |
| _strCountryCode | `InArgument(x:String)` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _arrWorkingDays | `OutArgument(s:DateTime[])` | Out |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |
| strAPIUrlTemp | `x:String` |
| arrYears | `s:Int32[]` |
| arrdtiHolidays | `s:DateTime[]` |

### Logic
- Members
  - _dtiStartDate (Type=InArgument(s:DateTime))
  - _intMaxDays (Type=InArgument(x:Int32))
  - _arrWorkingDays (Type=OutArgument(s:DateTime[]))
  - _strCountryCode (Annotation.AnnotationText=Go to https://date.nager.at/Country, Type=InArgument(x:String))
- Sequence: utils_get_country_working_days
  - If (Condition: [_dtiStartDate = DateTime.MinValue])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=479,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_config_parameters:
  - InvokeWorkflowFile: inv_utils_get_holidays_api -> wfs\_utils\utils_get_holidays_api.xaml
    - in _dtiStartDate: [_dtiStartDate]
    - out _arrdtiHolidays: [arrdtiHolidays]
    - in _strCountryCode: [_strCountryCode]
  - filter_holidays_and_weekends:
  - LogMessage: log_total_working_days (Level=[UiPath.Core.Activities.LogLevel.Info], Message=[String.Format("Working days since {0}: {1} (intMaxDays {2})", _dtiStartDate.ToString("dd-MM-yyyy"), _arrWorkingDays.Count, _intMaxDays)], ViewState=False)
  - comment_out_log_working_days (VirtualizedContainerService.HintSize=479,57, WorkflowViewState.IdRef=CommentOut_3, ViewState=False)
    - CommentOut.Body
      - Sequence: Ignored Activities
        - ForEach<> in [_arrWorkingDays]
          - ActivityAction (TypeArguments=s:DateTime)
            - Sequence: Body
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_get_holidays_api
**Path:** `wfs\_utils\utils_get_holidays_api.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dtiStartDate | `InArgument(s:DateTime)` | In |
| _strCountryCode | `InArgument(x:String)` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _arrdtiHolidays | `OutArgument(s:DateTime[])` | Out |

### Variables

| Name | Type |
|---|---|
| jobInfo | `ui:CurrentJobInfo` |
| strAPIUrlTemp | `x:String` |
| arrYears | `s:Int32[]` |
| arrdtiHolidays | `s:DateTime[]` |
| arrdtiYearHolidays | `s:DateTime[]` |
| strContent | `x:String` |
| jsonContent | `njl:JArray` |
| intStatus | `x:Int32` |
| strAPIEndpointYear | `x:String` |

### Logic
- Members
  - _dtiStartDate (Type=InArgument(s:DateTime))
  - _arrdtiHolidays (Type=OutArgument(s:DateTime[]))
  - _strCountryCode (Type=InArgument(x:String))
- Sequence: utils_get_holidays_api
  - If (Condition: [_dtiStartDate = DateTime.MinValue])
    - Then:
      - Sequence: assign_test_arguments
        - LogMessage: log_using_test_arguments (Level=Warn, Message=["USING TEST ARGUMENTS!"], ViewState=False)
        - assign_wf_test_arguments:
    - Else:
      - Sequence: Else
  - get_current_job_info (VirtualizedContainerService.HintSize=515,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_config_parameters:
  - LogMessage: log_country_and_year (Level=Info, Message=[String.Format("Getting holidays from COUNTRY: {0} | YEARS: {1}", _strCountryCode, String.Join(";", arrYears))], ViewState=False)
  - ForEach<> in [arrYears]
    - ActivityAction (TypeArguments=x:Int32)
      - Sequence: Body
        - Assign:  = 
        - http_request_year_holidays (Body={x:Null}, ClientCertificate={x:Null}, ClientCertificatePassword={x:Null}, ConsumerKey={x:Null}, ConsumerSecret={x:Null}, ContinueOnError={x:Null}, FileAttachments={x:Null}, OAuth1Token={x:Null}, OAuth1TokenSecret={x:Null}, OAuth2Token={x:Null}, Password={x:Null}, ResourcePath={x:Null}, ResponseAttachment={x:Null}, ResponseHeaders={x:Null}, SecureClientCertificatePassword={x:Null}, SecurePassword={x:Null}, Username={x:Null}, AcceptFormat=ANY, AuthenticationType=None, BodyFormat=application/xml, EnableSSLVerification=True, EndPoint=[strAPIEndpointYear], VirtualizedContainerService.HintSize=479,102, WorkflowViewState.IdRef=HttpClient_2, Method=GET, StatusCode=[intStatus], TimeoutMS=6000; Result -> [strContent])
        - deserialize_json_holidays_api_output (VirtualizedContainerService.HintSize=479,92, WorkflowViewState.IdRef=DeserializeJsonArray_2, JsonArray=[jsonContent], JsonString=[strContent])
        - filter_only_global_country_holidays:
        - assign_year_holidays:
  - LogMessage: log_total_of_holidays (Level=Info, Message=[String.Format("Total holidays: {0}", arrdtiHolidays.Count)], ViewState=False)
  - comment_log_holidays (VirtualizedContainerService.HintSize=515,583, WorkflowViewState.IdRef=CommentOut_7, ViewState=True)
    - CommentOut.Body
      - Sequence: Ignored Activities
        - ForEach<> in [arrdtiHolidays]
          - ActivityAction (TypeArguments=s:DateTime)
            - Sequence: Body
  - Assign:  = 
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_run_python_script
**Path:** `wfs\_utils\utils_run_python_script.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strPythonScriptPath | `InArgument(x:String)` | In |
| _arrScriptArguments | `InArgument(s:String[])` | In |
| _intDelaySecsAfterExecution | `InArgument(x:Int32)` | In |
| _dblExecutionMinutesTimeout | `InArgument(x:Double)` | In |
| _strPythonInterpreter | `InArgument(x:String)` | In |
| _boolRunBatchMinimized | `InArgument(x:Boolean)` | In |
| _boolWaitScriptFinalization | `InArgument(x:Boolean)` | In |

### Variables

| Name | Type |
|---|---|
| strScriptArgs | `x:String` |
| strBatchScriptCode | `x:String` |
| strBatchArgs | `x:String` |
| strBatchTempFilePath | `x:String` |
| boolScriptRunning | `x:Boolean` |
| intMaxWaitingSecs | `x:Int32` |
| intExeTime | `x:Int32` |
| jobInfo | `ui:CurrentJobInfo` |
| strDeleteBatchLines | `x:String` |

### Logic
- Members
  - _strPythonScriptPath (Type=InArgument(x:String))
  - _arrScriptArguments (Type=InArgument(s:String[]))
  - _intDelaySecsAfterExecution (Type=InArgument(x:Int32))
  - _dblExecutionMinutesTimeout (Type=InArgument(x:Double))
  - _strPythonInterpreter (Type=InArgument(x:String))
  - _boolRunBatchMinimized (Type=InArgument(x:Boolean))
  - _boolWaitScriptFinalization (Type=InArgument(x:Boolean))
- Sequence: utils_run_python_script
  - get_current_job_info (VirtualizedContainerService.HintSize=513,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - assign_wf_variables:
  - Sequence: create_temp_batch_file
    - If (Condition: [_boolRunBatchMinimized])
      - Then:
        - add_minimize_batch_command_line:
    - create_batch_script:
    - comment_out (VirtualizedContainerService.HintSize=479,57, WorkflowViewState.IdRef=CommentOut_6, ViewState=False)
      - CommentOut.Body
        - Sequence: Ignored Activities
          - create_batch_script:
    - comment_autodelete_line (VirtualizedContainerService.HintSize=479,88, WorkflowViewState.IdRef=Comment_1; Text -> // Autodelete: "del ""%~f0"" & exit")
    - LogMessage: log_batch_script (Level=Trace, Message=["Batch script: " + vbNewLine + strBatchScriptCode], ViewState=False)
    - LogMessage: log_creating_batch_file (Level=Info, Message=["Creating temp batch file in: " + strBatchTempFilePath], ViewState=False)
    - write_batch_script (File={x:Null}, FileName=[strBatchTempFilePath], VirtualizedContainerService.HintSize=479,165, WorkflowViewState.IdRef=WriteTextFile_1; Text -> [strBatchScriptCode])
    - comment_out_deleted_batch_file (VirtualizedContainerService.HintSize=479,48, WorkflowViewState.IdRef=CommentOut_5, ViewState=False)
      - CommentOut.Body
        - Sequence: Ignored Activities
          - Sequence: create_temp_delete_batch_file
            - create_temp_delete_batch_file:
            - write_delete_batch_file_code (File={x:Null}, FileName=[strTempDeleteBatchFile], VirtualizedContainerService.HintSize=479,156, WorkflowViewState.IdRef=WriteTextFile_4; Text -> [strDeleteBatchLines])
  - LogMessage: log_executing_python_script (Level=Info, Message=["Executing python script: " + _strPythonScriptPath], ViewState=False)
  - start_process_batch_file (WorkingDirectory={x:Null}, Arguments=[strScriptArgs], FileName=[strBatchTempFilePath], VirtualizedContainerService.HintSize=513,120, WorkflowViewState.IdRef=StartProcess_1)
  - LogMessage: log_waiting_script_execution (Level=Info, Message=[String.Format("Waiting script execution for max {1} [s] ({0} min) ...", _dblExecutionMinutesTimeout, intMaxWaitingSecs)], ViewState=False)
  - ForEach<> in [Enumerable.Range(0, intMaxWaitingSecs+ _intDelaySecsAfterExecution)]
    - ActivityAction (TypeArguments=x:Int32)
      - Sequence: fe_sec_wait_script_execution
        - If (Condition: [not _boolWaitScriptFinalization])
          - Then:
            - Break: break (VirtualizedContainerService.HintSize=416,25, WorkflowViewState.IdRef=Break_2)
          - Else:
            - Sequence: Else
        - Delay: delay_1s (Duration=00:00:01)
        - pe_temp_batch_flag_file (Resource={x:Null}, VirtualizedContainerService.HintSize=434,147, WorkflowViewState.IdRef=PathExists_1, Path=[strBatchTempFilePath], PathType=File; Exists -> [boolScriptRunning])
        - Assign:  = 
        - If (Condition: [not boolScriptRunning])
          - Then:
            - Break: break (VirtualizedContainerService.HintSize=416,25, WorkflowViewState.IdRef=Break_1)
          - Else:
            - Sequence: Else
        - If (Condition: [iter >= (intMaxWaitingSecs-1)])
          - Then:
            - LogMessage: log_max_timeout_reached (Level=Warn, Message=["Maximum timeout reached! Processing will continue even though the script has not finished!"], ViewState=True)
          - Else:
            - Sequence: Else
  - LogMessage: log_execution_time (Level=Info, Message=[String.Format("Script execution time -> {0}:{1}",  CInt(intExeTime/60).ToString("D2"), (intExeTime Mod 60).ToString("D2"))], ViewState=False)
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)

## utils_transform_dta_columns
**Path:** `wfs\_utils\utils_transform_dta_columns.xaml`

### Arguments
**Input / InOut:**

| Name | Type | Direction |
|---|---|---|
| _strTransformColsFile | `InArgument(x:String)` | In |
| _dtaIn | `InArgument(sd:DataTable)` | In |

**Output / InOut:**

| Name | Type | Direction |
|---|---|---|
| _dtaOut | `OutArgument(sd:DataTable)` | Out |

### Variables

| Name | Type |
|---|---|
| dtaParameters | `sd:DataTable` |
| strTestTableFile | `x:String` |
| intParamIdx | `x:Int32` |
| jobInfo | `ui:CurrentJobInfo` |
| arrDtaInCols | `s:String[]` |
| arrParamsInputCols | `s:String[]` |
| arrNewColsToRemove | `s:String[]` |
| strAction | `x:String` |
| strOriginalCol | `x:String` |
| strNewCol | `x:String` |
| strDefaultValue | `x:String` |
| arrSortedColumns | `s:String[]` |
| intColIdx | `x:Int32` |

### Logic
- Members
  - _strTransformColsFile (Type=InArgument(x:String))
  - _dtaIn (Type=InArgument(sd:DataTable))
  - _dtaOut (Type=OutArgument(sd:DataTable))
- Sequence: utils_transform_dta_columns
  - If (Condition: [_dtaIn is Nothing])
    - Then:
      - Sequence: assign_wf_test_args
        - LogMessage: log_using_test_args (Level=Warn, Message=["USING TEST ARGUMENTS"], ViewState=False)
        - assign_wf_test_arguments:
        - read_test_table_file (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[_dtaIn], VirtualizedContainerService.HintSize=479,52, WorkflowViewState.IdRef=ReadRange_1, SheetName=base, WorkbookPath=[strTestTableFile], ViewState=False)
  - get_current_job_info (VirtualizedContainerService.HintSize=611,57, WorkflowViewState.IdRef=GetCurrentJobInfo_1; Result -> [jobInfo])
  - LogMessage: log_starting_wf (Level=Info, Message=[String.Format("--- Starting: {0} ---", jobInfo.WorkflowName)], ViewState=False)
  - LogMessage: log_dta_in_col_count (Level=Info, Message=["_dtaIn columns: " + _dtaIn.ColumnCount.ToString], ViewState=False)
  - read_transformation_parameters (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[dtaParameters], VirtualizedContainerService.HintSize=611,120, WorkflowViewState.IdRef=ReadRange_2, SheetName=base, WorkbookPath=[_strTransformColsFile], ViewState=True)
  - sort_dta_params_by_index (ColumnIndex={x:Null}, DataColumn={x:Null}, Annotation.AnnotationText=ColName: "index", ColumnName=index, DataTable=[dtaParameters], VirtualizedContainerService.HintSize=611,143, WorkflowViewState.IdRef=SortDataTable_3, Order=Ascending, SortOrder=Ascending, ViewState=True; OutputDataTable -> [dtaParameters])
  - assign_dta_out:
  - Sequence: remove_extra_columns
    - assign_dta_in_columns:
    - If (Condition: [arrNewColsToRemove.Count > 0])
      - Then:
        - LogMessage: log_new_cols_to_remove (Level=Warn, Message=[String.Format("_dtaIn has {0} new rows: {1}", arrNewColsToRemove.Count, String.Join("; ", arrNewColsToRemove))], ViewState=False)
    - ForEach<> in [arrNewColsToRemove]
      - ActivityAction (TypeArguments=x:String)
        - Sequence: fe_new_col_remove_from_dta
          - remove_column (Column={x:Null}, ColumnIndex={x:Null}, ColumnName=[column_name], DataTable=[_dtaOut], VirtualizedContainerService.HintSize=388,186, WorkflowViewState.IdRef=RemoveDataColumn_3)
  - fer_transform_column (ColumnNames={x:Null}, CurrentIndex=[intParamIdx], DataTable=[dtaParameters], VirtualizedContainerService.HintSize=611,2370, WorkflowViewState.IdRef=ForEachRow_1)
    - ForEachRow.Body
      - ActivityAction (TypeArguments=sd:DataRow)
        - Sequence: fer_transform_column
          - assign_column_info:
          - Try
            - Switch: try_sw_action_process_column (TypeArguments=x:String, Expression=[strAction], VirtualizedContainerService.HintSize=481,1305, WorkflowViewState.IdRef=Switch`1_1, ViewState=True)
          - Catch (Exception)
            - Sequence: log_and_retrhow_exception
          - Finally
            - Sequence: Finally
  - Sequence: reorder_columns
    - get_sorted_new_columns:
    - ForEach<> in [arrSortedColumns]
      - ActivityAction (TypeArguments=x:String)
        - Sequence: fe_col_set_ordinal
          - InvokeMethod: invoke_set_ordinal (MethodName=SetOrdinal, TargetObject=[_dtaOut.Columns(column)], ViewState=True)
            - InArgument (TypeArguments=x:Int32)
  - LogMessage: log_dta_out_col_count (Level=Info, Message=["_dtaOut columns: " + _dtaOut.ColumnCount.ToString], ViewState=False)
  - If (Condition: [strTestTableFile <> ""])
    - Then:
      - write_dta_out (WorkbookPathResource={x:Null}, AddHeaders=True, DataTable=[_dtaOut], VirtualizedContainerService.HintSize=334,116, WorkflowViewState.IdRef=WriteRange_1, SheetName=base, StartingCell=A1, WorkbookPath=[strTestTableFile.Replace(".xlsx", "_formatted.xlsx")])
    - Else:
      - Sequence: Sequence
  - LogMessage: log_wf_finished (Level=Info, Message=[String.Format("--- Finished: {0} ---", jobInfo.WorkflowName)], ViewState=False)
