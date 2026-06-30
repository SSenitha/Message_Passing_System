# Stateful Workflow Engine &amp; Ticket Tracking Middleware

### Product Requirements &amp; Architecture Document (PRD)

**Overview:** Organizations lose visibility and control when business processes are manual, untracked, or scattered across tools. Document approvals stall, memo workflows create bottlenecks, and stakeholders lack auditability. This solution introduce an **API-first, stateful workflow engine** that models any business process as a ticket lifecycle, tracks state transitions in real-time, and surfaces visibility through a unified activity log. 

<br>

## 1. Project Overview &amp; Value Proposition

### The Core Problem

<table>
  <tr>
        <td> Problem Domain</td>
        <td> Current State</td>
        <td> Impact</td>
  </tr>
  <tr>
        <td> Manual Handoffs</td>
        <td> Email/chat-based approval chains</td>
        <td> Lost context, no audit trail </td>
  </tr>
  <tr>
        <td> Zero Visibility</td>
        <td> Stakeholders don&#39;t know ticket status</td>
        <td> Repeated follow-ups, wasted time</td>
  </tr>
  <tr>
        <td> Untracked Bottlenecks</td>
        <td> No history of delays or failures</td>
        <td> Can&#39;t optimize process flow</td>
  </tr>
  <tr>
        <td> Compliance Risk</td>
        <td> No centralized audit trail</td>
        <td> Audit failures, regulatory exposure</td>
  </tr>
</table>

    Our Solution: The Workflow Engine

<br>

A **stateful middleware layer** that:

* **Abstracts complexity**: Reduces any business process (approvals, approvals, supply chains) to a simple **state machine**

* **Ensures auditability**: Logs every state transition, actor, and timestamp in an immutable activity log

* **Enables prioritization**: Surfaces the most urgent tickets using intelligent algorithms

* **Scales via APIs**: Let external systems (university portals, ERP platforms) integrate seamlessly

* **Enforces compliance**: Soft-deletes preserve history; role-based access controls restrict sensitive workflows

<br>

**Why API-First?**

1. **Decoupled**: Frontend, backend, and third-party integrations are independent

2. **Scalable**: Support hundreds of concurrent workflows without bottlenecks

3. **Extensible**: Custom business logic layers on top without core changes

4. **Future-proof**: Integrate with legacy systems, modern SaaS, or mobile clients

<br><br>

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

### Scenario B: Office Approval Chain (Parallel Escalation)**

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

<br>

## 3. Core Functional Requirements: The CRUD Matrix**

**How Business Actions Map to CRUD Operations**

<table>
  <tr>
   <td> undefined</td>
   <td> undefined</td>
   <td> undefined</td>
   <td> undefined</td>
  </tr>
  <tr>
   <td> CREATE</td>
   <td> C</td>
   <td> Raise a new ticket</td>
   <td> Employee submits leave request</td>
  </tr>
  <tr>
   <td> READ</td>
   <td> R</td>
   <td> View ticket details &amp; timeline</td>
   <td> Approver sees memo + all comments</td>
  </tr>
  <tr>
   <td> UPDATE</td>
   <td> U</td>
   <td> Transition state / add action</td>
   <td> Manager approves; system moves ticket forward</td>
  </tr>
  <tr>
   <td> DELETE</td>
   <td> D</td>
   <td> Archive/retire ticket</td>
   <td> Close completed ticket; preserve in audit log</td>
  </tr>
</table>

Create (Raising a Ticket)

**System validates:**

* Requester has permission to raise this ticket type

* All required fields present (title, description, assignee rule)

* Assigns initial state: Raised

* Logs activity: [CREATED] Ticket #1234 by user@org.com at 2024-01-15 09:00 UTC

      Read (Viewing Tickets &amp; History)

**Endpoints return:**

* **Ticket summary:** ID, title, current state, assigned-to, created date, due date

* **Activity timeline:** Chronological log of all transitions, approvals, comments, and rejections

* **Access control:** Only users with role-based permission can view sensitive tickets

**Use case:** Approver opens dashboard, sees 47 pending tickets, filters by &quot;Overdue&quot; or &quot;Budget-related.&quot;

      * **Update (State Transitions &amp; Actions)**

**Valid state transitions** (see State Diagram below):

* Raised → In Review (auto, by system)

* In Review → Approved (manual, by approver)

* In Review → Rejected (manual, by approver with reason)

* Rejected → Draft (auto, notify requester)

* Approved → Completed (auto or manual, when all steps done)

**Logs every transition:**

[APPROVED] Ticket #1234 by manager@org.com (role: Manager) at 2024-01-15 10:30 UTC \
Reason: &quot;Budget looks good. Proceed.&quot;

      * **Delete (Soft-Delete &amp; Archival)**

**Why Soft-Delete (NOT Hard-Delete)?**

* **Compliance:** Audit trails must be immutable; hard deletes violate SOX, HIPAA, GDPR

* **Debugging:** Understand why a ticket was closed; full history preserved

* **Analytics:** Historical data remains available for bottleneck analysis

**Implementation:**

* Add is_archived = TRUE flag to tickets table

* Soft-deleted tickets hidden from active views but queryable via audit reports

* Implement **data retention policy:** Soft-deleted tickets auto-purged after 7 years (configurable)

**Example:**

-- User-initiated delete \
UPDATE tickets SET is_archived = TRUE, archived_at = NOW(), archived_by = user_id  \
WHERE ticket_id = 1234; \
 \
-- System still tracks it \
SELECT * FROM activity_log WHERE ticket_id = 1234; \
-- Returns full history even after soft-delete

 \


## 4. State Transition Diagram (Mermaid)**

```mermaid

stateDiagram-v2

    [*] --&gt; Raised

    Raised --&gt; InReview: Auto-assign to Approver 1

    InReview --&gt; ApprovedStep1: Approver 1 accepts

    InReview --&gt; RejectedReview: Approver 1 rejects

    ApprovedStep1 --&gt; InReview2: Escalate to Approver 2

    InReview2 --&gt; Approved: Approver 2 accepts

    InReview2 --&gt; RejectedReview: Approver 2 rejects

    RejectedReview --&gt; Draft: Notify requester; reset to draft

    Draft --&gt; InReview: Requester resubmits

    Approved --&gt; Completed: Final step done; all stakeholders notified

    Completed --&gt; [*]

    InReview --&gt; Escalated: SLA breach (&gt;2 days pending)

    Escalated --&gt; InReview: Admin reassigns to manager

    RejectedReview --&gt; Cancelled: Requester gives up (soft-delete)

    Cancelled --&gt; [*]

    note right of InReview

        Max 5 days pending

        Escalate if breached

    end note

    note right of RejectedReview

        Requester has 14 days

        to resubmit

    end note

```

      * **Key Transitions Explained**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

Raised

</td>
   <td>

In Review

</td>
   <td>

System auto-route

</td>
   <td>

System

</td>
   <td>

Happens instantly upon creation

</td>
  </tr>
  <tr>
   <td>

In Review

</td>
   <td>

Approved

</td>
   <td>

Manual approval

</td>
   <td>

Approver

</td>
   <td>

Logged with timestamp &amp; comment

</td>
  </tr>
  <tr>
   <td>

In Review

</td>
   <td>

Rejected

</td>
   <td>

Manual rejection

</td>
   <td>

Approver

</td>
   <td>

Reason required; resets to Draft

</td>
  </tr>
  <tr>
   <td>

Approved

</td>
   <td>

Completed

</td>
   <td>

Final step + notification

</td>
   <td>

System

</td>
   <td>

Triggers downstream actions (calendar updates, etc.)

</td>
  </tr>
  <tr>
   <td>

In Review

</td>
   <td>

Escalated

</td>
   <td>

SLA breach (2+ days)

</td>
   <td>

System

</td>
   <td>

Auto-escalate to manager if no response

</td>
  </tr>
</table>

 \


   * **5. Entity-Relationship Diagram (Mermaid)**

```mermaid

erDiagram

    USERS ||--o{ TICKETS : &quot;raises&quot;

    USERS ||--o{ TICKETS : &quot;approves&quot;

    TICKETS ||--o{ ACTIVITY_LOG : &quot;generates&quot;

    TICKETS ||--o{ COMMENTS : &quot;contains&quot;

    USERS ||--o{ COMMENTS : &quot;writes&quot;

    WORKFLOW_RULES ||--o{ TICKETS : &quot;defines&quot;

 

    USERS {

        int user_id PK &quot;Primary Key&quot;

        string email UK &quot;Unique email&quot;

        string full_name

        string role &quot;Enum: Requester, Approver, Admin&quot;

        string department &quot;e.g., Finance, HR, Ops&quot;

        boolean is_active &quot;Soft-delete flag&quot;

        timestamp created_at

        timestamp updated_at

    }

 

    TICKETS {

        int ticket_id PK &quot;Primary Key&quot;

        string ticket_number UK &quot;e.g., MEMO-2024-001&quot;

        int requester_id FK &quot;Foreign Key → Users&quot;

        int current_approver_id FK &quot;Foreign Key → Users&quot;

        string title

        text description

        string status &quot;Enum: Raised, InReview, Approved, Rejected, Completed, Archived&quot;

        string urgency_tier &quot;High, Medium, Low&quot;

        timestamp created_at

        timestamp updated_at

        timestamp due_date &quot;SLA deadline&quot;

        timestamp completed_at

        boolean is_archived &quot;Soft-delete flag&quot;

        int workflow_rule_id FK &quot;Foreign Key → WorkflowRules&quot;

    }

 

    ACTIVITY_LOG {

        int log_id PK &quot;Primary Key&quot;

        int ticket_id FK &quot;Foreign Key → Tickets&quot;

        int actor_id FK &quot;Foreign Key → Users&quot;

        string action &quot;Enum: CREATED, TRANSITIONED, APPROVED, REJECTED, COMMENTED&quot;

        string old_state &quot;Previous state&quot;

        string new_state &quot;New state&quot;

        text notes &quot;Approver comment&quot;

        timestamp action_timestamp &quot;When it happened&quot;

    }

 

    COMMENTS {

        int comment_id PK &quot;Primary Key&quot;

        int ticket_id FK &quot;Foreign Key → Tickets&quot;

        int author_id FK &quot;Foreign Key → Users&quot;

        text content

        timestamp created_at

    }

 

    WORKFLOW_RULES {

        int rule_id PK &quot;Primary Key&quot;

        string workflow_name &quot;e.g., LeaveApproval, MemoReview&quot;

        string description

        text state_machine_config &quot;JSON defining states &amp; transitions&quot;

        string approval_chain &quot;JSON: sequential or parallel&quot;

        int max_days_pending &quot;SLA threshold&quot;

        boolean is_active

        timestamp created_at

    }

```

      * **Schema Notes**

**Why This Design?**

* **Immutability:** ACTIVITY_LOG is append-only; never updated or deleted

* **Auditability:** Every action tied to an actor and timestamp

* **Scalability:** Tickets and Activity Log can be sharded by ticket_id or created_at

* **Compliance:** Soft-delete flags (is_archived, is_active) preserve history

* **Extensibility:** WORKFLOW_RULES allows admins to define custom workflows without code changes

**Indexing Strategy:**

CREATE INDEX idx_tickets_status ON tickets(status); \
CREATE INDEX idx_tickets_current_approver ON tickets(current_approver_id, status); \
CREATE INDEX idx_activity_log_ticket ON activity_log(ticket_id); \
CREATE INDEX idx_activity_log_timestamp ON activity_log(action_timestamp DESC); \
CREATE INDEX idx_tickets_created_at ON tickets(created_at DESC);

 \


   * **6. Business Logic Enhancement: Smart Prioritization Algorithm**

      * **The Challenge**

An approver has 47 pending tickets. Without logic, they see them in random order (oldest first, or alphabetical). Result: They approve low-impact items first and miss urgent deadlines.

      * **The Solution: Priority Score Formula**

PRIORITY_SCORE = (URGENCY_WEIGHT × URGENCY_TIER) + (AGE_WEIGHT × TIME_FACTOR)

**Where:**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

URGENCY_TIER

</td>
   <td>

{High: 3, Medium: 2, Low: 1}

</td>
   <td>

Set at ticket creation

</td>
  </tr>
  <tr>
   <td>

URGENCY_WEIGHT

</td>
   <td>

0.6

</td>
   <td>

Urgency matters most (60%)

</td>
  </tr>
  <tr>
   <td>

TIME_ELAPSED

</td>
   <td>

Hours since created_at

</td>
   <td>

Current time - created_at

</td>
  </tr>
  <tr>
   <td>

AGE_WEIGHT

</td>
   <td>

0.4

</td>
   <td>

Age matters second (40%)

</td>
  </tr>
  <tr>
   <td>

TIME_FACTOR

</td>
   <td>

log(TIME_ELAPSED + 1)

</td>
   <td>

Logarithmic; prevents very old items from dominating

</td>
  </tr>
</table>

      * **Example Calculation**

**Ticket A:**

* Urgency: High (3) | Raised 12 hours ago

* Score = (0.6 × 3) + (0.4 × log(12 + 1)) = 1.8 + (0.4 × 2.56) = 1.8 + 1.024 = **2.824**

**Ticket B:**

* Urgency: Low (1) | Raised 120 hours ago (5 days)

* Score = (0.6 × 1) + (0.4 × log(120 + 1)) = 0.6 + (0.4 × 4.79) = 0.6 + 1.916 = **2.516**

**Ticket C:**

* Urgency: Medium (2) | Raised 2 hours ago

* Score = (0.6 × 2) + (0.4 × log(2 + 1)) = 1.2 + (0.4 × 1.1) = 1.2 + 0.44 = **1.64**

**Inbox Order:** A → B → C

      * **Implementation (Pseudocode)**

def calculate_priority_score(ticket): \
    urgency_map = {&quot;High&quot;: 3, &quot;Medium&quot;: 2, &quot;Low&quot;: 1} \
    urgency_value = urgency_map[ticket.urgency_tier] \
     \
    time_elapsed_hours = (now() - ticket.created_at).total_seconds() / 3600 \
    age_factor = math.log(time_elapsed_hours + 1) \
     \
    priority_score = (0.6 * urgency_value) + (0.4 * age_factor) \
     \
    return priority_score \
 \
# In database: \
SELECT ticket_id, title, status, urgency_tier, created_at, \
       (0.6 * CASE urgency_tier WHEN &#39;High&#39; THEN 3 WHEN &#39;Medium&#39; THEN 2 ELSE 1 END) + \
       (0.4 * LN(EXTRACT(EPOCH FROM (NOW() - created_at))/3600 + 1)) AS priority_score \
FROM tickets \
WHERE current_approver_id = ? AND status = &#39;InReview&#39; \
ORDER BY priority_score DESC;

      * **Why This Works**

* **Prevents starvation:** Old low-urgency items eventually bubble up

* **Respects business rules:** Urgent items surface first

* **Tunable:** Adjust weights (0.6, 0.4) per organization

* **Simple to understand:** Non-technical stakeholders can see the logic

 \


   * **7. Non-Functional Requirements**

      * **Performance &amp; Scalability**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

**API Response Time**

</td>
   <td>

&lt;200ms (p95)

</td>
   <td>

State transitions, reads

</td>
  </tr>
  <tr>
   <td>

**Throughput**

</td>
   <td>

1,000 tickets/min

</td>
   <td>

Concurrent raises + approvals

</td>
  </tr>
  <tr>
   <td>

**Audit Log Latency**

</td>
   <td>

&lt;100ms

</td>
   <td>

Write to activity log

</td>
  </tr>
  <tr>
   <td>

**Storage**

</td>
   <td>

10 GB/year

</td>
   <td>

For 1M tickets + 10M audit entries

</td>
  </tr>
</table>

      * **Security &amp; Compliance**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

**Role-Based Access Control (RBAC)**

</td>
   <td>

Only approvers can approve tickets; only admins can delete rules

</td>
  </tr>
  <tr>
   <td>

**Audit Trail Immutability**

</td>
   <td>

Activity log uses append-only pattern; no updates after creation

</td>
  </tr>
  <tr>
   <td>

**Encryption at Rest**

</td>
   <td>

AES-256 for sensitive fields (comments, requester data)

</td>
  </tr>
  <tr>
   <td>

**Encryption in Transit**

</td>
   <td>

TLS 1.3 for all API calls

</td>
  </tr>
  <tr>
   <td>

**Data Retention Policy**

</td>
   <td>

Soft-deleted tickets purged after 7 years (configurable)

</td>
  </tr>
</table>

      * **Reliability &amp; Availability**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

**Uptime SLA**

</td>
   <td>

99.9% (allow 9 hours downtime/year)

</td>
  </tr>
  <tr>
   <td>

**Recovery Time Objective (RTO)**

</td>
   <td>

&lt;15 minutes

</td>
  </tr>
  <tr>
   <td>

**Recovery Point Objective (RPO)**

</td>
   <td>

&lt;1 minute

</td>
  </tr>
  <tr>
   <td>

**Backup Frequency**

</td>
   <td>

Hourly (Activity Log); daily (Tickets)

</td>
  </tr>
</table>

 \


   * **8. API Overview (Sample Endpoints)**

      * **Core Endpoints**

POST   /api/v1/tickets \
       → Raise a new ticket \
        \
GET    /api/v1/tickets/{ticket_id} \
       → Fetch ticket details + activity log \
        \
PATCH  /api/v1/tickets/{ticket_id}/state \
       → Transition state (e.g., approve, reject) \
        \
GET    /api/v1/users/{user_id}/inbox?sort=priority \
       → Fetch pending tickets for user (sorted by priority) \
        \
GET    /api/v1/workflows/{workflow_id}/bottlenecks \
       → Analyze: Which approval step is slowest? \
        \
DELETE /api/v1/tickets/{ticket_id} \
       → Soft-delete (archive) a ticket

   * **9. Success Metrics &amp; KPIs**

      * **Business Impact**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

**Average Approval Time**

</td>
   <td>

4.2 days

</td>
   <td>

1.5 days

</td>
   <td>

6 months

</td>
  </tr>
  <tr>
   <td>

**Approval SLA Compliance**

</td>
   <td>

68%

</td>
   <td>

&gt;95%

</td>
   <td>

3 months

</td>
  </tr>
  <tr>
   <td>

**User Adoption**

</td>
   <td>

—

</td>
   <td>

&gt;80% active users

</td>
   <td>

2 months

</td>
  </tr>
  <tr>
   <td>

**Audit Readiness**

</td>
   <td>

Manual, 40 hours/query

</td>
   <td>

&lt;5 minutes/query

</td>
   <td>

1 month

</td>
  </tr>
</table>

      * **Technical Health**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

**API p95 Latency**

</td>
   <td>

&lt;200ms

</td>
   <td>

CloudWatch, Prometheus

</td>
  </tr>
  <tr>
   <td>

**Activity Log Write Latency**

</td>
   <td>

&lt;100ms

</td>
   <td>

Database metrics

</td>
  </tr>
  <tr>
   <td>

**Uptime**

</td>
   <td>

99.9%

</td>
   <td>

Synthetic monitoring

</td>
  </tr>
  <tr>
   <td>

**Storage Growth**

</td>
   <td>

&lt;1 GB/month

</td>
   <td>

Disk usage alerts

</td>
  </tr>
</table>

   * **10. Roadmap**

      * **Phase 1 (MVP) - Weeks 1**

* Core API (Create, Read, Update, Delete)

* State machine engine

* Activity log (audit trail)

* RBAC (basic roles: Requester, Approver, Admin)

* Approval workflow (sequential only)

### **Phase 2 - Weeks 2**

* Priority algorithm + intelligent inbox

* Parallel approval workflows

* SLA monitoring &amp; escalation

* Advanced audit reporting

### **Phase 3 - Weeks 3**

* Workflow designer UI (low-code state machine builder)

* Webhook integrations (notify 3rd-party systems on completion)

* Bulk actions (batch approve/reject)

* Mobile app support

   * **11. Risks &amp; Mitigation**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

**State Machine Logic Bugs**

</td>
   <td>

Tickets stuck in invalid state

</td>
   <td>

Comprehensive unit + integration tests; state validation before transitions

</td>
  </tr>
  <tr>
   <td>

**Audit Log Growth**

</td>
   <td>

Storage/query performance degradation

</td>
   <td>

Partitioning by date; archival strategy after 7 years

</td>
  </tr>
  <tr>
   <td>

**Approver Overload**

</td>
   <td>

Priority algorithm not tuned; low-urgency items deprioritized forever

</td>
   <td>

Quarterly reviews of weights (0.6, 0.4); watchdog alerts if &gt;30 days pending

</td>
  </tr>
  <tr>
   <td>

**Integration Complexity**

</td>
   <td>

Third-party systems fail to call API correctly

</td>
   <td>

Comprehensive API documentation; SDK examples (Python, JavaScript, Java)

</td>
  </tr>
</table>

   * **Glossary**

<table>
  <tr>
   <td>

undefined

</td>
   <td>

undefined

</td>
  </tr>
  <tr>
   <td>

**Ticket**

</td>
   <td>

Atomic unit of work (e.g., a leave request, memo, purchase order)

</td>
  </tr>
  <tr>
   <td>

**State**

</td>
   <td>

Current lifecycle stage of a ticket (Raised, In Review, Approved, etc.)

</td>
  </tr>
  <tr>
   <td>

**Transition**

</td>
   <td>

Movement from one state to another, triggered by an action

</td>
  </tr>
  <tr>
   <td>

**Approver**

</td>
   <td>

User with permission to approve or reject a ticket

</td>
  </tr>
  <tr>
   <td>

**Activity Log**

</td>
   <td>

Immutable, append-only log of all ticket events

</td>
  </tr>
  <tr>
   <td>

**SLA**

</td>
   <td>

Service Level Agreement; time threshold for approvals

</td>
  </tr>
  <tr>
   <td>

**Soft Delete**

</td>
   <td>

Mark as deleted without removing from database (preserves audit trail)

</td>
  </tr>
  <tr>
   <td>

**RBAC**

</td>
   <td>

Role-Based Access Control; permission model tied to user roles

</td>
  </tr>
</table>

   * **Contact &amp; Questions**

**For Employers / Hiring Teams:** \
This PRD demonstrates:

* **Business Analysis:** User personas, clear problem statement, success metrics

* **Systems Design:** State machines, database modeling, API architecture

* **Technical Writing:** Scannable structure, use of diagrams, compliance focus

**For External Developers:**

All API endpoints, database schemas, and business logic are documented. Start with the API Overview and Entity-Relationship Diagram.

**Document Version:** 1.0 \
**Last Updated:** June 2026 \
**Author:** Enterprise Systems Architect \
**Status:** Ready for Implementation Review

