# Requirements

## 1. Requirements Overview
This document defines the main requirements for TriggerGuard. The system is intended to scan Windows scheduled tasks, identify suspicious task characteristics, calculate a risk level, and present the results through a clear dashboard.

The requirements are divided into functional requirements, non-functional requirements, data requirements, security requirements, user interface requirements, detection requirements, and future requirements.

## 2. Functional Requirements
Functional requirements describe what the system should do.

| ID | Requirement | Description | Priority |
|---|---|---|---|
| FR1 | Scan scheduled tasks | The system shall scan scheduled tasks on the local Windows machine. | Must have |
| FR2 | Extract task metadata | The system shall extract task name, task path, trigger, action, command arguments, hidden status, run level, and user account where available. | Must have |
| FR3 | Display scheduled tasks | The system shall display scanned scheduled tasks in a dashboard table. | Must have |
| FR4 | Analyse suspicious indicators | The system shall analyse tasks for suspicious names, paths, commands, triggers, visibility settings, and privilege levels. | Must have |
| FR5 | Assign risk score | The system shall calculate a numerical risk score for each scheduled task. | Must have |
| FR6 | Assign risk level | The system shall classify each task as Low, Medium, High, or Critical risk. | Must have |
| FR7 | Explain classification | The system shall provide a plain-English explanation for why a task received its risk level. | Must have |
| FR8 | Filter by risk level | The system shall allow users to filter tasks by Low, Medium, High, and Critical risk. | Should have |
| FR9 | Search scheduled tasks | The system shall allow users to search for tasks by name, path, or command. | Should have |
| FR10 | View detailed task information | The system shall allow users to select a task and view detailed metadata and matched risk indicators. | Should have |
| FR11 | Export scan results | The system shall allow users to export scan results to a report file. | Could have |

## 3. Non-Functional Requirements
Non-functional requirements describe how the system should perform.

| ID | Requirement | Description | Priority |
|---|---|---|---|
| NFR1 | Usability | The dashboard shall be clear and understandable for non-specialist users. | Must have |
| NFR2 | Explainability | Risk classifications shall include understandable explanations rather than only numerical scores. | Must have |
| NFR3 | Performance | The system shall complete a local scheduled task scan within a reasonable time for normal user machines. | Must have |
| NFR4 | Reliability | The system shall handle missing, incomplete, or malformed task data without crashing. | Must have |
| NFR5 | Maintainability | Detection rules shall be structured so they can be updated or extended easily. | Must have |
| NFR6 | Security | The system shall not modify, delete, or disable scheduled tasks in the first version. | Must have |
| NFR7 | Portability | The system shall run on supported Windows desktop environments. | Should have |
| NFR8 | Accessibility | The dashboard shall use text labels as well as colour to communicate risk levels. | Should have |
| NFR9 | Scalability | The system shall handle a large number of scheduled tasks without becoming difficult to use. | Should have |

## 4. Data Requirements
Data requirements describe the information the system needs to collect, process, store, and display.

| ID | Requirement | Description | Priority |
|---|---|---|---|
| DAR1 | Task name | The system shall collect the name of each scheduled task. | Must have |
| DAR2 | Task path | The system shall collect the task path or folder location within Task Scheduler. | Must have |
| DAR3 | Task action | The system shall collect the executable, script, or command that the task runs. | Must have |
| DAR4 | Command arguments | The system shall collect command-line arguments where available. | Must have |
| DAR5 | Trigger information | The system shall collect trigger information, such as logon, startup, daily, weekly, or repeated execution. | Must have |
| DAR6 | Hidden status | The system shall identify whether a task is marked as hidden where available. | Should have |
| DAR7 | Run level | The system shall collect whether the task runs with normal or highest privileges where available. | Should have |
| DAR8 | User account | The system shall collect the account used to run the task where available. | Should have |
| DAR9 | Last run information | The system shall collect last run time and last run result where available. | Could have |
| DAR10 | Risk data | The system shall store the calculated risk score, risk level, matched rules, and explanation for each task. | Must have |
| DAR11 | Local storage | The system shall store scan results locally if persistent storage is implemented. | Could have |
| DAR12 | No unnecessary personal data | The system shall avoid collecting unnecessary personal or sensitive user data beyond what is required for task analysis. | Must have |

## 5. Security Requirements
Security requirements describe how the system should protect the user and avoid creating additional risk.

| ID | Requirement | Description | Priority |
|---|---|---|---|
| SR1 | Read-only scanning | The first version of the system shall inspect scheduled tasks without modifying, disabling, deleting, or creating tasks. | Must have |
| SR2 | Local analysis | The system shall perform scheduled task analysis locally on the user’s machine. | Must have |
| SR3 | No automatic deletion | The system shall not automatically remove or disable scheduled tasks, even if they are classified as high or critical risk. | Must have |
| SR4 | Safe recommendations | The system shall recommend investigation before removal, especially where false positives are possible. | Must have |
| SR5 | Least privilege | The system should run with the lowest permissions possible for normal scanning. | Should have |
| SR6 | Permission awareness | If administrator privileges are required for deeper inspection, the system shall clearly inform the user. | Should have |
| SR7 | Secure storage | If scan results are saved, they shall be stored locally in a controlled format such as JSON or SQLite. | Should have |
| SR8 | No execution of task actions | The system shall never execute the commands, scripts, or executables found in scheduled task actions. | Must have |
| SR9 | Error handling | The system shall handle restricted, inaccessible, or malformed task data safely without crashing. | Must have |
| SR10 | Auditability | The system should make it clear which rules caused a task to be flagged. | Should have |

## 6. User Interface Requirements
User interface requirements describe how the dashboard should present information to the user.

| ID | Requirement | Description | Priority |
|---|---|---|---|
| UIR1 | Dashboard view | The system shall provide a dashboard for viewing scheduled task scan results. | Must have |
| UIR2 | Task table | The dashboard shall display scanned tasks in a table format. | Must have |
| UIR3 | Risk level display | The dashboard shall clearly display each task’s risk level as Low, Medium, High, or Critical. | Must have |
| UIR4 | Risk score display | The dashboard should display the numerical risk score for each task. | Should have |
| UIR5 | Main reason display | The dashboard shall show a short explanation of the main reason each task was flagged. | Must have |
| UIR6 | Detailed task panel | The system shall allow users to select a task and view detailed information, including action, arguments, trigger, hidden status, privilege level, and matched rules. | Should have |
| UIR7 | Risk filtering | The dashboard shall allow users to filter tasks by risk level. | Should have |
| UIR8 | Search function | The dashboard should allow users to search tasks by name, path, or command. | Could have |
| UIR9 | Sorting | The dashboard should allow users to sort tasks by risk score, name, trigger, or path. | Should have |
| UIR10 | Summary cards | The dashboard should display summary cards showing total tasks scanned and counts for each risk level. | Should have |
| UIR11 | Colour and text labels | Risk levels shall be communicated using both colour and text labels for accessibility. | Must have |
| UIR12 | Clear layout | The interface shall avoid overwhelming the user by showing summary information first and detailed information only when needed. | Must have |

## 7. Detection and Risk Scoring Requirements
| ID | Requirement | Description | Priority |
|---|---|---|---|
| DR1 | Suspicious path detection | The system shall flag tasks that execute from suspicious locations such as AppData, Temp, Downloads, or unusual ProgramData folders. | Must have |
| DR2 | Suspicious command detection | The system shall flag tasks using suspicious command arguments such as encoded PowerShell, hidden windows, execution policy bypass, or remote download commands. | Must have |
| DR3 | Masquerading detection | The system shall flag task names that imitate trusted software when the executable path or publisher does not match. | Should have |
| DR4 | Trigger analysis | The system shall increase risk for tasks running at logon, startup, or very frequent intervals when combined with other suspicious indicators. | Must have |
| DR5 | Privilege analysis | The system shall increase risk for tasks running with highest privileges, administrator accounts, or SYSTEM-level access. | Must have |
| DR6 | Hidden task analysis | The system shall identify tasks marked as hidden and increase risk when combined with other suspicious indicators. | Should have |
| DR7 | Risk scoring | The system shall combine matched indicators into a total risk score. | Must have |
| DR8 | Risk classification | The system shall map total risk scores to Low, Medium, High, or Critical risk levels. | Must have |
| DR9 | Explanation generation | The system shall generate an explanation based on the matched detection rules. | Must have |

## 8. Reporting Requirements
Reporting requirements describe how the system should present or export scan results.

| ID | Requirement | Description | Priority |
|---|---|---|---|
| RR1 | Scan summary | The system shall provide a summary of each scan, including total tasks scanned and the number of Low, Medium, High, and Critical risk tasks. | Must have |
| RR2 | Task-level results | The system shall report each task’s name, action, trigger, risk score, risk level, and explanation. | Must have |
| RR3 | Matched indicators | The system shall list the suspicious indicators or rules matched for each flagged task. | Must have |
| RR4 | Plain-English explanations | Reports shall include understandable explanations rather than only technical rule IDs. | Must have |
| RR5 | Export report | The system should allow users to export scan results to a file, such as CSV, JSON, or PDF. | Could have |
| RR6 | Timestamped reports | Exported reports should include the scan date and time. | Could have |
| RR7 | Prioritised findings | Reports should order tasks by risk level or score, with Critical and High risk tasks shown first. | Should have |
| RR8 | No destructive action in reports | Reports shall not encourage automatic deletion without investigation. | Must have |

## 9. Future Requirements

Future requirements describe features that are outside the first version of TriggerGuard but could be implemented later to improve detection, investigation, and usability.

| ID | Requirement | Description | Priority |
|---|---|---|---|
| FUR1 | Registry comparison | Future versions could compare visible scheduled tasks with TaskCache Registry entries to identify hidden or tampered tasks. | Future |
| FUR2 | Baseline comparison | Future versions could compare current scan results against previous scans to detect newly created, modified, or removed tasks. | Future |
| FUR3 | File signature verification | Future versions could check whether task executables are digitally signed and whether the publisher is trusted. | Future |
| FUR4 | Hash checking | Future versions could calculate file hashes for task executables and allow comparison against threat intelligence sources. | Future |
| FUR5 | VirusTotal integration | Future versions could optionally check suspicious file hashes against VirusTotal or similar services. | Future |
| FUR6 | Sysmon integration | Future versions could use Sysmon logs to check whether scheduled tasks have recently launched suspicious processes. | Future |
| FUR7 | Event Viewer integration | Future versions could inspect task creation and execution events from Windows logs. | Future |
| FUR8 | Fuzzy logic refinement | Future versions could improve the fuzzy scoring system using more detailed membership functions and rule tuning. | Future |
| FUR9 | Machine learning anomaly detection | Future versions could explore anomaly detection to identify unusual scheduled task behaviour compared with normal system behaviour. | Future |
| FUR10 | Remote machine scanning | Future versions could support scanning scheduled tasks on remote Windows machines in a controlled network environment. | Future |
| FUR11 | User-defined rules | Future versions could allow users to create, edit, import, or export custom detection rules. | Future |
| FUR12 | Whitelist support | Future versions could allow users to mark known safe tasks as trusted to reduce repeated false positives. | Future |
| FUR13 | Report comparison | Future versions could generate comparison reports showing changes between scans. | Future |
| FUR14 | Alert notifications | Future versions could notify users when a new high-risk or critical task appears. | Future |
| FUR15 | Safe remediation guidance | Future versions could provide step-by-step investigation guidance before disabling or removing suspicious tasks. | Future |
