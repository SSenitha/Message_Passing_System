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