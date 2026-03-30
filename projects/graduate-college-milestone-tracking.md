# Graduate College Milestone Tracking System  
Enterprise Workflow and Governance Platform

---

## Problem

The Graduate College relied on a fragmented process to manage doctoral milestones, using email-based approvals, manual spreadsheets, and inconsistent workflows across programs. This approach resulted in approval delays, limited visibility into student progress, and no centralized reporting or accountability.

At the same time, the process required strict enforcement of academic policies, including multi-stage approvals, faculty eligibility rules, and role-based access for students, faculty, and administrators. The existing system lacked the structure and flexibility to support these requirements or adapt to changing business rules without significant manual effort and ongoing IT involvement.

---

## Solution

To address these challenges, I designed and implemented a centralized, multi-application solution using Microsoft Power Platform. The system combines a Canvas App for end-user interaction, a Model-Driven App for administrative governance, and automated workflows to manage approvals and reporting.

This approach standardized milestone processes across programs, improved visibility for both students and staff, and allowed business rules to be controlled through data rather than requiring application changes. The result is a scalable platform that supports complex academic workflows while remaining flexible and maintainable.

![Canvas App - Home Page - Staff selections](../Images/Home_CollegeForms.jpg)
---

## Architecture Overview

The solution is structured using a layered architecture that separates user experience, data management, and automation. This design improves scalability, simplifies maintenance, and allows each component to evolve independently while supporting the overall workflow.

### User Experience Layer

**Canvas App**
- Student submissions (Milestones 6, 7, 9)  
- Staff submissions (Milestones 1–5, 8)  
- Role-based user experience  
- Milestone tracking and approval visibility  

**Model-Driven App**
- Administrative data management  
- Governance configuration  
- Approval monitoring  

---

### Data Layer

**Dataverse (System of Record)**
- Milestones  
- Committees  
- Faculty eligibility  
- Program hierarchy  
- Approval tracking  

**SharePoint (Reporting Layer)**
- Nightly synchronized datasets  
- Optimized for performance and reporting  

---

### Automation Layer

- Multi-stage approval workflows  
- Reminder and timeout logic  
- CSV report generation and distribution  
- Nightly data synchronization (Dataverse to SharePoint)  

---

## Role-Based Design

The system supports distinct user experiences based on role, ensuring that each group interacts with the system in a way that aligns with their responsibilities.

### Students
- Submit Milestones 6, 7, and 9  
- View milestone completion status  ![MilestonesCompleted](../Images/MilestonesCompelted_1_Edited.jpg)
- Track pending approvals  ![Track Pending Approvals](../Images/MS6_PendingCommittee_Edited.jpg)

### College Staff
- Submit Milestones 1–5 and 8  
- View student progress and committee assignments  
- Access reporting and administrative views  

---

## Approval Workflow Design

The system supports both student-initiated and staff-initiated workflows, each with distinct approval paths while maintaining consistent governance rules.

### Student-Driven Approvals (Milestones 6, 7, 9)

- Program Director  
- Associate Dean  
- Committee Chair  
- Committee Members  
- Dean’s Representative

Milestone 6 Chair approval request example with due date: ![MS6EmailChair](../Images/MS6EmailChair_Edited.jpg)

Milestone 6 Approval Reminder example with due date and Button navigating the the Approval by environment and approval ID: ![MS6EmailReminder](../Images/EmailReminder_Edited.jpg)

### Staff-Driven Approvals (Milestones 4, 5, 8)

- Submitted by staff  
- Routed through defined approval chains to ensure oversight  

### Dependency Enforcement

The system enforces milestone sequencing to maintain process integrity. For example, students cannot submit Milestone 7 or Milestone 9 until Milestone 6 has been fully approved, as later milestones depend on an approved committee structure.

---

## Governance and Data Management (Model-Driven App)

A Model-Driven App provides a centralized interface for managing system data, business rules, and administrative processes. This allows staff to control system behavior through configuration rather than requiring application updates.

### Approved Faculty

![Approved Faculty](../Images/AdminScreen_ApprovedFaculty_Edited.jpg)

Faculty eligibility is controlled through defined levels:
- Level 2: Eligible to serve as Chair or Member  
- Level 1: Eligible to serve as Member only  
- Level 0: Not eligible and excluded from selection  

This ensures that committee assignments comply with academic policies and only allows students to select eligible faculty for committee role.

PowerFx Collection in Canvas App to get approved faculty for selections: ![Approved Collection](../Images/Collection_ApprovedFaculty.jpg)

Level 2 Faculty Selections in Canvas App based on Collectino (FIlter: DorcoralLevel = 2): ![Approved Faculty](../Images/MS6_3_Edited.jpg)

---

### Program Director List

Stores Program Directors, Associate Deans, and Program Coordinators.  

Used for:
- Approval routing  
- Dropdown selection choices in Canvas App
- Role-based access control in Canvas App

---

### Button Statuses

Controls whether milestone forms are available for submission.  

This allows staff to enable or pause submissions without modifying the application.

---

### Approval Tracking

A custom approval tracking layer extends native approval functionality by:

- Providing centralized visibility into approval activity  
- Allowing staff to monitor progress and history  
- Displaying pending approvals within the Canvas App

![Approved Faculty](../Images/Admin_Approvals_Edited.jpg)
---

### Faculty Serving

Allows staff to select a faculty member and view all committees they are currently serving on, supporting visibility into faculty workload and participation.

Custom Page
![Faculty Serving_Custom Page](../Images/Admin_FacultyServing_CustomPage.jpg)

---

### Student Committees

Two views are provided:
- A display view as Custom Page  
- An administrative view for managing records in Dataverse  

This separation ensures both usability and data control.

---

### Milestones Met

Tracks all completed milestones for each student, including completion dates.  

Supports reporting, dashboards, and data exports.

---

## Reporting Strategy

A nightly automation process synchronizes data from Dataverse to SharePoint to create a reporting layer optimized for performance and accessibility.

Staff can:
- View aggregated data directly in the application  
- Receive CSV reports via email  

---

## Key Design Decisions

### Configuration-Driven Architecture

Business rules are controlled through Dataverse tables, allowing changes to be made without modifying the application.

This includes:
- Faculty eligibility rules  
- Submission availability  
- Approval routing logic  

---

### Performance Optimization

SharePoint is used as a reporting layer to reduce direct load on Dataverse and improve application responsiveness.

---

### Security and Access Strategy

- Role-based user experience and data access  
- Controlled exposure of data  
- Service account-driven workflows for reliability and continuity  

---

### Environment Strategy (DEV / TEST / PROD)

The solution was designed using a structured application lifecycle management (ALM) approach with separate Development, Test, and Production environments. This ensured that new features, configuration changes, and workflow updates could be developed and validated without impacting live users.

Environment-specific configuration was handled dynamically using environment variables and conditional logic within the application. For example, data sources such as SharePoint lists and Dataverse tables were selected at runtime based on the current environment, allowing the same application and workflows to operate across DEV, TEST, and PROD without requiring manual changes.

This approach enabled:
- Controlled testing and validation before production releases  
- Reduced risk of introducing errors into live workflows  
- Consistent deployment across environments using solution-based ALM practices  
- Improved maintainability by centralizing environment configuration logic  

![ListFormula](../Images/Formulas.jpg)

---

## Challenges and Solutions

**Complex workflow requirements**  
Designed dynamic approval routing logic to support multiple workflow paths.

**Licensing constraints**  
Implemented parent/child flow patterns to securely access Dataverse without requiring premium licenses for all users.

**Data performance and accessibility**  
Introduced a hybrid Dataverse and SharePoint model to balance performance and usability.

**Changing business rules**  
Built a configuration-driven system allowing updates without application changes.

---

## Impact

This solution transformed a fragmented, manual process into a centralized and scalable platform. By automating approvals, enforcing governance rules, and improving visibility, the system significantly reduced administrative overhead while improving the experience for both students and staff.

It also enabled non-technical users to manage system behavior through data, reducing reliance on IT and ensuring the solution could evolve with changing academic requirements.
