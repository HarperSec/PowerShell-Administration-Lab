
# Identity Automation Scripts

## Purpose

PowerShell automation allows administrators to create repeatable workflows for managing Active Directory identities. Instead of manually creating users and performing repetitive administrative tasks, scripts can process input data, apply changes, and record results.

This section demonstrates how PowerShell can be used to automate Active Directory user creation through CSV input, implement error handling, handle duplicate accounts, and generate administrative logs.

## Environment

| Component | Configuration |
| --- | --- |
| Operating System | Windows Server 2025 |
| Domain | `harper.local` |
| Role | Domain Controller |
| PowerShell Module | ActiveDirectory |

---

# Creating the Automation Workspace

## Purpose

Before creating automation scripts, administrators should organize script files in a dedicated location. A centralized script directory makes automation tools easier to manage and maintain.

---

## Creating the Scripts Directory

The following command creates a dedicated folder for PowerShell automation scripts.

```powershell
New-Item -Path C:\Scripts -ItemType Directory
```

### Result

A scripts directory is created:

```
C:\Scripts
```

This directory will store PowerShell automation scripts used for Active Directory administration.

---

# Creating the Automation Script

## Purpose

The automation script will process user information from a CSV file and create Active Directory accounts automatically.

The script created:

```
C:\Scripts\New-ADUserAutomation.ps1
```

The script performs the following tasks:

- Imports user data from CSV
- Creates Active Directory users
- Checks for existing accounts
- Handles errors
- Records automation results in a log file

---

# Building the User Automation Script

## Importing User Data From CSV

The script begins by importing user information from a CSV file.

```powershell
$Users = Import-Csv "C:\Reports\NewUsers.csv"
```

### Result

The CSV file is loaded into the `$Users` variable. Each row represents a user account that will be processed by the automation script.

Example CSV format:

```csv
FirstName,LastName,Username,Department
Tatum,Bore,t.bore,IT
Lillith,Spellmen,l.spellmen,HR
```

---

## Creating a Log File

The script creates a location for recording automation results.

```powershell
$LogFile = "C:\Reports\UserCreationLog.txt"
```

### Result

The script stores success, skipped, and failed operations in:

```
C:\Reports\UserCreationLog.txt
```

---

## Creating Users From CSV Data

The script processes each CSV entry using a `foreach` loop.

```powershell
foreach ($User in $Users) {

    New-ADUser `
    -Name "$($User.FirstName) $($User.LastName)" `
    -GivenName $User.FirstName `
    -Surname $User.LastName `
    -SamAccountName $User.Username `
    -UserPrincipalName "$($User.Username)@harper.local" `
    -Department $User.Department `
    -Enabled $true `
    -AccountPassword (ConvertTo-SecureString "HarperLab!2026" -AsPlainText -Force)

}
```

### Result

The script automatically creates Active Directory users using the information provided in the CSV file.

This reduces manual account creation and provides a repeatable administrative workflow.

---

# Adding Error Handling

## Purpose

Administrative scripts should handle failures gracefully. Error handling allows administrators to identify issues without stopping the entire automation process.

The script uses `try/catch` blocks:

```powershell
try {

    # User creation actions

}

catch {

    Add-Content $LogFile "ERROR: $($User.Username) - $($_.Exception.Message)"

}
```

### Result

If an account creation fails, the script records the error in the log file.

Examples of possible failures:

- Username already exists
- Password policy requirements not met
- Invalid user information
- Permission issues

---

# Adding Duplicate User Detection

## Purpose

Before creating accounts, automation scripts should verify whether the user already exists. This prevents duplicate accounts and unnecessary errors.

The script checks for existing accounts:

```powershell
if (Get-ADUser -Filter "SamAccountName -eq '$($User.Username)'") {

    Add-Content $LogFile "SKIPPED: User already exists $($User.Username)"

}

else {

    New-ADUser `
    -Name "$($User.FirstName) $($User.LastName)" `
    -GivenName $User.FirstName `
    -Surname $User.LastName `
    -SamAccountName $User.Username `
    -UserPrincipalName "$($User.Username)@harper.local" `
    -Department $User.Department `
    -Enabled $true `
    -AccountPassword (ConvertTo-SecureString "HarperLab!2026" -AsPlainText -Force)

    Add-Content $LogFile "SUCCESS: Created user $($User.Username)"

}
```

### Result

The automation workflow can now:

- Create new accounts
- Skip existing accounts
- Record actions taken

---

# Running the Automation Script

## Executing the Script

The completed automation script is executed using:

```powershell
C:\Scripts\New-ADUserAutomation.ps1
```

### Result

The script processes the CSV file and performs the configured identity management tasks.

---

# Verifying Automated User Creation

After running the script, newly created accounts can be verified.

```powershell
Get-ADUser -Identity t.bore -Properties Department |
Select-Object Name, SamAccountName, Enabled, Department
```

### Result

The command confirms that the automated user account was created successfully.

Example:

```
Name      SamAccountName   Enabled   Department
----      --------------   -------   ----------
T Bore    t.bore           True      IT
```

---

# Reviewing Automation Logs

The script records execution results in the log file.

Review the log:

```powershell
Get-Content C:\Reports\UserCreationLog.txt
```

### Result

The log displays automation results.

Example:

```
SUCCESS: Created user t.bore
SKIPPED: User already exists l.spellmen
```

The log provides administrators with a record of completed actions and troubleshooting information.

---

# Verification

The following tasks were completed:

- Created a PowerShell automation workspace
- Built an Active Directory user creation script
- Imported user data from CSV files
- Automated Active Directory account creation
- Added error handling
- Added duplicate account detection
- Created administrative logging
- Verified automation results

This demonstrates how PowerShell can be used to automate identity administration tasks in an Active Directory environment.
