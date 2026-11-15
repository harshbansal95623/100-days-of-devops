## 1. What is SELinux?

**SELinux = Security-Enhanced Linux**

SELinux is a Linux security mechanism that provides **Mandatory Access Control (MAC)**.

In simple words, SELinux controls what a process is allowed to access, even when normal Linux permissions allow that access.

### Why do we need SELinux?

Imagine:

- An Apache web server gets compromised.
- An attacker tries to access sensitive system files.
- Normal Linux permissions might allow some access.
- SELinux can block that access based on its security policy.

**Remember:**

- Linux permissions = Who can access a file?
- SELinux = Is this process allowed to access this resource?

Both security layers work together.

---

## 2. DAC vs MAC

| DAC | MAC |
|---|---|
| Discretionary Access Control | Mandatory Access Control |
| Traditional Linux permissions | SELinux security policies |
| Uses `chmod`, `chown` | Uses security contexts and policies |
| Based on users and groups | Based on process/resource labels and policy |

**Memory trick:** DAC = Permissions. MAC = Policy.

---

## 3. Three SELinux Modes

| Mode | Behaviour |
|---|---|
| Enforcing | Blocks policy violations and logs them |
| Permissive | Allows actions but logs policy violations |
| Disabled | SELinux is turned off |

### Important commands

**Check current SELinux mode:**

```bash
getenforce
```

**Show detailed SELinux status:**

```bash
sestatus
```

**Temporarily change to Permissive mode:**

```bash
sudo setenforce 0
```

**Temporarily change to Enforcing mode:**

```bash
sudo setenforce 1
```

### Memory trick

- `0` = Permissive
- `1` = Enforcing

These commands change the running mode, not the persistent boot configuration.

---

## 4. SELinux Security Contexts

SELinux attaches security labels to files, directories and processes.

Example:

```text
system_u:object_r:httpd_sys_content_t:s0
```

There are four fields:

| Field | Meaning |
|---|---|
| `system_u` | SELinux user |
| `object_r` | SELinux role |
| `httpd_sys_content_t` | SELinux type |
| `s0` | Security sensitivity level |

### Most Important: SELinux Type

Example:

`httpd_sys_content_t`

- `httpd` = HTTP daemon / web server
- `sys_content` = Web content
- `_t` = Type

This type is commonly used for files that web servers can read.

**Example:**

Apache may be allowed to read a file with the correct web-content label, but blocked from reading a file with an unsuitable label.

### What is `s0`?

- Represents a security sensitivity level.
- Used in SELinux security-level mechanisms such as MLS.
- It is not a Linux permission.
- Basic SELinux configurations commonly use `s0`.

**Remember:** Type identifies the resource category used in policy decisions. `s0` represents its security level.

---

## 5. SELinux Configuration File

**Main configuration file:**

```bash
/etc/selinux/config
```

### Why is this file important?

It stores SELinux settings used during system startup.

Example:

```ini
SELINUX=enforcing
SELINUXTYPE=targeted
```

### A. SELINUX

Controls the intended SELinux operating mode at boot.

Possible values:

```ini
SELINUX=enforcing
SELINUX=permissive
SELINUX=disabled
```

### B. SELINUXTYPE

Specifies which SELinux policy to use.

Common setting:

```ini
SELINUXTYPE=targeted
```

**What is targeted?**

It focuses SELinux restrictions on selected services and processes, rather than applying the same confinement to every process.

### Remember the difference

- `SELINUX` = Operating mode.
- `SELINUXTYPE` = Security policy.

---

## 6. Temporary vs Permanent Configuration

### Temporary Configuration

```bash
sudo setenforce 0
```

- Changes the running mode immediately.
- Does not edit the configuration file.
- Does not survive reboot as a persistent setting.

### Permanent Configuration

Edit the configuration file:

```bash
sudo nano /etc/selinux/config
```

Change:

```ini
SELINUX=enforcing
```

To:

```ini
SELINUX=permissive
```

Or:

```ini
SELINUX=disabled
```

The configuration takes effect during boot.

**Important:** Switching from Disabled to Enforcing requires additional preparation and may require filesystem relabeling. Never casually disable SELinux on production servers.

---

## 7. Important SELinux Commands

| Command | Purpose |
|---|---|
| `getenforce` | Shows current mode |
| `sestatus` | Displays detailed SELinux status |
| `setenforce 0` | Switches to Permissive |
| `setenforce 1` | Switches to Enforcing |
| `ls -Z` | Displays file security contexts |
| `ps -Z` | Displays process security contexts |
| `restorecon` | Restores default file contexts |
| `semanage` | Manages SELinux policy settings and labels |

---

# 8. Practical Lab: SELinux Installation and Configuration

## Task Requirements

On App Server 1 (`stapp01`):

1. Install required SELinux packages.
2. Permanently disable SELinux.
3. Do not reboot the server.
4. Final status after reboot should be Disabled.

### Step 1: Connect to App Server 1

From the Jump Host:

```bash
ssh tony@stapp01
```

### Step 2: Identify the Operating System

```bash
cat /etc/os-release
```

Our server was running:

```text
CentOS Stream 9
```

Since it is CentOS Stream 9, we use `dnf` for package installation.

### Step 3: Install Required Packages

```bash
sudo dnf install -y policycoreutils selinux-policy selinux-policy-targeted
```

**Package purposes:**

- `policycoreutils` - SELinux management utilities.
- `selinux-policy` - Base SELinux policies.
- `selinux-policy-targeted` - Targeted policy package.

### Step 4: Verify Package Installation

```bash
rpm -q policycoreutils selinux-policy selinux-policy-targeted
```

**Our output:**

```text
policycoreutils-3.6-9.el9.x86_64
selinux-policy-38.1.87-1.el9.noarch
selinux-policy-targeted-38.1.87-1.el9.noarch
```

All three packages were successfully installed.

### Step 5: Edit SELinux Configuration

```bash
sudo nano /etc/selinux/config
```

Set:

```ini
SELINUX=disabled
```

We used Nano instead of Vi to edit the configuration.

### Step 6: Verify Configuration

```bash
grep '^SELINUX=' /etc/selinux/config
```

**Our output:**

```text
SELINUX=disabled
```

This confirmed that the persistent configuration was updated.

### Step 7: Check SELinux Status

```bash
sestatus
```

**Our output:**

```text
SELinux status: disabled
```

### Final Result

All task requirements were satisfied:

- SELinux packages installed.
- Persistent configuration set to Disabled.
- No reboot performed.
- Current SELinux status showed Disabled.

**Note:** The task required the final status after reboot to be Disabled. The configuration file was the important confirmation for that requirement. In our lab, the current status was also already Disabled.

---

# 9. Five Things You Must Remember

1. SELinux adds another security layer on top of traditional Linux permissions.
2. Enforcing blocks, Permissive logs, Disabled turns SELinux off.
3. Security contexts label resources and processes.
4. `SELINUX` specifies the mode; `SELINUXTYPE` specifies the policy.
5. `setenforce` changes the running mode; `/etc/selinux/config` controls the intended boot configuration.

---

# 10. Quick Revision Questions

Try answering these without looking at the notes:

1. What is SELinux?
2. What is the difference between DAC and MAC?
3. What is the difference between Enforcing and Permissive?
4. What does `setenforce 0` do?
5. What is the purpose of `httpd_sys_content_t`?
6. What does `s0` represent?
7. Where is the SELinux configuration file?
8. What is the difference between `SELINUX` and `SELINUXTYPE`?
9. Which command displays the current SELinux mode?
10. Why did we not need to reboot immediately after changing the persistent configuration?

---

## Final Memory Trick

| Command / File | Remember it as |
|---|---|
| `getenforce` | Current mode |
| `setenforce` | Change running mode |
| `/etc/selinux/config` | Boot configuration |
| `ls -Z` | File security labels |
| `sestatus` | Detailed status |
| `rpm -q` | Verify installed packages |

---

**Day 5 Completed - SELinux Installation and Configuration.**

**Next Topic: Day 6 - Create a Cron Job.**
