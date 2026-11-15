
# Day 2: Temporary User Setup with Expiry

## 1. What is a Temporary Linux User?

A temporary Linux user is an account created for someone who needs access to a server for a limited period.

For example:

Imagine an organization hires a developer for a temporary assignment lasting three months.

The developer needs access to a Linux server during the assignment, but the account should expire when the assignment ends.

Instead of manually remembering to remove access, Linux allows us to configure an account expiration date.

### Why do we need account expiration?

- Prevent unnecessary long-term access.
- Improve server security.
- Manage temporary employees and contractors.
- Reduce unused accounts.
- Enforce access control policies.

**Remember:** Temporary accounts should not remain active indefinitely when access is no longer required.

---

## 2. Understanding Account Expiration vs Password Expiration

This is one of the most important concepts in this topic.

Linux provides different mechanisms for account expiration and password expiration.

### A. Account Expiration

Account expiration determines when a user account becomes expired.

Command:

```bash
sudo chage -E 2027-04-15 jim
```

This configures the account expiration date for user `jim`.

After the account expires, the user cannot authenticate normally.

### B. Password Expiration

Password expiration determines when a user's password expires and needs to be changed.

For example:

```bash
sudo chage -M 90 jim
```

This sets the maximum password age to 90 days.

The user will need to change their password according to the password aging rules.

### Difference Between Them

| Account Expiration | Password Expiration |
|---|---|
| Expires the user account | Expires the user's password |
| Controls account validity | Controls password validity |
| Uses `chage -E` | Uses `chage -M` |
| Account becomes expired | User may be required to change password |

**Memory trick:**

- `-E` = Expiration of account.
- `-M` = Maximum password age.

Do not confuse these two settings.

---

## 3. What is the `chage` Command?

`chage` stands for **Change Age**.

It is a Linux command used to manage password aging and account expiration information.

It allows administrators to:

- Set account expiration dates.
- Set maximum password age.
- Set minimum password age.
- Configure password warning periods.
- View account aging information.

### Important Syntax

```bash
chage [options] username
```

### Important Options

| Option | Purpose |
|---|---|
| `-E` | Sets account expiration date |
| `-M` | Sets maximum password age |
| `-m` | Sets minimum password age |
| `-W` | Sets password expiration warning period |
| `-l` | Displays account aging information |

### Examples

Set account expiration:

```bash
sudo chage -E 2027-04-15 jim
```

Set password maximum age to 90 days:

```bash
sudo chage -M 90 jim
```

Set password minimum age to 7 days:

```bash
sudo chage -m 7 jim
```

Set password warning period to 7 days:

```bash
sudo chage -W 7 jim
```

Display account aging information:

```bash
sudo chage -l jim
```

**Remember:** `chage` manages account expiration and password aging settings.

---

## 4. Understanding the `/etc/shadow` File

Linux stores password-related information and password aging details in:

```bash
/etc/shadow
```

This file contains information such as:

- Password hash.
- Last password change date.
- Minimum password age.
- Maximum password age.
- Password warning period.
- Account expiration date.

### Why is `/etc/shadow` important?

It separates sensitive password information from the publicly readable user account information in `/etc/passwd`.

Normally, access to `/etc/shadow` is restricted to privileged users.

### Important Difference

| File | Purpose |
|---|---|
| `/etc/passwd` | User account information |
| `/etc/shadow` | Password hashes and password aging information |

**Remember:** `/etc/passwd` identifies users; `/etc/shadow` stores sensitive password-related information.

---

## 5. Important Linux User Management Commands

| Command | Purpose |
|---|---|
| `useradd username` | Creates a user |
| `adduser username` | Creates a user |
| `id username` | Displays UID and group information |
| `getent passwd username` | Retrieves user account information |
| `passwd username` | Sets or changes a password |
| `usermod` | Modifies an existing user |
| `userdel username` | Deletes a user |
| `chage -l username` | Displays account aging information |
| `chage -E DATE username` | Sets account expiration date |

---

# 6. Practical Lab: Temporary User Setup with Expiry

## Task Requirements

The xFusionCorp Industries team needs a temporary user for a Nautilus project assignment.

Requirements:

1. Create a user named `jim`.
2. Create the user on App Server 3.
3. Set the account expiration date to `2027-04-15`.
4. The user should have a normal login shell for the developer assignment.

**Important:** We should not use `/usr/sbin/nologin` here because the temporary developer needs normal interactive access.

### Step 1: Connect to App Server 3

From the Jump Host:

```bash
ssh banner@stapp03
```

App Server 3:

```text
stapp03
```

### Step 2: Create the User

```bash
sudo useradd jim
```

This creates a new Linux user named `jim`.

### Understanding the command

| Component | Meaning |
|---|---|
| `sudo` | Executes the command with elevated privileges |
| `useradd` | Creates a Linux user |
| `jim` | Username |

On our lab system, the default login shell is used because we did not specify a custom shell.

### Step 3: Set Account Expiration Date

```bash
sudo chage -E 2027-04-15 jim
```

This configures the account expiration date as 15 April 2027.

### Understanding the command

| Component | Meaning |
|---|---|
| `sudo` | Executes with elevated privileges |
| `chage` | Changes account aging information |
| `-E` | Sets account expiration date |
| `2027-04-15` | Expiration date |
| `jim` | Target username |

### Step 4: Verify Account Expiration

```bash
sudo chage -l jim
```

This displays the account aging information.

We should look for:

```text
Account expires : Apr 15, 2027
```

### Step 5: Verify User Creation

```bash
id jim
```

This displays the user's UID, GID and group membership.

We can also inspect the account entry:

```bash
getent passwd jim
```

Check that the user has the expected login shell.

---

## 7. Understanding What Happens After Account Expiration

Suppose a user account has this setting:

```bash
sudo chage -E 2027-04-15 jim
```

The account is configured to expire on the specified date.

After expiration:

- Normal authentication using the expired account is denied.
- The account is not automatically deleted.
- The user's files are not automatically deleted.
- An administrator can inspect or modify the account.

**Important:** Account expiration does not necessarily terminate existing login sessions or processes immediately.

Also, account expiration and password locking are different operations.

---

## 8. Five Things You Must Remember

1. A temporary user account can have a predefined expiration date.
2. `chage` manages password aging and account expiration.
3. `chage -E` sets the account expiration date.
4. `chage -M` sets the maximum password age.
5. `/etc/shadow` stores sensitive password and account aging information.

---

## 9. Quick Revision Questions

Try answering these without looking at the notes:

1. What is a temporary Linux user?
2. Why do organizations create temporary accounts?
3. What is the purpose of the `chage` command?
4. What does `chage -E` do?
5. What does `chage -M` do?
6. What is the difference between account expiration and password expiration?
7. Where is password aging information stored?
8. Which command displays account expiration information?
9. Does account expiration automatically delete the user?
10. Why did we use a normal login shell instead of `/usr/sbin/nologin` for `jim`?

---

## 10. Final Memory Trick

| Command / File | Remember it as |
|---|---|
| `useradd` | Create a user |
| `id` | Check user identity |
| `getent passwd` | Retrieve user information |
| `chage` | Manage account aging |
| `chage -E` | Set account expiration |
| `chage -M` | Set maximum password age |
| `chage -l` | List account aging information |
| `/etc/passwd` | User account information |
| `/etc/shadow` | Password and aging information |

---

**Day 2 Completed - Temporary User Setup with Expiry.**

**Next Topic: Day 3 - Secure Root SSH Access.**
