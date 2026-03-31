# PEDS Financial Data Processing Automation (RPA)
Queue-Based Robotic Process Automation for Financial Data Validation and Entry

---

## Problem

The PEDS process supports internal financial adjustments between accounting structures and required staff to manually validate and enter correction data into a web-based system. Each submission could contain hundreds of rows, often with repeated account and date combinations that required identical validation checks.

The process was highly manual, time-consuming, and dependent on navigating a web interface, making it prone to delays, interruptions, and inconsistencies. As submission volume increased, staff were required to spend significant time performing repetitive validation and data entry tasks.

In addition, there was no centralized way to track submissions from intake through completion. Staff had limited visibility into processing status, failures, or partial completion, making it difficult to prioritize work, troubleshoot issues, or ensure timely processing.

---

## Solution

I designed and implemented a queue-based RPA system using Power Automate cloud flows, Power Automate Desktop, SharePoint, and a PowerApp interface to automate validation, data entry, and process tracking.

The solution separates responsibilities across multiple components:

- **Email Monitoring Flow**  
  Ingests incoming files, validates structure, and adds them to a centralized Queue

- **Queue Processing Flow**  
  Runs on a scheduled interval, retrieves the next item based on priority, and orchestrates execution of RPA workflows  

- **Desktop Automation (PAD)**  
  Performs validation and submits corrections into the PEDS web system  

- **PowerApp Interface**  
  Provides real-time visibility into the Queue, input logs, errors, and allows staff to manage retries, priorities, and manual intervention  

- **SharePoint Data Layer**  
  Stores Queue items, input logs, output files, and error tracking  

This architecture ensures that file intake, processing, and execution are decoupled, allowing for controlled, scalable, and resilient automation.

---

## Impact

The solution significantly reduced manual processing effort by automating repetitive validation and data entry tasks, while improving consistency and accuracy across submissions.

By introducing a centralized Queue and tracking system, staff gained full visibility into the lifecycle of each submission, from intake through completion. This enabled better prioritization of urgent items, faster troubleshooting of failures, and more reliable processing timelines.

The system also improved operational resilience by supporting retry logic, partial processing, and manual intervention when needed, ensuring that automation failures did not block overall progress.

Additionally, the solution established a scalable framework for RPA orchestration, allowing automation to run in a controlled, prioritized manner rather than as isolated or ad hoc processes.

---

## Key Challenges and How They Were Solved

**High volume of repetitive validation**  
→ Implemented deduplication logic to reduce redundant checks  

**Unreliable web UI interactions**  
→ Added targeted waits and retry logic in desktop automation  

**Lack of process visibility**  
→ Introduced Queue tracking and PowerApp interface  

**Need for prioritization**  
→ Designed queue-based processing with priority levels  

**Automation failures interrupting workflows**  
→ Built retry and manual intervention model  

---

## Validation and Intake Controls

A key part of the PEDS automation is the validation workflow, which acts as a gatekeeper before files enter the processing queue. Rather than allowing every submission to move directly into automated entry, the solution performs structured validation checks to confirm that files are complete, formatted correctly, and eligible for processing.

If validation errors are found, the file is not added to the queue. Instead, the automation marks the issues directly in the Excel file and returns it to the submitter by email so corrections can be made before resubmission. This prevents invalid data from entering the downstream automation process and reduces the need for staff to troubleshoot avoidable errors later.

### Validation Checks

The validation workflow checks for:

- Correct file and table structure
- Required fields and expected column layout
- Valid account and check date combinations
- Missing cost center or transfer amount/percentage
- Invalid or incomplete correction rows
- Transfer percentages greater than 100%
- Transfer amounts below the minimum valid threshold
- Amounts that exceed the available balance
- “Total” rows that incorrectly contain a correction request
- Check dates older than 90 days

### Validation Outcome

- **If the file passes validation:** it is added to the queue and becomes eligible for unattended processing.
- **If the file fails validation:** it is not queued, the issues are marked directly in the workbook, and the annotated file is emailed back to the submitter for correction and resubmission.

This approach improves data quality at the intake stage, protects the unattended automation from avoidable failures, and creates a cleaner separation between valid work ready for processing and files that require user correction.

---

## Key Design Decisions

### Environment Strategy (DEV / TEST / PROD)

The solution was implemented using a structured application lifecycle management (ALM) approach with separate Development, Test, and Production environments. This ensured that changes to automation logic, validation rules, and workflows could be developed and validated without impacting live processing.

Environment-specific configurations, including mailbox connections, SharePoint sites, and target systems, were isolated per environment, allowing controlled testing and consistent deployments across the solution.

This approach reduced risk during updates and improved overall maintainability of the RPA system.

---

### Unattended RPA Execution

The automation is executed using an unattended Power Automate Desktop bot running on a virtual machine, enabling processing to occur without manual intervention.

The unattended setup allows the queue-based workflow to trigger desktop automation on a scheduled basis, ensuring consistent processing of incoming files regardless of staff availability.

This design supports:
- reliable execution of long-running processes  
- reduced dependency on user sessions  
- scalability for increasing processing volume  

### Queue-Based Processing Model

A centralized Queue controls all file processing, ensuring that:
- Only one file is processed at a time  
- High-priority items are handled first  
- Files can be retried without duplication  

This approach allows dynamic prioritization and prevents conflicts in the target system.

---

### Separation of Intake and Execution

The email monitoring flow only adds items to the Queue, while a separate scheduled workflow controls processing.

This design:
- prevents duplicate processing  
- allows controlled execution timing  
- supports pause/resume functionality  

---

### Deduplication for Performance Optimization

The solution identifies repeated account and date combinations within files and validates each only once, significantly reducing redundant system calls and improving processing speed.

---

### Error Handling and Retry Mechanism

Failures are captured and logged at both the file and row level. Staff can review failed items, correct issues, and requeue them for processing.

---

### Human-in-the-Loop Design

A PowerApp interface allows staff to:
- monitor Queue status  
- prioritize submissions  
- retry failed items  
- manually process exceptions  

This ensures that automation is supported by oversight rather than replacing it entirely.

---

## Architecture Overview

The system follows a layered RPA architecture:

- **Intake Layer**
  - Email monitoring flow  
  - File ingestion and Queue creation  

- **Orchestration Layer**
  - Scheduled Queue processing  
  - Priority-based selection logic  

- **Execution Layer**
  - PAD Validation workflow  
  - PAD Data Entry workflow  

- **Monitoring & Control Layer**
  - PowerApp interface  
  - SharePoint Queue, Logs, and Error Lists 

---

### Power App Monitoring

Home Screen ![Canvas App - Home Page](../Images/PEDSHome.jpg)

Current Queue - Files are added as they arrive. Statuses available: New, In Progress, Retry, Worfklow Failed, Workflow Complete ![Canvas App - Home Page](../Images/PEDSQueue.jpg)

All PEDS Input - Logs all rows attempeted to process in website with "Processed" either Yes or No. Includes excel link and row number for reference. ![Canvas App - Home Page](../Images/PEDSAllItemsLog.jpg)

PEDS Errors List - Allows staff to see all rows that were not successfully processed and allows them to view details to manually enter row, or cancel if error persists. Also included a toggle to inform IT of the error if they believe the error was due to a workflow issue. ![Canvas App - Home Page](../Images/PEDSErrors.jpg)

Details Screen for Manually Entry - Staff can make a selection for "Processed". This will log current users name and email for reference of who changed the Processed Status ![Canvas App - Home Page](../Images/PEDSDetails.jpg)

 

