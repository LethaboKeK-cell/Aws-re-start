# Linux user and group setup guide for EC2

This guide walks you through creating new users with a default password, creating groups, and assigning users to the right groups on a Linux EC2 instance. It includes clear, reproducible steps with verification commands and troubleshooting tips.

---

## Prerequisites and context

- **Environment:** Amazon Linux or similar Linux distro on EC2.
- **Access:** SSH as a sudo-capable user (e.g., ec2-user).
- **Goal:** Create users, set default passwords, create groups, and assign memberships safely.

> Tip: Run each command exactly as shown; spacing matters. For example, use `cat /etc/group` (with a space), not `cat/etc/group`.

---

## Create users with a default password

#### Create the users
- **Command:**
  ```
  sudo useradd arosalez
  sudo useradd eowusu
  sudo useradd jdoe
  sudo useradd lijuan
  ```
- **Verify users exist:**
  ```
  cut -d: -f1 /etc/passwd | egrep 'arosalez|eowusu|jdoe|lijuan'
  ```

#### Set default passwords
- **Command (interactive):**
  ```
  sudo passwd arosalez
  sudo passwd eowusu
  sudo passwd jdoe
  sudo passwd lijuan
  ```
- **Notes:**
  - **Password length:** Use 8+ characters to avoid the “BAD PASSWORD: shorter than 8 characters” warning.
  - **Typing carefully:** If you see “Sorry, passwords do not match,” re-run `passwd` for that user.

#### Optional: Force password change at first login
- **Command:**
  ```
  sudo chage -d 0 arosalez
  sudo chage -d 0 eowusu
  sudo chage -d 0 jdoe
  sudo chage -d 0 lijuan
  ```
- **Verify:**
  ```
  sudo chage -l arosalez | head -n 5
  ```

---

## Create groups

#### Add department groups
- **Command:**
  ```
  sudo groupadd Sales
  sudo groupadd Shipping
  sudo groupadd HR
  sudo groupadd Finance
  sudo groupadd Personnel
  sudo groupadd CEO
  sudo groupadd Managers
  ```
- **Verify groups created:**
  ```
  getent group | egrep 'Sales|Shipping|HR|Finance|Personnel|CEO|Managers'
  ```
- **Example of /etc/group entries (expected output pattern):**
  ```
  Sales:x:1005:
  Shipping:x:1006:
  HR:x:1007:
  Finance:x:1008:
  Personnel:x:1009:
  CEO:x:1010:
  Managers:x:1011:
  ```

---

## Assign users to appropriate groups

#### Append users to groups (preserves existing memberships)
- **Command:**
  ```
  sudo usermod -a -G HR arosalez
  sudo usermod -a -G Shipping eowusu
  sudo usermod -a -G Finance lijuan
  ```
- **Verify membership immediately:**
  ```
  getent group HR
  getent group Shipping
  getent group Finance
  ```
  - Expected pattern:
    ```
    HR:x:1007:arosalez
    Shipping:x:1006:eowusu
    Finance:x:1008:lijuan
    ```

#### Confirm user group sets
- **Command:**
  ```
  id arosalez
  id eowusu
  id jdoe
  id lijuan
  ```
- **What to look for:** The `groups=` list includes the department group(s) you added.

> Tip: Use `-a -G` together for `usermod`. Omitting `-a` will replace group membership instead of appending.

---

## Quick verification checklist

- **List all users:**
  ```
  cut -d: -f1 /etc/passwd
  ```
- **List all groups:**
  ```
  cat /etc/group
  ```
- **Check one user’s login shell and home directory:**
  ```
  getent passwd arosalez
  ```
- **Test a password change flow (if needed):**
  ```
  sudo passwd arosalez
  ```

---

## Troubleshooting

- **Command not found or file not found after cat:**
  - **Cause:** Missing space (e.g., `cat/etc/group`).
  - **Fix:** Use `cat /etc/group` with a space after `cat`.

- **Weak password warning:**
  - **Cause:** Password shorter than 8 characters or fails policy.
  - **Fix:** Choose a longer, more complex password. Consider enabling first-login reset with `chage -d 0`.

- **Membership not showing after usermod:**
  - **Cause:** Using `usermod -G` without `-a`.
  - **Fix:** Re-run with `usermod -a -G <group> <user>` and verify with `id <user>`.

- **Group not found:**
  - **Cause:** Group wasn’t created or typo in name.
  - **Fix:** `getent group <GroupName>`; if missing, run `sudo groupadd <GroupName>`.

