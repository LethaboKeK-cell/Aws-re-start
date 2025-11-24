

## Prerequisites

- **Access level:** Administrator access to IAM.
- **Scope:** One AWS account with pre-created IAM users and groups (e.g., S3-Support, EC2-Support).
- **Goal:** Enforce stronger passwords, verify group policies, and assign users to groups that match their job functions.

---

## Step 1: Create and apply a secure IAM password policy

Strengthen account-wide password requirements to reduce common attack vectors.

1. **Open IAM console**
   - **Path:** IAM > Account settings > Password policy.

2. **Select policy mode**
   - **Choice:** Custom (not IAM default).

3. **Set minimum length**
   - **Value:** 10 characters.

4. **Enable complexity rules**
   - **Uppercase:** Require A–Z.
   - **Lowercase:** Require a–z.
   - **Number:** Require 0–9.
   - **Symbol:** Require at least one of ! @ # $ % ^ & * ( ) _ + - = [ ] { } |.

5. **Configure rotation and reuse**
   - **Expiration:** 90 days.
   - **Reuse prevention:** Remember last 5 passwords.

6. **Allow self-service changes**
   - **Setting:** Allow users to change their own password.

7. **Save and confirm**
   - **Check:** Policy shows as active with the selected controls.

---

## Step 2: Explore pre-created IAM users and user groups

Get familiar with existing identities before applying changes.

1. **List users**
   - **Path:** IAM > Users.
   - **Review:** User name, creation time, and last activity.

2. **List groups**
   - **Path:** IAM > User groups.
   - **Review:** Group names (e.g., S3-Support, EC2-Support) and attached policies.

3. **Record context**
   - **Mapping:** Note which users should belong to which groups based on role (e.g., S3 read-only vs EC2 support).

---

## Step 3: Inspect IAM policies attached to user groups

Verify each group’s effective permissions to ensure least privilege.

#### EC2 admin support policy (targeted control)

- **Purpose:** Allow instance viewing and start/stop actions.
- **Scope:** All EC2 resources (Describe*, StartInstances, StopInstances).

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ec2:Describe*",
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": ["*"],
      "Effect": "Allow"
    }
  ]
}
```

#### Amazon EC2 read only access

- **Purpose:** View EC2, ELB, and select CloudWatch dimensions without modification.
- **Scope:** Read-only describe/list actions across services.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:Describe*",
        "ec2:GetSecurityGroupsForVpc"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "elasticloadbalancing:Describe*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:ListMetrics",
        "cloudwatch:GetMetricStatistics",
        "cloudwatch:DescribeAlarms"
      ],
      "Resource": "*"
    }
  ]
}
```

#### Amazon S3 read only access

- **Purpose:** Read and list S3 objects and buckets (including Object Lambda).
- **Scope:** No write, delete, or ACL changes permitted.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:Get*",
        "s3:List*",
        "s3:Describe*",
        "s3-object-lambda:Get*",
        "s3-object-lambda:List*"
      ],
      "Resource": "*"
    }
  ]
}
```

> Tip: Ensure groups attach only what’s needed. Combine read-only with narrowly scoped admin actions when operationally required.

---

## Step 4: Add users to user groups with the right capabilities

Assign users based on role, aligning support tasks with least-privilege access.

1. **Open the target group**
   - **Path:** IAM > User groups > Select group (e.g., S3-Support, EC2-Support).

2. **Add users**
   - **Action:** Add users > Search/filter > Check the intended users > Add.

3. **Confirm membership**
   - **Check:** Group details now list the selected users.

4. **Examples**
   - **S3-Support:** Attach AmazonS3ReadOnlyAccess; add users who need to view buckets/objects.
   - **EC2-Support:** Attach AmazonEC2ReadOnlyAccess for monitoring, or EC2-Admin-Policy for controlled start/stop duties; add corresponding users.

5. **Document assignments**
   - **Record:** Keep a log of who was added, by whom, when, and why.

---

## Validation checklist

- **Password policy:** Minimum length 10, complexity on, 90-day expiry, prevent last 5 reuses, self-change enabled.
- **Group policies:** Read-only policies attached to S3/EC2 support groups; admin actions limited to intended EC2 operations.
- **User membership:** Each user is in the correct group; no unnecessary overlapping privileges.
- **Access testing:** Users can sign in, list resources they should see, and perform only the intended actions.
  
---

## Operational tips 

- **Least privilege first:** Start with read-only, then add narrowly scoped actions as needed.
- **Use tags and conditions:** Restrict EC2 admin actions to tagged resources with IAM policy conditions.
- **Review cadence:** Quarterly review of users, groups, and policies; rotate temporary elevated access.
- **Onboarding/offboarding:** Automate group assignment and deprovisioning with a checklist to avoid drift.
  
