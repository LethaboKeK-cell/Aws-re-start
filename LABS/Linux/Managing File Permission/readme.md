

## Prerequisites

- **Access:** SSH into the instance as a sudo-capable user (e.g., ec2-user).
- **Base path:** Company directory at `/home/ec2-user/companyA`.
- **Users and groups:** Users like `mjackson`, `jlarson`, `ljuan`, `owusu`; groups like `Personnel`, `HR`, `Finance`, `Sales`, `Shipping`.

> Tip: Mind spaces in commands (e.g., `cat /etc/group`, not `cat/etc/group`). Use `-R` for recursive operations on trees.

---

## Set ownership to match department groups

#### Assign directory owners and primary groups
- **Company root owner and group:**
  ```
  sudo chown -R mjackson:Personnel /home/ec2-user/companyA
  ```
- **HR directory owner and group:**
  ```
  cd /home/ec2-user/companyA
  sudo chown -R jlarson:HR HR
  ```
- **Finance subdirectory owner and group:**
  ```
  sudo chown -R rmajor:Finance HR/Finance
  ```

#### Verify ownership
- **List recursively with owners and groups:**
  ```
  ls -laR /home/ec2-user/companyA
  ```
- **Spot-check directories:**
  - Expected patterns near the top-level:
    ```
    drwxr-xr-x 2 mjackson Personnel ...
    drwxr-xr-x 2 jlarson   HR        ...
    ```
  - Files inside HR and Finance:
    ```
    -rw-r--r-- 1 ljuan HR ...
    -rw-r--r-- 1 mjmajor Finance ...
    ```


#### Verify permissions
- **Check specific directories:**
  ```
  ls -l /home/ec2-user/companyA/HR
  ls -l /home/ec2-user/companyA/Finance
  ls -l /home/ec2-user/companyA/SharedFolders
  ```
- **Look for these patterns:**
  - Directories: `drwxrwxr-x owner group ...`
  - Files: `-rw-rw-r-- owner group ...`


#### Make a file executable by department members
- **Example: department script or placeholder:**
  ```
  sudo chmod 775 /home/ec2-user/companyA/Sales/Employees
  ```

---

## Update and normalize the company folder structure

#### Create and align department directories
- **Ensure top-level structure exists and owned by Personnel:**
  ```
  cd /home/ec2-user/companyA
  sudo mkdir -p CEO Documents Employees HR Finance Management Sales Shipping SharedFolders
  sudo chown mjackson:Personnel CEO Documents Employees Managers Sales Shipping SharedFolders
  sudo chmod 775 CEO Documents Employees Managers Sales Shipping SharedFolders
  ```
- **HR substructure owned by HR:**
  ```
  sudo mkdir -p HR/Employees HR/Layoffs HR/Management HR/NewHires
  sudo chown -R jlarson:HR HR
  sudo chmod -R 775 HR
  ```
- **Finance files owned by Finance with group write:**
  ```
  sudo touch Finance/{MonthlyAssessments.csv,Hourly.csv,FinancialStatements.csv,Salary.csv}
  sudo chown rmajor:Finance Finance/*
  sudo chmod 664 Finance/*
  ```

#### Populate and set ownership for example files
- **HR example files:**
  ```
  sudo touch HR/Employees/Employees.csv HR/Layoffs/Layoffs.csv HR/NewHires/{NewHires.csv,TrialPeriod.csv}
  sudo touch HR/Management/{Losses.csv,Gains.csv,Schedule.csv,Reductions.csv}
  sudo chown ljuan:HR HR/Employees/Employees.csv HR/Layoffs/Layoffs.csv HR/NewHires/NewHires.csv HR/NewHires/TrialPeriod.csv HR/Management/*
  sudo chmod 664 HR/Employees/Employees.csv HR/Layoffs/Layoffs.csv HR/NewHires/* HR/Management/*
  ```
- **Shared folders file:**
  ```
  sudo touch SharedFolders/logins.csv
  sudo chown mjackson:Personnel SharedFolders/logins.csv
  sudo chmod 664 SharedFolders/logins.csv
  ```

#### Verify the entire tree
- **Recursive list with owners and groups:**
  ```
  ls -laR /home/ec2-user/companyA
  ```
- **Expected patterns:**
  - Top-level owned by `mjackson:Personnel`
  - HR subtree owned by `jlarson:HR`; files by `ljuan:HR`
  - Finance files owned by `mjmajor:Finance` (or `rmajor:Finance` per your roster)
  - Sales owned by `mwolf:Sales`; Shipping by `owusu:Shipping` if applicable

---

## Quick checks and auditing

- **Check users and groups present:**
  ```
  cut -d: -f1 /etc/passwd
  getent group | egrep 'Personnel|HR|Finance|Sales|Shipping'
  ```
- **Find any world-writable files (should be none):**
  ```
  find /home/ec2-user/companyA -perm -0002 -type f -ls
  ```
- **Ensure directories have group-write where needed:**
  ```
  find /home/ec2-user/companyA -type d ! -perm -002 -printf '%p\n'
  ```

---

## Common pitfalls and fixes

- **Missing space in commands:**  
  **Fix:** Use `cat /etc/group` and `cat /etc/passwd` with a space after `cat`.

- **Group write not applied:**  
  **Fix:** Re-run the directory normalization:
  ```
  find /home/ec2-user/companyA -type d -exec sudo chmod 775 {} \;
  find /home/ec2-user/companyA -type f -exec sudo chmod 664 {} \;
  ```

- **Ownership inconsistent after moves or copies:**  
  **Fix:** Reset ownership by subtree:
  ```
  sudo chown -R <owner>:<group> /home/ec2-user/companyA/<Dept>
  ```

- **Accidentally removed execute on directories:**  
  **Fix:** Directories need execute to traverse:
  ```
  find /home/ec2-user/companyA -type d -exec sudo chmod u+x,g+x {} \;
  ```

