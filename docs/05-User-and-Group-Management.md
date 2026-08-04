# User and Group Management

## Purpose

PowerShell provides administrators with the ability to manage and automate Active Directory identity operations. This section focuses on user account auditing, identity reporting, bulk user management, security group administration, and repeatable identity management workflows.

## Environment

| Component | Configuration |
| --- | --- |
| Operating System | Windows Server 2025 |
| Domain | `harper.local` |
| Role | Domain Controller |
| PowerShell Module | ActiveDirectory |

---

# User Account Auditing

## Purpose

Account auditing is an important administrative task used to identify account issues such as disabled users, expired accounts, and password expiration states. PowerShell allows administrators to quickly review account conditions within Active Directory.

---

## Finding Disabled User Accounts

The following command searches Active Directory for user accounts that are disabled.

```powershell
Search-ADAccount -UsersOnly -AccountDisabled
```

### Result

The command returns user accounts that are currently disabled. This allows administrators to identify inactive accounts and review account lifecycle status.

---

## Finding Expired User Accounts

The following command searches Active Directory for accounts that have expired.

```powershell
Search-ADAccount -UsersOnly -AccountExpired
```

### Result

The command displays user accounts that have passed their configured expiration date. This can help administrators identify accounts requiring review or cleanup.

---

## Finding Users With Expired Passwords

The following command searches for accounts with expired passwords.

```powershell
Search-ADAccount -UsersOnly -PasswordExpired
```

### Result

The command identifies accounts requiring password updates and supports identity security reviews.

---

# Identity Reporting

## Purpose

Administrators often need reports containing user account information for audits, documentation, and security reviews. PowerShell can collect Active Directory information and export the results into CSV files.

---

## Creating a User Audit Report

Create a folder to store administrative reports.

```powershell
New-Item -Path C:\Reports -ItemType Directory
```

Export Active Directory user information:

```powershell
Get-ADUser -Filter * -Properties Enabled, LastLogonDate |
Select-Object Name, SamAccountName, Enabled, LastLogonDate |
Export-Csv C:\Reports\UserAudit.csv -NoTypeInformation
```

Verify the report was created:

```powershell
Test-Path C:\Reports\UserAudit.csv
```

### Result

The user audit report is created and saved as:

```
C:\Reports\UserAudit.csv
```

The report contains account information that can be reviewed for administrative and security purposes.

---

# Bulk User Management

## Purpose

In larger environments, administrators need to create and manage multiple accounts efficiently. PowerShell can automate account creation by importing user information from CSV files.

---

## Importing User Data From CSV

Example CSV structure:

```csv
FirstName,LastName,Username,Department
Michael,Jordan,m.jordan,IT
Emily,Davis,e.davis,HR
```

Review the CSV information:

```powershell
Import-Csv C:\Reports\NewUsers.csv
```

### Result

The command displays the user information that will be processed during account creation.

---

## Creating Users From CSV

The following script imports user information and creates Active Directory accounts automatically.

```powershell
Import-Csv C:\Reports\NewUsers.csv | ForEach-Object {
    New-ADUser `
    -Name "$($_.FirstName) $($_.LastName)" `
    -GivenName $_.FirstName `
    -Surname $_.LastName `
    -SamAccountName $_.Username `
    -UserPrincipalName "$($_.Username)@harper.local" `
    -Department $_.Department `
    -Enabled $true `
    -AccountPassword (ConvertTo-SecureString "P@ssw0rd123" -AsPlainText -Force)
}
```

### Result

The script creates Active Directory user accounts using the information provided in the CSV file.

This demonstrates how PowerShell can automate repetitive identity administration tasks.

---

## Verifying Created Users

Verify that the imported users were created successfully.

```powershell
Get-ADUser -Filter "SamAccountName -eq 'm.jordan' -or SamAccountName -eq 'e.davis'" |
Select-Object Name, SamAccountName, Enabled, Department
```

### Result

The command confirms that the new user accounts exist and displays their account information.

---

# Group Management and Access Review

## Purpose

Active Directory security groups allow administrators to assign permissions and manage access based on user roles. PowerShell can be used to review groups, manage membership, and validate access assignments.

---

## Reviewing Security Groups

Display available Active Directory groups:

```powershell
Get-ADGroup -Filter *
```

### Result

The command displays security groups available within the domain.

---

## Reviewing Group Membership

View members assigned to a security group:

```powershell
Get-ADGroupMember -Identity "IT"
```

### Result

The command displays users and objects assigned to the selected security group.

---

## Assigning Users to Security Groups

Add users to the appropriate security groups:

```powershell
Add-ADGroupMember -Identity "IT" -Members "m.jordan"
```

```powershell
Add-ADGroupMember -Identity "HR" -Members "e.davis"
```

### Result

Users are added to their assigned security groups, supporting role-based access management.

---

## Verifying Group Assignments

Verify group membership:

```powershell
Get-ADGroupMember -Identity "IT"
```

```powershell
Get-ADGroupMember -Identity "HR"
```

### Result

The commands confirm that users have been assigned to the correct security groups.

---

# Identity Administration Workflow

## Purpose

Combining auditing, reporting, and access management tasks creates a repeatable identity administration workflow.

---

## Creating an Identity Review Report

Generate a report containing user identity information.

```powershell
Get-ADUser -Filter * -Properties Enabled, LastLogonDate, Department |
Select-Object Name, SamAccountName, Department, Enabled, LastLogonDate |
Export-Csv C:\Reports\IdentityReview.csv -NoTypeInformation
```

Verify the report:

```powershell
Test-Path C:\Reports\IdentityReview.csv
```

### Result

The workflow creates an identity review report containing user account information for administrative review.

---

## Reviewing Group Access

Review security group assignments:

```powershell
Get-ADGroupMember -Identity "IT" |
Select-Object Name, ObjectClass
```

### Result

The command displays group membership information and supports access review.

---

## Reviewing Account Status

Review account enablement status:

```powershell
Get-ADUser -Filter * -Properties Enabled |
Select-Object Name, SamAccountName, Enabled
```

### Result

The command displays the current enabled or disabled state of user accounts.

---

# Verification

The following tasks were completed:

- Audited Active Directory user accounts
- Reviewed disabled and expired account states
- Generated user identity reports
- Automated user creation using CSV data
- Managed security group membership
- Verified identity and access assignments
- Created repeatable PowerShell identity management workflows
