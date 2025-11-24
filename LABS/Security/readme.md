

## Prerequisites and context

- **AWS account and permissions:** You need access to Systems Manager, EC2, IAM, and AWS Organizations (if patching across accounts).
- **SSM agent and IAM role:** Ensure your EC2 instances run the SSM Agent and are attached to an instance profile with Systems Manager permissions (e.g., `RoleForSSM`). The instance should appear “Online” in Systems Manager with a “Running” node state.
- **Operating systems:** This guide shows Windows Server examples and notes for Linux. Your instance metadata may look similar to:
  - **Platform type:** Linux or Windows
  - **Resource type:** EC2 instance
  - **Ping status:** Online
  - **Image ID:** e.g., `ami-00c1d63aff2d420ad` (varies per region)
- **Region selection:** Work in the same region your instances run in.

> Tip: If you don’t see your instance under Fleet Manager or Patch Manager, verify the SSM agent is installed, the IAM role is correct, and that the instance has outbound internet/NAT to reach SSM endpoints.

---

## Tagging instances into patch groups

Patch Manager targets instances using the tag key “Patch Group”. You’ll use values like `WindowsProd` or `LinuxProd` to group environments.

1. **Open the EC2 console** and select the instance you want to patch.
2. **Manage tags:**
   - **Key:** Patch Group  
   - **Value:** WindowsProd (for Windows servers)  
   - Optional: Add `Name` and any lab identifiers (e.g., `cloudlab`).
3. **Save tags** and confirm they appear on the instance.

> Tip: Keep patch groups environment-specific (e.g., `WindowsProd`, `WindowsTest`, `LinuxProd`) so you can roll out patches safely.

---

## Creating a custom patch baseline (Windows example)

A patch baseline defines which patches are approved and when. You can use AWS-provided defaults or create one tailored to your compliance policy.

1. **Go to Systems Manager → Patch Manager → Patch baselines → Create patch baseline.**
2. **Baseline details:**
   - **Name:** WindowsServerSecurityUpdates  
   - **Description:** Windows security baseline patch  
   - **Operating system:** Windows  
   - **Available security updates compliance status:** Noncompliant (so unapproved security fixes still show up in reports)
3. **Operating system rule 1 (high priority security):**
   - **Products:** WindowsServer2019  
   - **Classification:** SecurityUpdates  
   - **Severity:** Critical  
   - **Auto-approval:** Approve patches after 3 days  
   - **Compliance reporting:** Critical
4. **Operating system rule 2 (broader security):**
   - **Products:** WindowsServer2019  
   - **Classification:** SecurityUpdates  
   - **Severity:** Important  
   - **Auto-approval:** Approve patches after 3 days  
   - **Compliance reporting:** High
5. **Create the patch baseline.**

---

## Associating patch groups with the baseline

You’ll link your baseline to specific patch groups so Patch Manager knows which instances it applies to.

1. **Open Patch baselines** and select your baseline `WindowsServerSecurityUpdates`.
2. **Actions → Modify patch groups.**
3. **Add patch group values:**  
   - **WindowsProd** (and any others you plan to patch under this baseline)
4. **Save changes** and verify your group appears under “Patch groups”.

> Tip: You can create up to 25 tag values per baseline. Keep naming consistent across accounts and regions.

---

## Setting the default patch baseline (optional but recommended)

You can set your custom baseline as the default for the OS to simplify operations.

1. **Open Patch baselines** and select `WindowsServerSecurityUpdates`.
2. **Actions → Set default patch baseline** for Windows.
3. **Confirm** the change. You’ll still retain the AWS defaults like `AWS-DefaultPatchBaseline` for reference.

> Note: If your org uses multiple baselines (e.g., different rules for Prod vs Test), you may prefer not to set a single default and instead target by patch group.

---

## Targeting instances by tag for patch operations

Before running a patch, confirm target selection is tag-based and points to your patch group.

1. **Systems Manager → Patch Manager → Patch now.**
2. **Target selection:** Choose “Specify instance tags”.
3. **Tag key/value:**  
   - **Tag key:** Patch Group  
   - **Tag value:** WindowsProd (or `LinuxProd` for Linux fleets)
4. **Add** and verify targets are recognized.

> Tip: Resource Groups are great for complex environments, but tags keep it simple.

---

## Running a patch: scan and install, with reboot control

Use “Patch now” for ad-hoc patching; later, you can automate with Maintenance Windows.

1. **Patch Manager → Patch now.**
2. **Patching operation:**
   - **Scan** (to see what’s missing), or  
   - **Scan and install** (to apply approved patches)
3. **Reboot option:**
   - **Reboot if needed** (safe default for Windows), or  
   - **Do not reboot** (use when coordinating with app owners), or  
   - **Schedule a reboot time** (for controlled maintenance)
4. **Instances to patch:** Choose “Patch only the target instances I specify” (safer than “All instances”).
5. **Click “Patch now”** to start the operation.

---

## Verifying compliance and outcomes

After the operation completes, validate that instances meet the baseline.

- **Compliance dashboard:** Check “Patch compliance” for counts like “Patch installed”, “Patch missing”, and overall status per instance.
- **Instance details:** Confirm association status is “Success” and ping is “Online”.
- **Reporting:** Unapproved security updates should show as “Noncompliant” if you selected that compliance status.

> Tip: If compliance is off, re-check tags, baseline associations, OS alignment, and SSM agent health. For Windows, see Windows Update logs.

---

## Troubleshooting checklist

- **Instance not targeted:** Verify `Patch Group` tag exactly matches the baseline’s patch groups.
- **Instance “Offline”:** Check SSM Agent, IAM role (`RoleForSSM`), VPC endpoints or NAT for SSM connectivity.
- **No patches installed:** Confirm your baseline approves relevant products/classifications for the OS version (e.g., WindowsServer2019).
- **Reboots not happening:** If set to “Do not reboot”, kernel or system-level updates may remain pending until manual or scheduled reboot.
- **Baseline not applied:** Make sure you modified patch groups on the specific baseline and, if desired, set it as default for the OS.

---

## Quick reference: what you configured

- **Tags:** Patch Group = WindowsProd (and LinuxProd for Linux)
- **Custom baseline:** WindowsServerSecurityUpdates (Critical + Important security updates, auto-approve after 3 days)
- **Patch groups linked:** WindowsProd on the baseline
- **Action flow:** Patch now → Specify instance tags → Scan or Scan and install → Reboot options → Patch only target instances → Patch now
- **Verification:** Compliance dashboard shows installed/missing/noncompliant counts per instance

