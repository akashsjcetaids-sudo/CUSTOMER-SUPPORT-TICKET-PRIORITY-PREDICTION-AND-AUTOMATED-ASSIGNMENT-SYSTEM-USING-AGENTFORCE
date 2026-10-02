# CUSTOMER-SUPPORT-TICKET-PRIORITY-PREDICTION-AND-AUTOMATED-ASSIGNMENT-SYSTEM-USING-AGENTFORCE

## 📌 Overview

The **Customer Support Ticket Priority Prediction and Automated Assignment System** is a Salesforce-based solution that uses **Salesforce Flow and Agentforce** to analyze customer support tickets, automatically determine their priority, and initiate appropriate support actions.

The system retrieves the latest support ticket associated with a customer Account, analyzes the ticket description, classifies it as **High, Medium, or Low priority**, and triggers backend automation when required.

For High-priority tickets, the system creates an urgent handling Task and assigns the ticket to a senior support agent.

---

## 🎯 Problem Statement

Customer support teams often handle a large volume of tickets. Manual prioritization and assignment can result in:

* Delays in identifying critical tickets
* Important urgency signals being missed
* Increased manual effort for support agents
* Inconsistent ticket assignment
* Potential SLA-related risks

This project addresses these challenges by automating ticket analysis, priority classification, task creation, and support assignment.

---

## 💡 Solution

The solution combines:

* **Salesforce CRM** for customer and ticket data
* **Custom Salesforce Object** for support-ticket information
* **Auto-Launched Flow** for backend automation
* **Agentforce** for conversational ticket analysis
* **Decision logic** for High/Medium/Low priority classification
* **Task automation** for High-priority tickets
* **Support-level assignment** based on ticket priority

The overall workflow is:

```text
Customer / Support Request
          ↓
       Account
          ↓
 Latest Support Ticket
          ↓
  Ticket Description
          ↓
 Analyze Description
          ↓
 Priority Classification
    ↓       ↓       ↓
  High    Medium    Low
    ↓       ↓       ↓
Urgent    Normal   Queue
 Task     Handling Processing
    ↓
Senior Support Agent
```

The documented customer journey follows:

```text
Ticket Creation
      ↓
Account Identification
      ↓
Ticket Retrieval
      ↓
Description Analysis
      ↓
Priority Classification
      ↓
Agent Assignment
      ↓
Urgent Task Creation
      ↓
Support Handling
      ↓
Resolution
```

---

## 🛠️ Technology Stack

| Layer                     | Technology                       |
| ------------------------- | -------------------------------- |
| CRM Platform              | Salesforce                       |
| AI / Conversational Layer | Agentforce                       |
| Automation                | Salesforce Auto-Launched Flow    |
| Custom Object             | `Support_Ticket_Intelligence__c` |
| Related Records           | Account, Contact, User, Task     |
| Priority Levels           | High, Medium, Low                |

The project specifically uses the **Support Ticket Priority Analysis** Agentforce subagent.

---

## 🗃️ Data Model

### Custom Object

**Object:** Support Ticket Intelligence
**API Name:** `Support_Ticket_Intelligence__c`

### Fields

| Field           | API Name             | Data Type       | Description                 |
| --------------- | -------------------- | --------------- | --------------------------- |
| Ticket Number   | `Ticket_Number__c`   | Auto Number     | Ticket identifier           |
| Customer        | `Customer__c`        | Lookup(Account) | Related customer account    |
| Contact         | `Contact__c`         | Lookup(Contact) | Customer contact            |
| Issue Type      | `Issue_Type__c`      | Picklist        | Technical, Billing, General |
| Description     | `Description__c`     | Long Text Area  | Issue details               |
| Priority Level  | `Priority_Level__c`  | Picklist        | Low, Medium, High           |
| Status          | `Status__c`          | Picklist        | New, In Progress, Resolved  |
| Created Date    | `Created_Date__c`    | Date            | Ticket date                 |
| Assigned To     | `Assigned_To__c`     | Lookup(User)    | Support agent               |
| SLA Breach Risk | `SLA_Breach_Risk__c` | Checkbox        | SLA risk flag               |
| Resolution Time | `Resolution_Time__c` | Number          | Resolution time in hours    |

The custom object and field structure are documented in the project specification.

---

## ⚙️ Auto-Launched Flow

### Flow Name

`Support_Ticket_Intellegence`

### Flow Type

**Auto-Launched Flow**

The Flow can be called from Agentforce and does not require a record-triggered start.

### Input

```text
varAccountName
```

The Account Name is used to identify the customer whose latest support ticket needs to be analyzed.

### Outputs

```text
varAccountId
varTicketId
varPriorityLevel
varAssignedTo
varActionMessage
```

---

## 🔄 Flow Logic

The Flow performs the following operations:

### 1. Get Account

Searches the Salesforce **Account** object using:

```text
Name = varAccountName
```

The latest matching record is selected.

### 2. Store Account ID

The Account ID is stored in:

```text
varAccountId
```

### 3. Get Latest Ticket

The Flow searches:

```text
Support_Ticket_Intelligence__c
```

using the Account ID and retrieves the latest ticket.

### 4. Store Ticket ID

The ticket ID is stored in:

```text
varTicketId
```

### 5. Analyze Description

The ticket description is evaluated using configured keyword conditions.

### 6. Determine Priority

The system assigns:

```text
High
Medium
Low
```

### 7. High-Priority Check

If:

```text
varPriorityLevel = "High"
```

the Flow creates an urgent handling Task.

### 8. Create Urgent Task

For High-priority tickets:

```text
Subject  = Urgent Ticket Handling
Priority = High
Status   = Not Started
WhatId   = varTicketId
```

### 9. Assign Support Level

High-priority tickets are assigned to:

```text
Senior Support Agent
```

### 10. SLA Risk Check

The documented optional SLA check identifies tickets where the Created Date is older than two days.

### 11. Return Final Message

The Flow returns an appropriate action message based on the ticket priority.

The complete Flow structure is:

```text
Start
  ↓
Get Account
  ↓
Assignment - Account ID
  ↓
Get Ticket
  ↓
Assignment - Ticket ID
  ↓
Decision - Analyze Description
  ↓
Assignment - Priority Level
  ↓
Decision - High Priority
  ↓
Create Task
  ↓
Assignment - Assigned Agent
  ↓
Optional SLA Decision
  ↓
Assignment - Final Message
  ↓
End
```

---

## 🧠 Priority Classification

The current implementation uses configured keywords from the ticket description.

### 🔴 High Priority

A ticket is classified as High when the description contains:

```text
urgent
not working
failure
```

### 🟡 Medium Priority

A ticket is classified as Medium when the description contains:

```text
issue
slow
delay
```

### 🟢 Low Priority

If none of the configured High or Medium keywords are detected:

```text
Low
```

This is the documented keyword-based classification logic used by both the Flow and Agentforce configuration.

---

## 🤖 Agentforce Configuration

### Subagent

**Name:**

```text
Support Ticket Priority Analysis
```

**API Name:**

```text
Support_Ticket_Priority_Analysis
```

### Purpose

The Agentforce subagent analyzes customer support ticket descriptions, determines ticket priority, and triggers backend automation for task assignment.

### Scope

The Agentforce subagent is designed to:

* Analyze support ticket descriptions
* Determine High, Medium, or Low priority
* Trigger backend automation
* Support ticket-status and severity analysis
* Assign the appropriate support level

Unrelated activities such as billing, subscription management, and account updates are outside the documented scope.

---

## 💬 Agentforce Workflow

The Agentforce interaction follows this process:

```text
User provides Account Name
          ↓
Agentforce
          ↓
Support Ticket Priority Analysis
          ↓
Retrieve Latest Ticket
          ↓
Read Ticket Description
          ↓
Determine Priority
          ↓
Trigger Auto-Launched Flow
          ↓
Create Task if High Priority
          ↓
Assign Support Level
          ↓
Return Result to User
```

The Agentforce instructions specify that record updates and email sending should not be performed directly by the subagent; Flow is used for backend actions.

---

## 📤 Example Outputs

### High Priority

```text
High priority ticket detected.
Assigned to senior agent.
```

### Medium Priority

```text
Ticket marked as medium priority.
Will be handled shortly.
```

### Low Priority

```text
Ticket is low priority and queued for processing.
```

---

## 🔌 Agentforce Action

The Agentforce action uses the Flow to perform ticket intelligence processing.

### Input

```text
varAccountName
```

The user provides the customer Account Name.

### Outputs

```text
varAccountId
varTicketId
varPriorityLevel
varAssignedTo
varActionMessage
```

These outputs provide the Account ID, analyzed ticket ID, calculated priority, assigned support agent, and final action message to the Agentforce conversation.

---

## 🧪 Testing

The project includes functional and performance testing to validate the Salesforce implementation and its automation behavior.

The intended validation includes:

* Ticket retrieval
* Priority classification
* High-priority Task creation
* Support assignment
* Agentforce interaction
* Flow execution
* Output generation

---

## ✅ Key Features

* Automatic ticket prioritization
* High/Medium/Low classification
* Latest ticket retrieval
* Keyword-based description analysis
* Automatic urgent Task creation
* Senior-agent assignment for High-priority tickets
* Agentforce conversational interaction
* Auto-Launched Flow backend automation
* Optional SLA-risk checking
* Reduced manual ticket handling
* Scalable Salesforce-based automation

The project documentation identifies these as the main advantages of the solution.

---

## ⚠️ Current Limitations

The current implementation has several documented limitations:

1. **Keyword-Based Logic**
   Priority depends on configured keywords.

2. **Limited Context Understanding**
   The current logic may not recognize every possible urgency signal.

3. **Data Dependency**
   Accurate Account, Contact, and ticket information is required.

4. **Flow Dependency**
   Changes to ticket handling require Flow configuration updates.

5. **Agentforce Configuration Dependency**
   Agentforce behavior depends on correctly configured instructions and actions.

6. **Limited SLA Monitoring**
   SLA checking is currently optional rather than a complete escalation system.

7. **Assignment Rules**
   More sophisticated assignment rules would require additional configuration.

8. **Limited Analytics**
   Advanced priority and resolution analytics are outside the current implementation.

---

## 🚀 Future Scope

Potential future enhancements include:

* Add additional priority indicators
* Introduce more business rules
* Improve ticket-context analysis
* Extend Agentforce outputs
* Implement advanced SLA monitoring
* Add escalation mechanisms
* Support additional support teams
* Introduce advanced assignment rules
* Add priority-trend analytics
* Add resolution-performance analytics
* Add additional Agentforce variables
* Provide richer conversational outputs

The project documentation specifically identifies these areas as future scope.

---

## 📁 Project Components

```text
Customer Support Ticket Priority Prediction
│
├── Salesforce
│   ├── Account
│   ├── Contact
│   ├── User
│   ├── Task
│   └── Support_Ticket_Intelligence__c
│
├── Flow
│   └── Support_Ticket_Intellegence
│
├── Agentforce
│   └── Support Ticket Priority Analysis
│
└── Agentforce Action
    └── Flow-based Support Ticket Intelligence
```

---

## 📋 Project Requirements

### Functional Requirements

* Support ticket creation and management
* Account and Contact association
* Ticket description analysis
* Automatic priority classification
* High-priority Task creation
* Support agent assignment
* SLA breach-risk checking
* Agentforce conversational analysis

### Non-Functional Requirements

* Usability
* Reliability
* Performance
* Security
* Maintainability
* Scalability

---

## 🏁 Conclusion

The **Customer Support Ticket Priority Prediction and Automated Assignment System Using Agentforce** demonstrates how Salesforce Flow and Agentforce can work together to automate customer-support ticket handling.

The system retrieves the latest ticket for an Account, analyzes its description, determines priority, creates an urgent Task for High-priority cases, assigns a support level, and returns the result through Agentforce.

The documented implementation focuses on reducing manual effort, improving identification of urgent tickets, and providing a conversational interface for support-ticket analysis.

---

## 👨‍💻 Project Information

**Project:** Customer Support Ticket Priority Prediction and Automated Assignment System
**Platform:** Salesforce
**AI:** Agentforce
**Automation:** Salesforce Flow
**Custom Object:** `Support_Ticket_Intelligence__c`
**Flow:** `Support_Ticket_Intellegence`
**Agentforce Subagent:** `Support_Ticket_Priority_Analysis`

---

## 📄 Reference

This README is based on the project documentation supplied for the **Customer Support Ticket Priority Prediction and Automated Assignment System Using Agentforce**.
