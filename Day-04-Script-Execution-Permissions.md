
# Day 4 – Script Execution Permissions

## 1. Objective

Understand Linux file permissions and configure a shell script so that all users can execute it.

### Practical Task

On App Server 3 (`stapp03`), configure the following file:

```text
/tmp/xfusioncorp.sh
```

All users must be able to execute the script directly.

## 2. Understanding Linux File Permissions

Linux uses permissions to control who can access files and directories.

There are three basic permission types:

| Permission | Symbol | Numeric Value | Meaning |
|---|---|---:|---|
| Read | `r` | 4 | Read file contents |
| Write | `w` | 2 | Modify file contents |
| Execute | `x` | 1 | Execute a file or access a directory |

## 3. Understanding Permission Groups

Linux permissions are divided into three groups:

| Group | Symbol | Meaning |
|---|---|---|
| User | `u` | Owner of the file |
| Group | `g` | Group associated with the file |
| Others | `o` | Everyone else |

For example:

```text
-rwxr-xr-x
```

Breakdown:

| Part | Permission | Meaning |
|---|---|---|
| `-` | File type | Regular file |
| `rwx` | Owner | Read, write, execute |
| `r-x` | Group | Read and execute |
| `r-x` | Others | Read and execute |

The first character represents the file type. The remaining nine characters represent permissions.

## 4. Understanding chmod

`chmod` stands for **Change Mode**.

It is used to change file and directory permissions.

### Symbolic Method

```bash
chmod +x filename
```

Adds execute permission to the file.

Other examples:

```bash
chmod u+x filename
chmod g+x filename
chmod o+x filename
chmod a+x filename
```

| Command | Meaning |
|---|---|
| `u+x` | Add execute permission for owner |
| `g+x` | Add execute permission for group |
| `o+x` | Add execute permission for others |
| `a+x` | Add execute permission for everyone |

### Numeric Method

Numeric permissions use these values:

- Read = 4
- Write = 2
- Execute = 1

The values are added together.

| Permission | Calculation | Value |
|---|---|---:|
| `---` | 0 | 0 |
| `--x` | 1 | 1 |
| `-w-` | 2 | 2 |
| `-wx` | 2 + 1 | 3 |
| `r--` | 4 | 4 |
| `r-x` | 4 + 1 | 5 |
| `rw-` | 4 + 2 | 6 |
| `rwx` | 4 + 2 + 1 | 7 |

### Common chmod Examples

```bash
chmod 755 filename
chmod 644 filename
chmod 700 filename
chmod 777 filename
```

| Permission | Owner | Group | Others |
|---|---|---|---|
| `755` | `rwx` | `r-x` | `r-x` |
| `644` | `rw-` | `r--` | `r--` |
| `700` | `rwx` | `---` | `---` |
| `777` | `rwx` | `rwx` | `rwx` |

**Security note:** Avoid using `777` unless there is a specific reason. It grants everyone read, write, and execute permissions.

## 5. Practical Implementation

### Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
```

### Step 2: Check the existing permissions

```bash
ls -l /tmp/xfusioncorp.sh
```

The initial permissions in this lab were:

```text
----------
```

This means no read, write, or execute permissions were granted to the owner, group, or others.

### Step 3: Try adding execute permission

```bash
chmod +x /tmp/xfusioncorp.sh
```

In this lab, the command failed with:

```text
Operation not permitted
```

The reason was that the current user did not have permission to change the file's permissions.

### Step 4: Use sudo

```bash
sudo chmod +x /tmp/xfusioncorp.sh
```

This changed the permissions to:

```text
---x--x--x
```

However, the lab still did not pass.

### Step 5: Configure the correct permissions

The script needs both read and execute permissions for everyone.

Run:

```bash
sudo chmod 755 /tmp/xfusioncorp.sh
```

Alternatively:

```bash
sudo chmod a+rx /tmp/xfusioncorp.sh
```

### Step 6: Verify the permissions

```bash
ls -l /tmp/xfusioncorp.sh
```

Expected permissions:

```text
-rwxr-xr-x
```

This means:

- Owner: Read, write, execute.
- Group: Read and execute.
- Others: Read and execute.

All users can now read and execute the script.

## 6. Why Execute Permission Alone Was Not Enough

In this lab, adding only execute permission resulted in:

```text
---x--x--x
```

But the script also needed read permission.

When a shell interpreter reads a script, it needs access to the script contents.

Therefore, the required permissions were:

```text
-rwxr-xr-x
```

## 7. Useful Commands

| Command | Purpose |
|---|---|
| `ls -l filename` | View file permissions |
| `chmod +x filename` | Add execute permission |
| `chmod 755 filename` | Set permissions to 755 |
| `chmod 644 filename` | Set permissions to 644 |
| `chmod a+rx filename` | Add read and execute permissions for everyone |
| `stat filename` | Display detailed file information |
| `whoami` | Display current username |
| `id` | Display user and group information |

## 8. Key Takeaways

- Linux permissions control access to files and directories.
- `r` means read, `w` means write, and `x` means execute.
- Permissions apply to owner, group, and others.
- `chmod` changes file permissions.
- `sudo` can be used when administrative privileges are required.
- `755` gives the owner full permissions and gives group and others read and execute permissions.
- Execute permission alone may not be sufficient to run a script.
- `ls -l` helps verify permissions.

## 9. Final Verification Checklist

- [x] Connected to App Server 3.
- [x] Checked the original file permissions.
- [x] Attempted to add execute permission.
- [x] Used sudo after the permission error.
- [x] Identified why execute-only permissions were insufficient.
- [x] Applied `755` permissions.
- [x] Verified the expected permission format.

---

**Day 4 Completed - Script Execution Permissions.**

**Next Topic: Day 5 - SELinux Installation and Configuration.**
