###  **IAM — Identity & Access Management**

**What it is:** IAM is the global service that controls WHO can do WHAT to WHICH AWS resources. It is free, global (not region-specific), and foundational to every AWS architecture.

####  **Core IAM Components**

| Component | What it is | Real-World Analogy | Key Exam Fact |
| ------ | ------ | ------ | ------ |
| User | A permanent identity for a person or application | An employee badge | Has long-term credentials (password + access keys). AWS strongly discourages long-term access keys for human users, favoring temporary credentials via AWS IAM Identity Center. |
| Group | A collection of users sharing the same permissions | A department (HR, Dev, Finance) | Groups cannot be nested; policies attach to groups, not directly to users (best practice). |
| Role | A temporary identity assumed by services, users, or external accounts | A visitor badge you wear and return | No long-term credentials; uses STS tokens (15 min–12 hrs for AssumeRole; up to 36 hrs for GetFederationToken). |
| Policy | JSON document defining Allow/Deny for actions on resources | A rulebook/terms of service | Explicit Deny always wins over any Allow. |
| STS | Security Token Service — issues temporary credentials | A temp-pass vending machine | AssumeRole (15 min-12 hrs), AssumeRoleWithWebIdentity, GetFederationToken (15 min-36 hrs). |

####  **IAM Policy Evaluation Logic**

AWS evaluates policies in a strict priority order. Knowing this order is critical for the exam.

| Priority | Evaluation Step | Result |
| ------ | ------ | ------ |
| 1 (highest) | Explicit Deny in ANY policy | DENY — game over, no exceptions. |
| 2 | Organizations SCP (Service Control Policy) | If SCP doesn't Allow → DENY. Does not apply to Organization's management account. |
| 3 | Resource-based policy (e.g. S3 bucket policy) | May grant cross-account access. In same-account requests, can allow access without identity-based policy allow. |
| 4 | Permission Boundary | Must also Allow what identity policy allows. Restricts effective permissions (intersection). |
| 5 | Session Policy (when assuming roles) | Further restricts temporary session permissions during role assumption. |
| 6 (lowest) | Identity-based policy (IAM policy on user/role) | Explicit Allow needed if not already granted by resource-based policy. |

| ⚠️ EXAM TRAP: No explicit Allow = implicit DENY. A user with no policies attached cannot do anything at all. This trips up many candidates who assume a blank slate allows read-only access. |
| ------ |

####  **IAM Roles — The Most Important IAM Concept for SAA**

Roles are used constantly in SAA exam scenarios. Learn to recognize WHEN to use a role vs other options.

| Scenario | Solution | Why Not Alternatives |
| ------ | ------ | ------ |
| EC2 instance needs to read S3 | Attach IAM Role to EC2 instance profile | Never store access keys on EC2 — they can be stolen from metadata. |
| Lambda needs to write to DynamoDB | Execution Role on Lambda function | Same reasoning — roles provide temp credentials automatically. |
| Account A needs to access Account B's S3 | Role in Account B; Account A users AssumeRole | Cross-account access = Roles, not copying credentials. |
| Mobile app users (millions) need AWS access | Cognito Identity Pool + Web Identity Federation | Cannot create IAM user per customer — doesn't scale. |
| On-prem employees need AWS access via AD | AWS IAM Identity Center with AD connector | Federation with SAML 2.0 or OIDC — no AWS user creation needed. |
| Third-party audit needs read-only access | Role with External ID (Confused Deputy protection) | External ID prevents one tenant impersonating another. |
| On-premises servers need to access AWS resources | IAM Roles Anywhere with X.509 certificates | Avoids storing long-term IAM access keys on-premise; uses PKI trust to issue temporary STS credentials. |

####  **Attribute-Based Access Control (ABAC) vs Role-Based Access Control (RBAC)**

| Feature | ABAC (Attribute-Based) | RBAC (Role-Based) |
| ------ | ------ | ------ |
| **Definition** | Permissions based on attributes (tags) on users and resources. | Permissions based on roles/job functions (policies attached directly). |
| **Scalability** | High. No need to update policies when adding new resources or users. | Low. Requires updating or creating new roles and policies for each change. |
| **How it works** | Subject tag matches resource tag (e.g., User:Project=Blue must match Resource:Project=Blue). | User joins HR group, automatically gets HR IAM Policy permissions. |
| **Use Case** | Dynamic environments with rapidly growing resources and teams. | Static environments with well-defined, slow-changing structures. |

####  **IAM Policies: Inline vs Managed**

| Type | Description | When to Use | Exam Tip |
| ------ | ------ | ------ | ------ |
| AWS Managed Policy | Pre-built by AWS (e.g. AmazonS3ReadOnlyAccess) | Starting point, common use cases | Cannot modify; may be overly permissive. |
| Customer Managed Policy | You create and maintain; reusable across entities | Production environments, least privilege | Version-controlled; best practice for shared policies. |
| Inline Policy | Embedded directly in one user/role/group; deleted with entity | Strict 1:1 relationship needed | Hard to audit; generally discouraged. |

####  **Permission Boundaries**

**What it does:** Sets the MAXIMUM permissions an IAM entity can have. The effective permissions are the intersection of the identity policy and the permission boundary.

**Use case:** You want to allow developers to create IAM roles/users for their services, but ensure they cannot grant more permissions than they themselves have (prevents privilege escalation).

| 💡 TIP: Think of Permission Boundary as a fence. The identity policy says what they CAN do. The boundary says what the fence allows. Only actions inside BOTH the fence AND the policy are permitted. |
| ------ |

####  **MFA & Security Best Practices**

| Practice | Details |
| ------ | ------ |
| Root Account | Enable MFA immediately; never use for day-to-day tasks; delete access keys. AWS now mandates MFA for root accounts. |
| MFA Delete on S3 | Requires MFA to delete S3 objects or change bucket versioning — needs root account to enable. |
| Workforce Identity | Use AWS IAM Identity Center for human users. Avoid creating IAM users. Integrate with on-premises AD or external Okta/Azure AD. |
| Least Privilege | Start with minimal permissions and add as needed; use IAM Access Advisor to see when services were last accessed and prune unused ones. |
| IAM Access Analyzer | Identifies resources shared outside your org/account. Now includes Unused Access Analyzer (finds unused keys/roles) and custom policy checks. |

| 🧠 MNEMONIC: GRUELS for IAM entities: Groups, Roles, Users, External IDs, Limits (boundaries), STS. Everything in IAM connects to these six. |
| ------ |