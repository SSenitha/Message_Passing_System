## 5. Entity-Relationship Diagram (Mermaid)

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