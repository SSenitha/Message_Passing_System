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