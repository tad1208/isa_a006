# Standalone framework
Created by: vicente.diaz | Last updated: 2025-01-02

## Main features

* Uses *State Machine* layout for the phases of automation project
* Uses a work tray to store processing information for each state
* Stores execution history in memory files with the same structure as the work tray
* Offers high level exception handling and application recovery
* Keeps external settings in `configuration.xlsx` file
* Pulls credentials from *Orchestartor Assets*
* Takes screenshots in case of application exceptions and are attached in the email
* Provides extra utility workflows like main_state_wf template for easy development

## How It Works

### INIT_STATE

* Initializes state machine parameters  
* Reads `configuration.xlsx` file and create a dictionary
* Empty the **process_data** folder for a clean process execution
* Sets the next state to execute using the framework argument **_intRobotState** (default 0).
* If the **_intRobotState** argument is greater than 0, the files in the **process_data** folder **WILL NOT BE DELETED**.


### PROCESS_STATES

* Depending on the developing process, it invokes the developed workflows that the process requires.
* You can use `framework\wfs\_templates` workflows to help you develop the process.

### ERROR_STATE

* Takes screenshot of the generated exception and saves the screenshot file path.
* Log the exception in the Orchestrator logs.
* Invokes the `utils_close_apps` workflow to clean the execution environment.
* If exists more attempts, then resets the variables and retry the state it was left in.
* If there are no more attempts, continue with the FINAL_STATE.

### FINAL_STATE
* Invokes the `utils_close_apps` workflow to clean the execution environment.
* Updates the process memory to local files and uploads it into the process Storage Bucket.
* If the process finished with a exception: sends an email to the respective recipients. If it is a system exception, **the exception screenshots are attached in the email**, and saved in the `out\_exceptions\` folder.
* If the process finished with a **system exception**: **rethrows the exception** for Orchestrator statistics and maintenance purposes.

## For a New Project

1. Copy the last standalone_template_X.X.X.zip package folder in the VM where the process will be developed.
2. Check out the `framework\configuration.xlsx` file and add/customize any required fields and values. To run the framework, you need to change the following parameters:
    * PROCESS_MAIN_FOLDER: This folder will be the main directory of the process on the machine where it will execute.
    * EMAIL_SERVER_TYPE & EMAIL_CREDENTIALS
3. Customize the `framework\inputs\_recipients.xlsx` file to define the recipients of the emails that Robot will send.
4. If necessary, update the `framework\wfs\_fmw\utils_close_apps.xaml` workflow adding the process required application.
5. Implement many workflows as the process needs and add them to the different states of the framework. You can use the wf templates in `framework\wfs\_templates` to help your development.
    * Design the worktray structure and build the worktray using the template `framework\wfs\_fmw\p001_build_worktray.xaml`
    * Each state should have at least one `p001_main_state_wf` to handle the process logic

### Points to consider
* The framework creates its own folders at the start of runs. It is the developer's responsibility to create other external output folders.
* It is essential to assign the path parameters of folders/files according to their type (relative or absolute):
    * **Relative paths** should be used for input files, so they are only changed if the process is published with a new version
    * **Absolute paths** must be used for temporary (process_data) and output files, since these files must be stored on the machine where the execution takes place. You can use the `PROCESS_MAIN_FOLDER` or any other path folder parameter in other configuration parameters to avoid using hard-coded absolute paths (i.e. `{PROCESS_MAIN_FOLDER}\process_data`)
* The default email configuration in the framework is using UiPath Iintegration Service of `int-service-office365`. This argument should be changed in the `configuration.xlsx` file if the robot's email is from another email service.
* You can add or remove states depending on the process that is being developed. **It is always recommended to have a state that reports the execution of the process. You can use `wfs\_templates\p001_send_execution_report.xaml` workflow as a template**
* You must create the `framework\inputs\templates\_worktray_template.xlsx` for each process with the necessary columns and use it to have an execution memory that allows the process to retry the states without problems.

**It is important to design a good state machine to get a REM (robust, efficient and maintainable) process over time!**

**HAPPY AUTOMATION!**