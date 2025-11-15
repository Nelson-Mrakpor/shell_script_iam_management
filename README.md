# This Project demonstrates the use of shell script in automating the IAM processes in AWS.

## Overview

**Primary Goal:**\
Automate common IAM tasks to create users, create an admin group and
attach a policy, then add those users to the group.

This script was developed to:

-   Accept a list of usernames from an array
-   Create each IAM user (if not present)
-   Ensure an **admin** group exists (create if missing)
-   Attach an access policy (AWS-managed `AdministratorAccess` used in
    the example)
-   Add users to the admin group
-   Fail safely and provide readable, user-friendly output

------------------------------------------------------------------------

## Non-Functional Requirements

-   **Idempotent:** re-running should not duplicate or break resources
-   **Clear progress messages:** clean, readable output
-   **Minimal dependencies:** Bash + AWS CLI
-   **Secure:** avoid leaking secrets; follow least-privilege principles

------------------------------------------------------------------------

## Prerequisites

Before executing the script, ensure the following are already in place:

1.  **AWS CLI v2 installed and configured** (default or named profile)
2.  **CLI credentials have the required IAM permissions**, including:
    -   `iam:CreateUser`
    -   `iam:CreateGroup`
    -   `iam:GetGroup`
    -   `iam:AttachGroupPolicy`
    -   `iam:AddUserToGroup`
    -   `iam:CreateLoginProfile`

------------------------------------------------------------------------

## High-Level Design Choices & Rationale

### 1. **Bash + AWS CLI**

-   Quick portability
-   No extra languages or tooling required
-   Easy debugging and easy to run inside lightweight environments
    (e.g., Linux VM)

### 2. **Array of Usernames (`IAM_USER_NAMES`)**

-   Simple and readable
-   Easy to modify directly or replace with input from a file

### 3. **Three Separate Functions**

-   `create_iam_users()`
-   `create_admin_group()`
-   `add_users_to_admin_group()`

**Rationale:** clear separation of concerns → easier testing, reading,
and extension.

### 4. **Idempotency Checks**

-   Group existence checked before creation
-   User creation errors interpreted → avoids duplication and allows
    safe re-runs

### 5. **Quiet Mode for AWS CLI Output**

    >/dev/null 2>&1

Used to keep only essential human-readable messages.

### 6. **Exit Codes and Simple Checks**

    if [ $? -eq 0 ]

Portable and reliable method to detect success/failure of commands.

------------------------------------------------------------------------

## Final Notes & Judgement Calls

### ✔ Simplicity Prioritized

Script remains easy for anyone with basic shell and AWS CLI knowledge to
understand and modify.

### ✔ Idempotency Considered

-   Safe re-runs
-   Group existence checked
-   Failed user creation handled gracefully

### ✔ Security Trade-offs

`AdministratorAccess` was used strictly for demonstration.
In production, create a **minimal custom policy** granting only the
required actions.

### ✔ Extensibility

The structure allows easy enhancements such as: - Importing users from
CSV
- Adding console password generation
- Applying IAM tags
- Email notifications
- Logging and audit features


![Script Execution](script_execute_success.png)


[View the IAM Management Script](iam_management_script.sh)
------------------------------------------------------------------------

## Conclusion

This script provides a clean, modular, and reliable foundation for
automating user and group management in AWS IAM. The design emphasizes
clarity, safety, and ease of future enhancement. While the example uses
elevated permissions for simplicity, production environments should
replace them with precise, least-privilege policies. With proper
prerequisites met, this script can serve as a dependable starting point
for CloudOps automation routines.

