
# Day 1: Linux User Setup with Non-Interactive Shell

**Date:** 10 November 2026  
**Category:** Linux Administration & User Management  
**Platform:** KodeKloud - Stratos Datacenter  
**Topic:** Linux Users and Non-Interactive Shells

---

## 1. What is a Linux User?

Linux is a multi-user operating system. This means multiple users can access and use the same system with different permissions.

Every Linux user has:

- A username.
- A User ID (UID).
- A primary group.
- A home directory (usually).
- A login shell.

### Why do we create different users?

Different users help us:

- Control access to system resources.
- Separate administrative and regular accounts.
- Assign permissions to individuals.
- Run applications and services securely.

**Remember:** Every user has an identity (UID) that Linux uses to manage permissions.

---

## 2. What is a Shell?

A shell is a program that provides an interface between the user and the operating system.

When you open a Linux terminal and execute commands, you are interacting with a shell.

Common Linux shells:

| Shell | Description |
|---|---|
| `/bin/bash` | Bash shell |
| `/bin/sh` | Standard shell interface |
| `/bin/zsh` | Z shell |
| `/usr/sbin/nologin` | Prevents interactive login |
| `/bin/false` | Immediately exits without providing a shell |

### What is an Interactive Shell?

An interactive shell allows users to log in and execute commands through a terminal.

Example:

```bash
/bin/bash
```

A user with Bash as their login shell can normally access an interactive terminal, subject to authentication and account restrictions.

---

## 3. What is a Non-Interactive Shell?

A non-interactive login shell prevents a user from obtaining a normal interactive shell session.

A common example is:

```bash
/usr/sbin/nologin
```

When someone attempts to log in using an account configured with `nologin`, the system refuses the interactive session.

### Why do we need it?

Imagine an organization has a web server running under a dedicated user called `nginx`.

Does the Nginx service need a human to log in using that account?

Usually, no.

The service needs an identity to run its processes, but it doesn't need an interactive login.

This is where `nologin` is useful.

### Real-world examples

- Nginx service accounts.
- Database service accounts.
- Application service accounts.
- Other system-managed service identities.

**Remember:** Service accounts need to run services, not necessarily provide human login access.

---

## 4. Difference Between `/bin/bash` and `/usr/sbin/nologin`

| Feature | `/bin/bash` | `/usr/sbin/nologin` |
|---|---|---|
| Interactive login | Allowed | Prevented |
| Terminal access | Yes | No |
| Execute commands | Yes | Not through an interactive login |
| Common usage | Human users | Service accounts |

### Important clarification

`nologin` is not a complete security boundary.

A user configured with `nologin` may still be able to execute a command through another authorized mechanism, such as:

```bash
sudo -u username id
```

This does not provide an interactive login shell.

**Remember:** Preventing interactive login does not mean preventing every possible execution of a command.

---

## 5. Creating a Linux User with a Non-Interactive Shell

We can create a user using the `adduser` command.

Example:

```bash
sudo adduser -s /usr/sbin/nologin harsh
```

### Understanding the command

| Component | Meaning |
|---|---|
| `sudo` | Executes the command with elevated privileges |
| `adduser` | Creates a Linux user |
| `-s` | Specifies the user's login shell |
| `/usr/sbin/nologin` | Sets the non-interactive login shell |
| `harsh` | Username |

### Alternative using useradd

```bash
sudo useradd -s /usr/sbin/nologin harsh
```

On many Linux distributions, `adduser` and `useradd` are related utilities, but their behaviour and options can differ.

**Remember:** `-s` specifies the login shell.

---

## 6. Important Commands for Linux User Management

| Command | Purpose |
|---|---|
| `adduser username` | Creates a user |
| `useradd username` | Creates a user |
| `id username` | Displays UID and group information |
| `getent passwd username` | Retrieves user account information |
| `cat /etc/passwd` | Displays local user account entries |
| `passwd username` | Sets or changes a user's password |
| `usermod` | Modifies an existing user |
| `userdel username` | Deletes a user |
| `whoami` | Displays the current username |

---

## 7. Understanding `/etc/passwd`

Linux stores local user account information in:

```bash
/etc/passwd
```

You can inspect a particular user using:

```bash
grep '^harsh:' /etc/passwd
```

Example output:

```text
harsh:x:1001:1001::/home/harsh:/usr/sbin/nologin
```

### Understanding the fields

The `/etc/passwd` file contains seven colon-separated fields.

| Field | Example | Meaning |
|---|---|---|
| 1 | `harsh` | Username |
| 2 | `x` | Password information stored separately |
| 3 | `1001` | User ID (UID) |
| 4 | `1001` | Primary Group ID (GID) |
| 5 | Empty | User information/comment |
| 6 | `/home/harsh` | Home directory |
| 7 | `/usr/sbin/nologin` | Login shell |

**Important:** The `x` indicates that password information is stored in the shadow password file, usually `/etc/shadow`.

### Memory trick

The last field of `/etc/passwd` tells us which login shell is configured for the user.

---

## 8. Practical Lab: Linux User Setup with Non-Interactive Shell

### Task Objective

Create a Linux user with a non-interactive shell.

The requirement is to prevent the user from obtaining an interactive login session.

### Step 1: Create the user

```bash
sudo adduser -s /usr/sbin/nologin harsh
```

### Step 2: Verify the user

```bash
id harsh
```

This displays the user's UID and group information.

### Step 3: Verify the configured login shell

```bash
getent passwd harsh
```

Or:

```bash
grep '^harsh:' /etc/passwd
```

Check that the final field is:

```text
/usr/sbin/nologin
```

### Step 4: Understand the non-interactive shell

Check the user's account entry:

```bash
getent passwd harsh
```

The final field should identify `/usr/sbin/nologin`.

This confirms that the account is configured to prevent interactive login.

---

## 9. Service Accounts: Real-World Example

Suppose we want to create a service account for an application.

We can use:

```bash
sudo useradd --system --no-create-home --shell /usr/sbin/nologin appuser
```

### Understanding the options

| Option | Purpose |
|---|---|
| `--system` | Creates a system account |
| `--no-create-home` | Does not create a home directory |
| `--shell` | Specifies the login shell |
| `/usr/sbin/nologin` | Prevents interactive login |
| `appuser` | Username |

### Why use service accounts?

- Improves security.
- Separates application processes from human accounts.
- Limits unnecessary interactive access.
- Makes ownership and permission management easier.

**Important:** A service account should have only the permissions required by its application.

---

## 10. Five Things You Must Remember

1. Linux is a multi-user operating system.
2. Every Linux user has a UID and group information.
3. A shell provides an interface for interacting with Linux.
4. `/usr/sbin/nologin` prevents interactive login.
5. Service accounts can run application processes without needing human login access.

---

## 11. Quick Revision Questions

Try answering these without looking at the notes:

1. What is a Linux user?
2. What is a shell?
3. What is an interactive shell?
4. What is a non-interactive shell?
5. What is the purpose of `/usr/sbin/nologin`?
6. What is the difference between `/bin/bash` and `/usr/sbin/nologin`?
7. What does the `-s` option do in `adduser`?
8. Where is Linux user account information stored?
9. What does UID represent?
10. Why do service accounts commonly use non-interactive shells?

---

## Final Memory Trick

| Command / File | Remember it as |
|---|---|
| `adduser` | Create a user |
| `useradd` | Create a user |
| `id` | Check user identity |
| `getent passwd` | Retrieve user information |
| `/etc/passwd` | Local user account information |
| `/etc/shadow` | Password-related account information |
| `/bin/bash` | Interactive shell |
| `/usr/sbin/nologin` | Prevent interactive login |
| `usermod` | Modify a user |

---

**Day 1 Completed - Linux User Setup with Non-Interactive Shell.**

**Next Topic: Day 2 - Temporary User Setup with Expiry.**
