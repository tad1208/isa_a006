# State Machine Workflows Summary
_Project root: `D:\uipath_code\CL\test_cl_framework\framework`_

## Index
| Relative Path | StateMachineName | InitialState |
|---|---|---|
| `main.xaml` | main | {x:Reference __ReferenceID6} |

## main.xaml
**RelativePath:** `main.xaml`
**WorkflowType:** StateMachine
**StateMachineName:** main
**InitialState:** {x:Reference __ReferenceID6}

### State Logic
#### final_state
**Entry:**
- Sequence: final_sequence
  - Log: [Info] ["----- Starting state: final_state -----"]
  - InvokeWorkflowFile: inv_utils_close_apps.xaml -> wfs\_fmw\utils_close_apps.xaml
    - Dictionary : 
  - Try
    - Sequence: try_update_and_upload_memory
      - InvokeWorkflowFile: inv_fmw_update_process_memory.xaml -> wfs\_fmw\memory\fmw_update_process_memory.xaml
        - in  _dicConfig: [dicConfig]
      - InvokeWorkflowFile: inv_fmw_upload_process_memory.xaml -> wfs\_fmw\memory\fmw_upload_process_memory.xaml
        - in  _dicConfig: [dicConfig]
  - Catch (Exception)
      - DelegateInArgument: exception
    - Sequence: log_and_assign_sys_exception
      - Log: [Warn] [String.Format("Sys Exception: {0} -> {1}", exception.Source, exception.Message)]
      - MultipleAssign: assign_system_exception
  - Finally
    - Sequence: Finally
  - IfElseIf: if_exception_exists
**Exit:**
_(no exit actions)_

#### 1_state_name
**Entry:**
- Sequence: process_current_state
  - If (Condition: [_intRobotState = 1 and _intRobotState <= _intFinalState])
    - Then:
      - Try
        - Sequence: try_process_current_state
          - Log: [Info] ["----- Starting state: "+ _intRobotState.ToString + " -----"]
          - InvokeWorkflowFile: inv_p001_build_worktray_workflow -> wfs\_templates\p001_build_worktray.xaml
            - in  _dicConfig: [dicConfig]
            - in  _intExecutionDayOffset: [_intExecutionDayOffset]
          - CommentOut: comment_out_test_exceptions
          - Comment: comment_fmw
          - Assign:  = 
          - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Finally
        - Sequence: Finally
    - Else:
      - Sequence: Else
**Exit:**
_(no exit actions)_

#### 2_state_name
**Entry:**
- Sequence: process_current_state
  - If (Condition: [_intRobotState = 2 and _intRobotState <= _intFinalState])
    - Then:
      - Try
        - Sequence: try_process_current_state
          - Log: [Info] ["----- Starting state: "+ _intRobotState.ToString + " -----"]
          - Comment: comment_fmw
          - Assign:  = 
          - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Finally
        - Sequence: Finally
    - Else:
      - Sequence: Else
**Exit:**
_(no exit actions)_

#### 3_state_name
**Entry:**
- Sequence: process_current_state
  - If (Condition: [_intRobotState = 3 and _intRobotState <= _intFinalState])
    - Then:
      - Try
        - Sequence: try_process_current_state
          - Log: [Info] ["----- Starting state: "+ _intRobotState.ToString + " -----"]
          - Comment: comment_fmw
          - Assign:  = 
          - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Finally
        - Sequence: Finally
    - Else:
      - Sequence: Else
**Exit:**
_(no exit actions)_

#### n_send_execution_report
**Entry:**
- Sequence: process_current_state
  - If (Condition: [_intRobotState = 4 and _intRobotState <= _intFinalState])
    - Then:
      - Try
        - Sequence: try_process_current_state
          - Log: [Info] ["----- Starting state: "+ _intRobotState.ToString + " -----"]
          - InvokeWorkflowFile: inv_send_execution_report.xaml -> wfs\_templates\p001_send_execution_report.xaml
            - in  _dicConfig: [dicConfig]
            - in  _boolSendToClient: [_boolSendToClient]
          - Comment: comment_fmw
          - Assign:  = 
          - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Catch (Exception)
          - DelegateInArgument: exception
        - Assign:  = 
      - Finally
        - Sequence: Finally
    - Else:
      - Sequence: Else
**Exit:**
_(no exit actions)_

#### error_state
**Entry:**
- Sequence: handle_exception
  - Log: [Error] [String.Format("Error found in state: {0}", _intRobotState)]
  - MultipleAssign: assign_screenshot_path
  - CreateDirectory: create_exception_folder
  - If (Condition: [excBusinessException isNot Nothing])
    - Then:
      - Sequence: log_business_exception
        - Log: [Error] [excBusinessException.Source + " -> " + excBusinessException.Message]
    - Else:
      - Sequence: Else
  - If (Condition: [excSystemException isNot Nothing])
    - Then:
      - Sequence: Then
        - Log: [Error] [excSystemException.Source + " -> " + excSystemException.Message]
        - If (Condition: [CInt(dicConfig("ENVIRONMENT")) = 10])
          - Then:
            - Sequence: msg_box_stop_exception
          - Else:
            - Sequence: Else
        - InvokeWorkflowFile: inv_utils_take_screenshot_workflow -> wfs\_fmw\utils_take_screenshot.xaml
          - in  _strScreenshotPath: [strExcScreenshotPath]
        - If (Condition: [intRetryCounter < (intMaxTryNumber-1)])
          - Then:
            - Sequence: Then
          - Else:
            - Sequence: Else
    - Else:
      - Sequence: Else
  - InvokeWorkflowFile: inv_utils_close_apps_workflow -> wfs\_fmw\utils_close_apps.xaml
    - Dictionary : 
**Exit:**
_(no exit actions)_

#### framework_init_state
**Entry:**
- Try
  - Sequence: try_init_sequence
    - InvokeWorkflowFile: inv_utils_read_config_dict_workflow -> wfs\_fmw\utils_read_config_dict.xaml
      - out _dicConfiguration: [dicConfig]
      - in  _strConfigFile: configuration.xlsx
      - in  _strarrConfigSheets: [{"base"}]
    - Log: [Info] [String.Format("--- Executing process in environment: {0} ---", dicConfig("ENVIRONMENT"))]
    - Log: [Info] [String.Format("User session: {0} | Screen resolution (WxH): ({1}x{2})", Environment.UserName, System.Windows.Forms.Screen.PrimaryScreen.Bounds.Width, System.Windows.Forms.Screen.PrimaryScreen.Bounds.Height)]
    - Log: [Info] [String.Format("_intRobotState: {0} | _intFinalState: {1} |  _boolSendToClient: {2} | _intExecutionDayOffset: {3}", _intRobotState, _intFinalState, _boolSendToClient, _intExecutionDayOffset)]
    - MultipleAssign: assign_init_parameters
    - InvokeWorkflowFile: inv_utils_close_all_apps -> wfs\_fmw\utils_close_apps.xaml
      - Dictionary : 
    - If (Condition: [_intRobotState = 0])
      - Then:
        - Sequence: normal_execution
          - Sequence: create_fmw_folders
            - MultipleAssign: get_process_main_folder
            - Log: [Info] ["Creating fmw process folder: " + strProcessMainFolder]
            - CreateDirectory: create_process_main_folder
            - CreateDirectory: created_out_folder
            - CreateDirectory: created_exceptions_folder
            - CreateDirectory: created_process_data_folder
          - Sequence: create_process_data_backup_and_clean_process_data
            - MultipleAssign: assign_porcess_data_folders
            - Log: [Info] ["Moving process_data to: " + String.Join("\", strDestFolder.Split("\"c).ToList.GetRange(strDestFolder.Split("\"c).Count-3, 3))]
            - CreateDirectory: created_latest_process_data_folder
            - Delete: delete_latest_process_data
            - CreateDirectory: created_latest_process_data_folder
            - CreateDirectory: created_process_data_folder
            - MoveFolderX: move_folder_process_data_to_out
            - CommentOut: comment_out_move_inv_method
            - CreateDirectory: created_process_data_folder
          - Assign:  = 
      - Else:
        - Sequence: log_skip_state
          - Log: [Warn] ["Robot is set to start in state: " + _intRobotState.ToString]
- Catch (Exception)
    - DelegateInArgument: exception
  - Sequence: catch_exception
    - Assign:  = 
    - Assign:  = 
    - Log: [Error] ["Initialization error. Exception: " + exception.Source + " -> " + exception.Message]
- Finally
  - Sequence: Finally
**Exit:**
_(no exit actions)_

### Transitions
- **1_state_name** -> **2_state_name**
- **1_state_name** -> **error_state**
- **2_state_name** -> **3_state_name**
- **2_state_name** -> **error_state**
- **3_state_name** -> **n_send_execution_report**
- **3_state_name** -> **error_state**
- **n_send_execution_report** -> **final_state**
- **n_send_execution_report** -> **error_state**
- **error_state** -> **final_state**
- **error_state** -> **1_state_name**
- **framework_init_state** -> **1_state_name**
- **framework_init_state** -> **final_state**

### Graph (JSON)
```json
{
  "state_machine_name": "main",
  "initial_state": "{x:Reference __ReferenceID6}",
  "states": {
    "final_state": {
      "entry_count": 21,
      "exit_count": 0,
      "entry_outline": [
        "- Sequence: final_sequence",
        "  - Log: [Info] [\"----- Starting state: final_state -----\"]",
        "  - InvokeWorkflowFile: inv_utils_close_apps.xaml -> wfs\\_fmw\\utils_close_apps.xaml",
        "    - Dictionary : ",
        "  - Try",
        "    - Sequence: try_update_and_upload_memory",
        "      - InvokeWorkflowFile: inv_fmw_update_process_memory.xaml -> wfs\\_fmw\\memory\\fmw_update_process_memory.xaml",
        "        - in  _dicConfig: [dicConfig]",
        "      - InvokeWorkflowFile: inv_fmw_upload_process_memory.xaml -> wfs\\_fmw\\memory\\fmw_upload_process_memory.xaml",
        "        - in  _dicConfig: [dicConfig]",
        "  - Catch (Exception)",
        "      - DelegateInArgument: exception",
        "    - Sequence: log_and_assign_sys_exception",
        "      - Log: [Warn] [String.Format(\"Sys Exception: {0} -> {1}\", exception.Source, exception.Message)]",
        "      - MultipleAssign: assign_system_exception",
        "  - Finally",
        "    - Sequence: Finally",
        "  - IfElseIf: if_exception_exists"
      ],
      "exit_outline": []
    },
    "1_state_name": {
      "entry_count": 15,
      "exit_count": 0,
      "entry_outline": [
        "- Sequence: process_current_state",
        "  - If (Condition: [_intRobotState = 1 and _intRobotState <= _intFinalState])",
        "    - Then:",
        "      - Try",
        "        - Sequence: try_process_current_state",
        "          - Log: [Info] [\"----- Starting state: \"+ _intRobotState.ToString + \" -----\"]",
        "          - InvokeWorkflowFile: inv_p001_build_worktray_workflow -> wfs\\_templates\\p001_build_worktray.xaml",
        "            - in  _dicConfig: [dicConfig]",
        "            - in  _intExecutionDayOffset: [_intExecutionDayOffset]",
        "          - CommentOut: comment_out_test_exceptions",
        "          - Comment: comment_fmw",
        "          - Assign:  = ",
        "          - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Finally",
        "        - Sequence: Finally",
        "    - Else:",
        "      - Sequence: Else"
      ],
      "exit_outline": []
    },
    "2_state_name": {
      "entry_count": 11,
      "exit_count": 0,
      "entry_outline": [
        "- Sequence: process_current_state",
        "  - If (Condition: [_intRobotState = 2 and _intRobotState <= _intFinalState])",
        "    - Then:",
        "      - Try",
        "        - Sequence: try_process_current_state",
        "          - Log: [Info] [\"----- Starting state: \"+ _intRobotState.ToString + \" -----\"]",
        "          - Comment: comment_fmw",
        "          - Assign:  = ",
        "          - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Finally",
        "        - Sequence: Finally",
        "    - Else:",
        "      - Sequence: Else"
      ],
      "exit_outline": []
    },
    "3_state_name": {
      "entry_count": 11,
      "exit_count": 0,
      "entry_outline": [
        "- Sequence: process_current_state",
        "  - If (Condition: [_intRobotState = 3 and _intRobotState <= _intFinalState])",
        "    - Then:",
        "      - Try",
        "        - Sequence: try_process_current_state",
        "          - Log: [Info] [\"----- Starting state: \"+ _intRobotState.ToString + \" -----\"]",
        "          - Comment: comment_fmw",
        "          - Assign:  = ",
        "          - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Finally",
        "        - Sequence: Finally",
        "    - Else:",
        "      - Sequence: Else"
      ],
      "exit_outline": []
    },
    "n_send_execution_report": {
      "entry_count": 12,
      "exit_count": 0,
      "entry_outline": [
        "- Sequence: process_current_state",
        "  - If (Condition: [_intRobotState = 4 and _intRobotState <= _intFinalState])",
        "    - Then:",
        "      - Try",
        "        - Sequence: try_process_current_state",
        "          - Log: [Info] [\"----- Starting state: \"+ _intRobotState.ToString + \" -----\"]",
        "          - InvokeWorkflowFile: inv_send_execution_report.xaml -> wfs\\_templates\\p001_send_execution_report.xaml",
        "            - in  _dicConfig: [dicConfig]",
        "            - in  _boolSendToClient: [_boolSendToClient]",
        "          - Comment: comment_fmw",
        "          - Assign:  = ",
        "          - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Catch (Exception)",
        "          - DelegateInArgument: exception",
        "        - Assign:  = ",
        "      - Finally",
        "        - Sequence: Finally",
        "    - Else:",
        "      - Sequence: Else"
      ],
      "exit_outline": []
    },
    "error_state": {
      "entry_count": 21,
      "exit_count": 0,
      "entry_outline": [
        "- Sequence: handle_exception",
        "  - Log: [Error] [String.Format(\"Error found in state: {0}\", _intRobotState)]",
        "  - MultipleAssign: assign_screenshot_path",
        "  - CreateDirectory: create_exception_folder",
        "  - If (Condition: [excBusinessException isNot Nothing])",
        "    - Then:",
        "      - Sequence: log_business_exception",
        "        - Log: [Error] [excBusinessException.Source + \" -> \" + excBusinessException.Message]",
        "    - Else:",
        "      - Sequence: Else",
        "  - If (Condition: [excSystemException isNot Nothing])",
        "    - Then:",
        "      - Sequence: Then",
        "        - Log: [Error] [excSystemException.Source + \" -> \" + excSystemException.Message]",
        "        - If (Condition: [CInt(dicConfig(\"ENVIRONMENT\")) = 10])",
        "          - Then:",
        "            - Sequence: msg_box_stop_exception",
        "          - Else:",
        "            - Sequence: Else",
        "        - InvokeWorkflowFile: inv_utils_take_screenshot_workflow -> wfs\\_fmw\\utils_take_screenshot.xaml",
        "          - in  _strScreenshotPath: [strExcScreenshotPath]",
        "        - If (Condition: [intRetryCounter < (intMaxTryNumber-1)])",
        "          - Then:",
        "            - Sequence: Then",
        "          - Else:",
        "            - Sequence: Else",
        "    - Else:",
        "      - Sequence: Else",
        "  - InvokeWorkflowFile: inv_utils_close_apps_workflow -> wfs\\_fmw\\utils_close_apps.xaml",
        "    - Dictionary : "
      ],
      "exit_outline": []
    },
    "framework_init_state": {
      "entry_count": 23,
      "exit_count": 0,
      "entry_outline": [
        "- Try",
        "  - Sequence: try_init_sequence",
        "    - InvokeWorkflowFile: inv_utils_read_config_dict_workflow -> wfs\\_fmw\\utils_read_config_dict.xaml",
        "      - out _dicConfiguration: [dicConfig]",
        "      - in  _strConfigFile: configuration.xlsx",
        "      - in  _strarrConfigSheets: [{\"base\"}]",
        "    - Log: [Info] [String.Format(\"--- Executing process in environment: {0} ---\", dicConfig(\"ENVIRONMENT\"))]",
        "    - Log: [Info] [String.Format(\"User session: {0} | Screen resolution (WxH): ({1}x{2})\", Environment.UserName, System.Windows.Forms.Screen.PrimaryScreen.Bounds.Width, System.Windows.Forms.Screen.PrimaryScreen.Bounds.Height)]",
        "    - Log: [Info] [String.Format(\"_intRobotState: {0} | _intFinalState: {1} |  _boolSendToClient: {2} | _intExecutionDayOffset: {3}\", _intRobotState, _intFinalState, _boolSendToClient, _intExecutionDayOffset)]",
        "    - MultipleAssign: assign_init_parameters",
        "    - InvokeWorkflowFile: inv_utils_close_all_apps -> wfs\\_fmw\\utils_close_apps.xaml",
        "      - Dictionary : ",
        "    - If (Condition: [_intRobotState = 0])",
        "      - Then:",
        "        - Sequence: normal_execution",
        "          - Sequence: create_fmw_folders",
        "            - MultipleAssign: get_process_main_folder",
        "            - Log: [Info] [\"Creating fmw process folder: \" + strProcessMainFolder]",
        "            - CreateDirectory: create_process_main_folder",
        "            - CreateDirectory: created_out_folder",
        "            - CreateDirectory: created_exceptions_folder",
        "            - CreateDirectory: created_process_data_folder",
        "          - Sequence: create_process_data_backup_and_clean_process_data",
        "            - MultipleAssign: assign_porcess_data_folders",
        "            - Log: [Info] [\"Moving process_data to: \" + String.Join(\"\\\", strDestFolder.Split(\"\\\"c).ToList.GetRange(strDestFolder.Split(\"\\\"c).Count-3, 3))]",
        "            - CreateDirectory: created_latest_process_data_folder",
        "            - Delete: delete_latest_process_data",
        "            - CreateDirectory: created_latest_process_data_folder",
        "            - CreateDirectory: created_process_data_folder",
        "            - MoveFolderX: move_folder_process_data_to_out",
        "            - CommentOut: comment_out_move_inv_method",
        "            - CreateDirectory: created_process_data_folder",
        "          - Assign:  = ",
        "      - Else:",
        "        - Sequence: log_skip_state",
        "          - Log: [Warn] [\"Robot is set to start in state: \" + _intRobotState.ToString]",
        "- Catch (Exception)",
        "    - DelegateInArgument: exception",
        "  - Sequence: catch_exception",
        "    - Assign:  = ",
        "    - Assign:  = ",
        "    - Log: [Error] [\"Initialization error. Exception: \" + exception.Source + \" -> \" + exception.Message]",
        "- Finally",
        "  - Sequence: Finally"
      ],
      "exit_outline": []
    }
  },
  "transitions": [
    {
      "from": "1_state_name",
      "to": "2_state_name",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "1_state_name",
      "to": "error_state",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "2_state_name",
      "to": "3_state_name",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "2_state_name",
      "to": "error_state",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "3_state_name",
      "to": "n_send_execution_report",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "3_state_name",
      "to": "error_state",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "n_send_execution_report",
      "to": "final_state",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "n_send_execution_report",
      "to": "error_state",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "error_state",
      "to": "final_state",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "error_state",
      "to": "1_state_name",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "framework_init_state",
      "to": "1_state_name",
      "condition": "",
      "trigger": null,
      "action_outline": []
    },
    {
      "from": "framework_init_state",
      "to": "final_state",
      "condition": "",
      "trigger": null,
      "action_outline": []
    }
  ]
}
```
