## 2. User Personas &amp; Use Cases

### Persona 1: The Requester (End User)

**Profile:**

* *Role:* Employee, student, external customer

* *Goal:* Submit a request and track its progress

* *Pain Point:* Doesn&#39;t know where the ticket is stuck; no feedback until rejection

**Key Needs:**

* Submit ticket in &lt;2 clicks

* Real-time status updates

* Visibility into remaining approvers

<br>

### Persona 2: The Processor (Employee)

**Profile:**

* *Role:* Manager, HR staff, procurement officer, department head

* *Goal:* Review and act on tickets efficiently; prioritize urgent items

* *Pain Point:* Inbox overflowed with mixed-priority tickets; can&#39;t see which ones are overdue

**Key Needs:**

* Intelligent inbox sorting (by deadline, urgency, time pending)

* Batch actions (approve 5 similar leaves at once)

* Delegation capability (reassign to colleague)

<br>

### Persona 3: The System Administrator (Architect)**

**Profile:**

* *Role:* Workflow design engineer, compliance officer

* *Goal:* Define workflows, audit activity, ensure regulatory compliance

* *Pain Point:* Manual log reviews; no way to visualize bottlenecks across workflows

**Key Needs:**

* Workflow designer interface (state machine builder)

* Advanced audit queries (show all rejections on leave approvals in Q3)

* Bottleneck reports (which approval step takes longest?)

<br><hr>

### Scenario A: Memo Passing (Sequential Workflow)

**Flow:**

1. **Requester** drafts a memo, tags it with topic (e.g., &quot;Budget Adjustment&quot;), and **raises a ticket**

2. System auto-assigns to **Reviewer 1** (Finance Lead)

   2. Reviewer 1 reads memo, **approves**

   2. State: In Review → Approved Step 1

3. Reviewer 1 escalates to **Reviewer 2** (CFO)

   3. CFO reviews, **rejects** with comment: &quot;Needs cost breakdown&quot;

   3. State: Approved Step 1 → Rejected

4. System notifies **Requester**; ticket moves back to **Draft**

5. Requester revises memo, resubmits

6. Both reviewers approve; memo reaches **Completed**

**Auditability:** Full timeline logged—who reviewed, when, and what they said.

<br>

### Scenario B: Office Approval Chain (Parallel Escalation)

**Flow:**

1. Employee requests **time off** (3 days)

   1. State: Raised

2. System assigns to **Direct Manager**

   2. Manager approves immediately

   2. State: In Review (Manager)

3. System checks if &gt;5 days: No, so skips VP, escalates to **HR for compliance**

   3. HR approves

   3. State: In Review (HR)

4. Both approvals complete → **Approved**

5. System updates calendar, notifies finance for payroll adjustment

   5. State: Completed

**Bottleneck Detection:**

* If HR takes &gt;3 days to respond, system flags it for admin review

* Report: &quot;HR approval time: avg 2.1 days; 95th percentile 5.2 days&quot;