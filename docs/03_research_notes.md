# Research Notes

## 1. Windows Task Scheduler Overview
Task Scheduler is a built-in Windows utility that automates the execution of scripts, programs and tasks at specific intervals or specific events. It was designed to reduce mannual work, enhance system management and allow background processes to run without user intervention. The Task Scheduler dashboard shows you an overview of the tasks on your local machine, and displays an actions menu consisting of functions such as creating tasks, importing tasks, displaying tasks and disabling tasks. 

https://learn.microsoft.com/en-us/windows/win32/taskschd/about-the-task-scheduler 

## 2. How Scheduled Tasks Work
To create a task, several details need to be defined such as the name of the task and the action. One of the most important components of every task is its trigger. Users can choose time-based triggers and event-based triggers. Time-based triggers can be divided into three categories: one-time triggers at a specific time or date, recurring triggers such as daily, weekly and monthly and repetition triggers such as running every X minutes or seconds. Event-based triggers are more complex, they fall under categories including system start-up, user logon, system idle, on creation and more.

## 3. Legitimate Uses of Scheduled Tasks
Windows Task Scheduler is commonly used to automate routine system, application, and user-level tasks. These tasks can run at specific times, during system startup, when a user logs in, or when certain system events occur. Many scheduled tasks are created by Windows itself, third-party applications, antivirus tools, update services, backup software, and system maintenance utilities.

Legitimate scheduled tasks may include:

- Windows Update checks
- Microsoft Defender scans
- software update services, such as Adobe, Google Chrome, or Microsoft Edge updates
- system maintenance tasks
- disk cleanup or optimisation tasks
- backup and restore operations
- telemetry or diagnostic tasks
- cloud sync services
- application licensing checks
- startup tasks for trusted applications

Understanding legitimate scheduled tasks is important because not every automatically running task is malicious. Many trusted applications rely on scheduled tasks to keep software updated, run scans, synchronise files, or perform maintenance without requiring manual user action.

For this project, this means the system should not simply treat all recurring, hidden, or automatically triggered tasks as suspicious. Instead, it should consider the task name, publisher, executable location, command arguments, trigger type, and whether the task appears to belong to a known trusted application.

## 4. Scheduled Tasks as a Malware Persistence Mechanism
Malware authors, or hackers, use the Windows Task Scheduler for persistence by programming malicious code to execute automatically at specific triggers. This technique allows malware to survive reboots, regain access if a previous session was terminated, and evade detection.

Attackers typically configure tasks to run onLogon, OnStart or at recurring time intervals. They disguise malware by giving them names that mimic legitimate software such as GoogleUpdateTask to blend into the environment and remain undetected. Attackers also often use signed system binaries like cmd.exe or powershell.exe as the Action, supplying the malicious payload as command-line arguments to hide the initial source.

Attackers also use more advanced techniques to disguise and execute tasks. In extreme cases, the attacker will modify or remove the permission data linked to the scheduled task, meaning the task will still exist and run but it may become hidden from Task Scheduler GUI or be harder to see with normal constraints. 

## 5. Common Attack Techniques Involving Scheduled Tasks
As well as using persistence and recurring execution, malware actors may also use a priviledge escalation technique called User Access Control (UAC) bypass. Attackers schedule tasks to run under the privileged or elevated accounts such as SYSTEM or Administrator, granting the malware deep system access. Attackers can also use a technique called remote task creation. They use tools like schtasks or AT to push malware to remote systems across a network, utlising protocols like SMB or RPC to automate infections on connected devices. 

https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/schtasks 
https://learn.microsoft.com/en-us/troubleshoot/windows-client/system-management-components/use-at-command-to-schedule-tasks 

## 6. MITRE ATT&CK: Scheduled Task/Job Technique
MITRE ATT&CK is a cybersecurity knowledge base that documents real-world attacker behaviours. It organises these behaviours into tactics and techniques, helping security professionals understand how attackers gain access, maintain persistence, move through systems, and evade detection.

For this project, MITRE ATT&CK is useful because it provides a recognised framework for understanding how scheduled tasks are abused by attackers.

The MITRE ATT&CK technique most relevant to this project is T1053: Scheduled Task/Job. This technique describes how attackers abuse task scheduling features to execute malicious code at a specific time, on a recurring schedule, or when a particular system event occurs.

Scheduled task abuse is especially important because task scheduling is a normal operating system feature. This allows malicious tasks to blend in with legitimate automation, software updates, maintenance tasks, and system processes.

https://attack.mitre.org/techniques/T1053/ 

## 7. Indicators of Suspicious Scheduled Tasks
Suspicious indicators are task properties or behaviours that may suggest a scheduled task is being used for malicious purposes. A single suspicious indicator does not always mean a task is malware, but several indicators appearing together can increase the likelihood that the task is unsafe.

For this project, suspicious indicators are important because they form the basis of the detection rules and risk scoring system.

Suspicious scheduled tasks may include:

- unusual or misleading task names
- task names that imitate trusted Windows or software update tasks
- executable files running from suspicious locations
- PowerShell, CMD, WScript, or script-based execution
- encoded or obfuscated command arguments
- hidden task configuration
- tasks running at user logon or system startup
- tasks running with highest privileges
- tasks running under SYSTEM or administrator accounts
- unknown or unsigned executable files
- task actions that connect to remote URLs or download files
- very frequent execution intervals
- task metadata that does not match the executable publisher
- registry entries that do not match visible Task Scheduler entries

## 8. Suspicious Task Names and Masquerading
Masquerading is when an attacker makes malicious activity look like normal system or application behaviour. In the context of Windows Scheduled Tasks, this may involve giving a malicious task a name that resembles a trusted Windows service, software updater, browser updater, or maintenance process.

This is done to reduce suspicion during manual inspection. A user may ignore a task with a familiar-looking name, even if the actual executable path or command is suspicious.

Suspicious task names may include names that imitate legitimate services or update processes, such as:

- `WindowsUpdate`
- `MicrosoftUpdate`
- `SystemUpdate`
- `SecurityHealth`
- `DefenderUpdate`
- `ChromeUpdate`
- `GoogleUpdater`
- `AdobeUpdate`
- `OneDriveSync`
- `JavaUpdate`
- `DriverUpdate`
- `WinService`
- `SystemCheck`
- `MaintenanceTask`

These names are not automatically malicious. However, they become suspicious when the task name does not match the file path, publisher, command, or expected software location.

A task name by itself cannot prove that a task is malicious. Many legitimate tasks use update-related or maintenance-related names. For example, Google Chrome, Microsoft Edge, Adobe, and Windows services may all create scheduled tasks for updates or background maintenance.

The detection system should therefore avoid flagging tasks only because their names look generic. Instead, the task name should be compared against other task properties.

Attackers may use names that:

- copy or slightly modify legitimate Windows task names
- include trusted words such as `Microsoft`, `Windows`, `Security`, `Update`, or `Service`
- imitate antivirus, browser, or driver update tasks
- use vague names such as `SystemCheck`, `Maintenance`, or `ServiceHost`
- contain random strings to avoid obvious detection
- use misspellings that look similar to legitimate names
- use excessive spacing or unusual characters to hide the real name visually
- mirror legitimate tasks but use incorrect casing

## 9. Suspicious File Paths and Execution Locations
The executable path of a scheduled task shows where the task’s program or script is stored on the system. This is important because legitimate software usually runs from trusted installation directories, while malware often runs from user-writable or temporary locations.

A scheduled task running from a normal software directory is usually less suspicious than a task running from a temporary folder, downloads folder, or hidden user profile location.

For example, a task running from:

`C:\Program Files\Google\Update\GoogleUpdate.exe`

is less suspicious than a task named `GoogleUpdate` running from:

`C:\Users\User\AppData\Roaming\GoogleUpdate.exe`

The second example uses a trusted-looking name but runs from a user-writable location, which may indicate masquerading or persistence behaviour.

Suspicious scheduled tasks may run executables or scripts from locations such as:

```text
C:\Users\<User>\AppData\Roaming\
C:\Users\<User>\AppData\Local\
C:\Users\<User>\AppData\Local\Temp\
C:\Users\<User>\Downloads\
C:\Users\<User>\Desktop\
C:\ProgramData\
C:\Windows\Temp\
C:\Temp\
```

## 10. Suspicious Commands, Scripts, and Arguments
Scheduled tasks contain an action that tells Windows what program, script, or command to run. This action may include an executable path and additional command-line arguments. These arguments are important because they can reveal suspicious behaviour even when the task name looks normal.

For example, a task called `WindowsUpdateCheck` may appear legitimate, but if it runs an encoded PowerShell command, the task becomes much more suspicious.

Some legitimate Windows tools that attackers may abuse include:

```text
powershell.exe
cmd.exe
wscript.exe
cscript.exe
mshta.exe
rundll32.exe
regsvr32.exe
certutil.exe
bitsadmin.exe
wmic.exe
schtasks.exe
```

## 11. Hidden Tasks and Privilege Escalation Indicators
Some scheduled tasks may be configured in ways that make them harder to notice or more powerful than normal user-level tasks. This can include hidden task settings, unusual user accounts, elevated permissions, or tasks running under privileged accounts such as `SYSTEM`.

In legitimate cases, some Windows and security-related tasks may need elevated privileges to perform maintenance, updates, or system checks. However, attackers can also abuse these features to make malicious tasks run with greater control over the system.

A hidden scheduled task is not always malicious, but it can be suspicious when combined with other indicators such as risky file paths, encoded commands, unknown executables, or logon/startup triggers.

Attackers may hide tasks to reduce the chance of a user or administrator noticing them during manual inspection.

Hidden task behaviour may include:

- task marked as hidden
- task not visible in the normal Task Scheduler interface
- task visible in the Registry but missing from normal task queries
- task stored with unusual permissions
- task with tampered Security Descriptor information
- task with a misleading or empty description

## 12. Existing Windows Tools for Task Inspection
Windows provides several built-in tools that can be used to inspect scheduled tasks. These tools allow users and administrators to view task names, triggers, actions, run conditions, user accounts, and task history.

The most relevant tools are:

- Task Scheduler GUI
- `schtasks.exe`
- PowerShell ScheduledTasks module
- Event Viewer
- Windows Registry

These tools are useful for manual investigation, but they do not automatically classify scheduled tasks by security risk. They show the data, but they do not clearly tell the user which tasks are suspicious or why. TriggerGuard can improve this by turning raw task metadata into understandable risk classifications.

## 13. Limitations of Current Task Scheduler Visibility
Windows Task Scheduler provides useful visibility into scheduled tasks, including task names, triggers, actions, conditions, and run history. However, this visibility is mainly designed for task management rather than security analysis.

This means that while users can inspect scheduled tasks manually, they are not automatically shown whether a task appears safe, suspicious, or potentially malicious.

Task Scheduler can contain many tasks created by Windows, Microsoft services, third-party applications, update tools, drivers, and user-installed software. A user or administrator may need to inspect each task individually to understand what it does.

This can be time-consuming because each task may need to be checked for:

- task name
- task description
- action path
- command-line arguments
- trigger type
- author
- user account
- run privileges
- hidden status
- last run time
- task history

Without automated analysis, suspicious tasks can be missed.

TriggerGuard should not replace existing Windows tools. Instead, it should make scheduled task inspection easier by analysing the data those tools expose and presenting it in a more security-focused way.

## 14. Rule-Based Malware Detection
Rule-based malware detection is a detection approach that uses predefined rules to identify suspicious or malicious behaviour. Instead of relying on machine learning or prediction models, the system checks whether certain known suspicious patterns are present.

Rule-based detection is suitable for this project because scheduled task abuse often contains recognisable patterns. Attackers may use suspicious paths, misleading task names, encoded PowerShell commands, hidden windows, or logon/startup triggers.

A rule-based system is also easier to explain than a machine learning model. Since this project aims to provide clear risk explanations, rules allow the dashboard to show exactly why a task was flagged.

## 15. Fuzzy Logic for Risk Scoring
Fuzzy logic is a reasoning approach that allows values to exist between strict categories. Instead of classifying something as only true or false, fuzzy logic allows partial membership in different categories.

In this project, fuzzy logic can be used to classify scheduled tasks by risk level. This is useful because scheduled task behaviour is not always clearly safe or clearly malicious. A task may have some suspicious features, but not enough evidence to prove that it is malware.

For example, a task running from `AppData` may be suspicious, but it is not automatically malicious. However, a task running from `AppData`, starting at logon, and using encoded PowerShell is much more dangerous.

Fuzzy logic is useful because scheduled task risk exists on a spectrum. A simple yes/no decision may create too many false positives or miss suspicious combinations.

Fuzzy logic still depends on the quality of the rules and input categories. If the rules are poorly designed, the risk score may be inaccurate. It also cannot automatically learn new malware behaviours unless the rules are updated. This means the system will need regular rule improvements as new suspicious patterns are discovered.

## 16. Explainable Risk Classification
Explainable risk classification means that the system does not only assign a risk level to a scheduled task, but also provides a clear reason for the classification.

In this project, each scheduled task should be classified as Low, Medium, High, or Critical risk. However, the risk label alone is not enough. The user should also be able to understand which task properties caused the classification.

For example, instead of only showing:

Critical Risk

The system should explain:

Critical Risk: Task uses encoded PowerShell, hides the execution window, runs at user logon, and executes with highest privileges.


## 17. Dashboard Design for Cybersecurity Tools
A cybersecurity dashboard is a visual interface that presents security-related information in a clear, organised, and actionable way. Instead of showing raw technical data only, a good dashboard helps users understand the current security state of a system and prioritise what needs attention.

For this project, the dashboard should display scheduled tasks, their risk levels, suspicious indicators, and explanations for why each task has been classified as low, medium, high, or critical risk.

Scheduled task data can be technical and difficult to interpret. A user may need to understand task names, file paths, command arguments, triggers, hidden settings, privileges, and registry information. If this information is displayed poorly, suspicious tasks may be missed.

A well-designed dashboard should make the most important information visible first.

The user should be able to quickly answer:

- Which tasks are most suspicious?
- Why were they flagged?
- What command or file path is being executed?
- Does the task run at logon or startup?
- Is the task hidden or elevated?
- Which tasks should be investigated first?

The dashboard should not overwhelm the user with too much information at once. Instead, it should show a summary first, then allow the user to inspect more detail when needed.

The dashboard could be split into four main areas:

```text
1. Summary panel
2. Risk distribution section
3. Scheduled task table
4. Detailed task inspection panel
```

## 18. Existing Tools and Similar Products
Existing tools can already be used to inspect scheduled tasks, startup behaviour, system logs, and malware-related activity. However, many of these tools are either general-purpose, advanced, enterprise-focused, or require the user to manually interpret the results. This section compares similar tools and identifies the gap that TriggerGuard aims to address.

| Tool / Product | Main Purpose | Strengths | Limitations | Design Lesson for TriggerGuard |
|---|---|---|---|---|
| Windows Task Scheduler | Built-in Windows tool for creating, viewing, and managing scheduled tasks | Easy to access, built into Windows, shows task names, triggers, actions, and conditions | Does not provide risk scoring, suspicious indicator detection, or clear security explanations | TriggerGuard should build on task data but present it in a security-focused way |
| `schtasks.exe` | Command-line tool for querying, creating, changing, and deleting scheduled tasks | Useful for exporting task data, supports command-line automation, can output task information | Requires command-line knowledge and manual interpretation | TriggerGuard could use command-line task data but make the output easier to understand |
| PowerShell ScheduledTasks module | PowerShell commands for retrieving and managing scheduled task information | Provides structured access to task details and can be scripted | Requires PowerShell knowledge and does not classify risk automatically | TriggerGuard can use structured task data and convert it into risk classifications |
| Event Viewer | Windows tool for viewing system, security, and application logs | Useful for investigating task history, execution events, and system behaviour | Can be noisy, complex, and difficult for beginners to interpret | TriggerGuard should avoid overwhelming users and show only relevant investigation details |
| Windows Registry | Stores scheduled task metadata, including TaskCache entries | Can reveal hidden or inconsistent task records | Advanced, risky to edit, and not beginner-friendly | Future versions of TriggerGuard could compare Registry entries with visible task output |
| Sysinternals Autoruns | Microsoft tool for viewing programs configured to start automatically | Powerful persistence inspection tool, includes scheduled tasks and many other autostart locations | Broad rather than scheduled-task-focused, can be overwhelming, requires user interpretation | TriggerGuard should be more focused and explainable for scheduled task analysis |
| Microsoft Defender | Antivirus and endpoint protection tool | Provides malware detection, real-time protection, and system scanning | Does not provide a dedicated scheduled task risk dashboard for learning or manual inspection | TriggerGuard should complement antivirus by explaining suspicious scheduled task behaviour |
| Sysmon | Microsoft Sysinternals tool for logging detailed system activity | Useful for threat hunting, process creation logs, and forensic investigation | Requires configuration and technical knowledge; produces logs rather than simple classifications | Future versions of TriggerGuard could use Sysmon logs to enrich task analysis |
| SIEM tools | Centralised log collection and security monitoring platforms | Useful for enterprise monitoring, alerts, dashboards, and correlation across systems | Complex, expensive, and designed for organisations rather than individual users | TriggerGuard can borrow risk-prioritisation ideas while remaining lightweight and local |
| EDR/XDR platforms | Advanced endpoint and cross-platform threat detection tools | Behavioural detection, investigation, response features, and threat intelligence integration | Enterprise-focused, may be expensive, and detection logic may not be transparent | TriggerGuard should remain explainable, focused, and suitable for a student-built desktop tool |
| Open-source threat hunting tools | Community tools for malware analysis, persistence inspection, and endpoint investigation | Often free, flexible, and useful for learning | May require setup, technical knowledge, and may lack a polished interface | TriggerGuard should combine technical detection with a clear dashboard and explanations |

Existing Windows and cybersecurity tools provide useful ways to inspect scheduled tasks, startup behaviour, system logs, and malware-related activity. However, most of these tools either require manual interpretation, advanced technical knowledge, or enterprise-level infrastructure.

Windows Task Scheduler, `schtasks.exe`, PowerShell, Event Viewer, and the Registry can expose scheduled task information, but they do not automatically explain whether a task appears suspicious. Security tools such as Microsoft Defender, Sysmon, Autoruns, SIEM platforms, and EDR/XDR systems provide stronger security visibility, but they are broader than scheduled task analysis and may be too complex for a focused, beginner-friendly dashboard.

This creates a gap for TriggerGuard. The purpose of TriggerGuard is not to replace existing security tools, but to provide a focused scheduled task risk analysis tool that classifies tasks as Low, Medium, High, or Critical risk and explains the reasons behind each classification. This supports faster investigation, clearer visibility, and better understanding of scheduled task-based persistence threats.

## 19. Key Findings and Design Implications
This section summarises the most important findings from the research and explains how they influence the design of the system. The purpose is to connect the research directly to the planned features, detection logic, and dashboard design.

Rather than only describing scheduled task threats, this section explains what the system should do because of those threats.

### Key finding 1: Scheduled tasks are legitimate but can be abused

Scheduled tasks are a normal part of Windows. They are used by the operating system, software updates, antivirus tools, backup software, maintenance utilities, and user applications. However, the same functionality can be abused by attackers to run malicious code automatically.

#### Design implication

The system should not treat every scheduled task as suspicious. It should analyse context, including task name, executable path, command arguments, trigger type, visibility, and privilege level.

The tool should use risk scoring rather than a simple safe-or-malicious decision.

---

### Key finding 2: Scheduled tasks are a known persistence technique

Research into MITRE ATT&CK shows that scheduled tasks are a recognised attack technique, especially under Windows Scheduled Task abuse. Attackers may use scheduled tasks to maintain access, run code after reboot, execute commands at logon, or hide malicious behaviour inside normal system automation.

#### Design implication

The detection system should focus on persistence-related indicators such as:

- tasks running at user logon
- tasks running at system startup
- recurring execution
- hidden task settings
- elevated privileges
- suspicious commands or scripts

Tasks with logon or startup triggers should receive increased risk when combined with other suspicious indicators.

---

### Key finding 3: Suspicious file paths are strong risk indicators

Malware often uses user-writable or temporary locations because they are easier to write to than protected system folders. Locations such as `AppData`, `Temp`, `Downloads`, and unusual `ProgramData` subfolders can be suspicious when used as execution paths.

#### Design implication

The detection engine should analyse executable paths and increase risk for tasks running from suspicious locations.
