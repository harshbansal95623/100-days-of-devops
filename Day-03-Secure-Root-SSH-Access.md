
# Day 3 – Secure Root SSH Access

## 1. Objective

Disable direct SSH login for the root user on all three application servers:

- App Server 1: `stapp01`
- App Server 2: `stapp02`
- App Server 3: `stapp03`

Apply the configuration change to the running SSH service.

## 2. What is SSH?

SSH stands for **Secure Shell**.

It is a network protocol that allows us to securely access and manage remote Linux servers.

Example:

```bash
ssh username@server
```

SSH commonly uses port `22` and encrypts communication between the client and server.

## 3. What is the Root User?

The root user is the superuser in Linux.

- Root has administrative privileges.
- Root can modify system files and configurations.
- Root can install and remove software.
- Root can manage users and permissions.

Because root has extensive privileges, allowing direct remote login can increase security risks.

## 4. Why Disable Root SSH Login?

Disabling direct root SSH login helps:

1. Reduce the risk of unauthorized administrative access.
2. Prevent attackers from directly targeting the root account over SSH.
3. Encourage administrators to log in with individual accounts.
4. Maintain better accountability through individual user accounts and `sudo`.

**Important:** Disabling root SSH login does not disable the root account. Administrators can still use `sudo` when authorized.

## 5. Important SSH Configuration Files

Linux has two commonly used SSH configuration files.

| File | Purpose |
|---|---|
| `/etc/ssh/ssh_config` | Client-side SSH configuration |
| `/etc/ssh/sshd_config` | Server-side SSH daemon configuration |

### Difference

- `ssh_config`: Controls how your machine connects to other SSH servers.
- `sshd_config`: Controls how other machines connect to your SSH server.

For this task, we must edit:

```bash
/etc/ssh/sshd_config
```

## 6. Important Configuration: PermitRootLogin

The `PermitRootLogin` directive controls whether root can log in through SSH.

Common values:

| Value | Meaning |
|---|---|
| `yes` | Allows root SSH login |
| `no` | Completely disables direct root SSH login |
| `prohibit-password` | Disallows password authentication for root but may allow key-based login |
| `forced-commands-only` | Allows root login only for forced commands |

For this task, we need:

```text
PermitRootLogin no
```

### What about the # symbol?

A line beginning with `#` is a comment and is not an active configuration directive.

Example:

```text
#PermitRootLogin prohibit-password
```

This is commented out.

The active configuration should be:

```text
PermitRootLogin no
```

## 7. Practical Implementation

### Server Details

| Server | Hostname | Login User |
|---|---|---|
| App Server 1 | stapp01 | tony |
| App Server 2 | stapp02 | steve |
| App Server 3 | stapp03 | banner |

### Step 1: Connect to the server

From the jump host, connect to the application server:

```bash
ssh tony@stapp01
```

Use the appropriate username and hostname for each server.

### Step 2: Open the SSH daemon configuration

```bash
sudo nano /etc/ssh/sshd_config
```

Find the `PermitRootLogin` directive.

Set it to:

```text
PermitRootLogin no
```

Save the file.

### Step 3: Validate the configuration

Before applying the change, check the SSH configuration syntax:

```bash
sudo sshd -t
```

If the command produces no output, the configuration syntax is valid.

### Step 4: Check the effective configuration

```bash
sudo sshd -T | grep permitrootlogin
```

Expected output:

```text
permitrootlogin no
```

This checks the effective SSH daemon setting.

### Step 5: Reload the SSH service

Apply the configuration without unnecessarily stopping the SSH service:

```bash
sudo systemctl reload sshd
```

On systems where the service is named `ssh`, use:

```bash
sudo systemctl reload ssh
```

### Step 6: Repeat on all application servers

Perform the same configuration and validation on:

- `stapp01`
- `stapp02`
- `stapp03`

## 8. Important Safety Precautions

Before changing SSH configuration:

1. Keep your existing SSH session open.
2. Make sure another account with working `sudo` access exists.
3. Validate the configuration using `sshd -t`.
4. Reload the SSH service only after validation.
5. Test a new SSH connection before closing your existing session.

A mistake in SSH configuration can prevent remote access to a server.

## 9. Useful Commands

| Command | Purpose |
|---|---|
| `ssh user@host` | Connect to a remote server |
| `sudo nano /etc/ssh/sshd_config` | Edit SSH server configuration |
| `sudo sshd -t` | Validate SSH configuration syntax |
| `sudo sshd -T` | Display effective SSH configuration |
| `sudo systemctl status sshd` | Check SSH service status |
| `sudo systemctl reload sshd` | Reload SSH configuration |
| `grep` | Search for a configuration directive |

## 10. Key Takeaways

- SSH provides secure remote access to Linux servers.
- Root is the Linux superuser.
- Direct root SSH login can increase security risks.
- `ssh_config` is for the SSH client.
- `sshd_config` is for the SSH server.
- `PermitRootLogin no` disables direct root SSH login.
- `sshd -t` checks configuration syntax.
- `sshd -T` displays effective configuration.
- Reloading SSH applies configuration changes.

## 11. Final Verification Checklist

- [ ] Root SSH login disabled on `stapp01`.
- [ ] Root SSH login disabled on `stapp02`.
- [ ] Root SSH login disabled on `stapp03`.
- [ ] SSH configuration syntax validated.
- [ ] Effective configuration checked.
- [ ] SSH service reloaded.
- [ ] Existing administrative access verified.

---

**Day 3 Completed - Secure Root SSH Access.**

**Next Topic: Day 4 - Script Execution Permissions.**
