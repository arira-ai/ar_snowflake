### 1. Network Policies in Snowflake Inc.

Network Policies restrict **which IP addresses are allowed to authenticate to a Snowflake account or user**.
They function as a **network-level access control layer during login**, before database authorization or RBAC evaluation occurs.

Purpose:

* Prevent access from unauthorized networks
* Restrict access to corporate VPN or office networks
* Reduce attack surface from public internet login attempts
* Enforce enterprise network security boundaries

Network policies operate **only during authentication**.

If the source IP is not allowed, authentication fails even if credentials are correct.

---

# 2. Core Concepts

### Allowed IP List

List of IP addresses or CIDR ranges that are permitted to connect.

### Blocked IP List

List of addresses explicitly denied access even if included elsewhere.

### Policy Scope

Network policies can be applied to:

* Entire Snowflake account
* Individual users

User-level policy overrides account-level policy.

---

# 3. How Network Security Works During Login

Authentication flow:

```
Client → Snowflake Login Endpoint
        → Source IP checked against Network Policy
        → If allowed → Authentication continues
        → If blocked → Login rejected
        → If no policy → Login allowed
```

Security sequence:

1. Client sends login request
2. Snowflake detects client IP
3. Network policy evaluated
4. If allowed → identity verification continues
5. If denied → connection terminated

Network policies therefore operate **before RBAC, roles, or permissions are evaluated**.

---

# 4. Creating a Network Policy

Create a policy with allowed and blocked IP ranges.

Example:

```sql
CREATE NETWORK POLICY corp_network_policy
ALLOWED_IP_LIST = (
    '192.168.10.0/24',
    '203.0.113.5'
)
BLOCKED_IP_LIST = (
    '0.0.0.0/0'
);
```

Meaning:

Allowed

```
192.168.10.0 – 192.168.10.255
203.0.113.5
```

Blocked

```
All other networks
```

---

# 5. Applying a Network Policy to an Account

Restrict the entire Snowflake account.

```
ALTER ACCOUNT SET NETWORK_POLICY = corp_network_policy;
```

Effect:

All users must connect from approved IP addresses.

---

# 6. Applying a Network Policy to a User

User-specific restriction.

```
ALTER USER analyst_user
SET NETWORK_POLICY = corp_network_policy;
```

Example scenario:

| User             | Network Access      |
| ---------------- | ------------------- |
| Analyst          | Office network only |
| Admin            | VPN network         |
| External partner | Limited IP range    |

User policy overrides account policy.

---

# 7. Example Corporate Security Design

Company network layout:

```
Office Network: 10.10.0.0/16
VPN Network:    172.20.0.0/16
Blocked:        Public internet
```

Policy:

```sql
CREATE NETWORK POLICY company_access
ALLOWED_IP_LIST = (
  '10.10.0.0/16',
  '172.20.0.0/16'
);
```

Attach to account:

```
ALTER ACCOUNT SET NETWORK_POLICY = company_access;
```

Result:

Only office or VPN users can log in.

---

# 8. Blocking Specific Malicious IPs

Example:

```
CREATE NETWORK POLICY restricted_policy
ALLOWED_IP_LIST = ('10.0.0.0/8')
BLOCKED_IP_LIST = ('10.0.5.10');
```

Even though `10.0.5.10` belongs to allowed range, it is blocked.

Blocked list always takes priority.

---

# 9. Viewing Existing Network Policies

List policies:

```sql
SHOW NETWORK POLICIES;
```

Describe policy:

```sql
DESCRIBE NETWORK POLICY corp_network_policy;
```

---

# 10. Removing a Network Policy

Detach policy from account.

```
ALTER ACCOUNT UNSET NETWORK_POLICY;
```

Detach from user:

```
ALTER USER analyst_user UNSET NETWORK_POLICY;
```

---

# 11. Security Architecture Context

Snowflake security layers operate in this order:

```
1. Network Policy (IP validation)
2. Authentication (password, SSO, MFA)
3. Role Based Access Control
4. Object permissions
5. Data protection policies
```

Network policies therefore provide **perimeter security for the data platform**.

---

# 12. Best Practices

Use corporate VPN ranges only
Apply account-level policy first
Use user-specific policies for privileged users
Block known malicious IPs
Combine with MFA and SSO
Monitor login history for unauthorized attempts

---

# 13. Real Enterprise Use Case

Finance organization architecture:

```
Employees → Corporate VPN → Snowflake
Partners  → Fixed IP allowlist
Admins    → Separate restricted network policy
```

Policies enforce:

* Analysts cannot login from home networks
* Admins must connect through secure bastion network
* External partners limited to specific IP addresses

Result: reduced exposure of the Snowflake environment to internet-based attacks.
