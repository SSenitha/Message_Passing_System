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