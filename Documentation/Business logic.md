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