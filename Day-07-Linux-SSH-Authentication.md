# Day 07 – Linux SSH Authentication

## Objective

Configure passwordless SSH authentication from the `thor` user on the Jump Host to all three application servers using their respective sudo users.

### Target connections

| Source | Destination |
|---|---|
| `thor` on Jump Host | `tony@stapp01` |
| `thor` on Jump Host | `steve@stapp02` |
| `thor` on Jump Host | `banner@stapp03` |

---

## 1. Understanding SSH

**SSH (Secure Shell)** is a network protocol used to securely connect to and manage a remote machine.

Example:

```bash
ssh tony@stapp01
```

This means:

- `ssh` – start an SSH connection.
- `tony` – the account to log in as on the remote machine.
- `stapp01` – the remote server's hostname.

SSH normally asks for a password unless another authentication method, such as SSH keys, is configured.

## 2. SSH key-based authentication

SSH key-based authentication uses a pair of mathematically related keys:

| Key | Where it stays | Purpose |
|---|---|---|
| Private key | On the client/Jump Host | Proves the connecting user has the matching secret key. Keep it secret. |
| Public key | Installed on the remote server | Allows the server to recognize the matching private key. It can be shared. |

The public key is normally stored in the remote user's file:

```bash
~/.ssh/authorized_keys
```

When a connection is attempted, the server checks whether the client can prove possession of the private key corresponding to an authorized public key.

**Passwordless does not mean authentication is disabled.** SSH is still authenticating the user; it is using keys instead of asking for the account password.

### How the pieces fit together

1. Generate a key pair on the Jump Host as `thor`.
2. Keep the private key on the Jump Host.
3. Copy the public key to each destination user's `authorized_keys` file.
4. Connect using SSH.
5. The server verifies the key-based authentication, so the account password is not needed for that connection.

## 3. Important commands

### Check the current user and host

```bash
whoami
hostname
```

- `whoami` displays the current logged-in username.
- `hostname` displays the machine's hostname.

### Check the SSH directory

```bash
ls -la ~/.ssh/
```

The `~` represents the current user's home directory. SSH keys and configuration are commonly stored in the `.ssh` directory.

### Generate an RSA key pair

```bash
ssh-keygen -t rsa
```

- `ssh-keygen` – creates SSH keys.
- `-t rsa` – selects the RSA key type.

For this task, we accepted the default file location:

```text
/home/thor/.ssh/id_rsa
```

We left the passphrase empty by pressing Enter twice, as required for this passwordless automation task.

The generated files are:

```text
~/.ssh/id_rsa       # Private key
~/.ssh/id_rsa.pub   # Public key
```

A passphrase can add protection to a private key. Leaving it empty means the key itself does not require a passphrase when used, which is convenient for unattended automation but makes protecting the private-key file especially important.

### Copy the public key to a remote account

```bash
ssh-copy-id username@hostname
```

`ssh-copy-id` installs the selected public key in the remote account's `~/.ssh/authorized_keys` file.

Because we used the default key filename, `ssh-copy-id` automatically found:

```bash
~/.ssh/id_rsa.pub
```

We could also specify the public key explicitly:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub username@hostname
```

Here, `-i` specifies the identity file to install.

### Connect to a remote server

```bash
ssh username@hostname
```

After key-based authentication is configured, the connection should not ask for the remote account password.

### Exit a remote session

```bash
exit
```

This closes the remote shell and returns to the previous machine/session.

---

## 4. Practical task – Step by step

### Step 1: Confirm the Jump Host user

```bash
whoami
hostname
```

We confirmed that the current user was `thor` on the Jump Host.

It is important to generate the key as `thor`, because the key should belong to the account that will initiate the SSH connections.

### Step 2: Check for existing keys

```bash
ls -la ~/.ssh/
```

We checked the `.ssh` directory before generating a new key pair. No existing key pair was present for this task.

### Step 3: Generate the key pair

```bash
ssh-keygen -t rsa
```

We accepted the default save location and left the passphrase empty by pressing Enter twice.

We then verified the files:

```bash
ls -l ~/.ssh/
```

Expected key files:

```text
id_rsa
id_rsa.pub
```

### Step 4: Copy the public key to App Server 1

```bash
ssh-copy-id tony@stapp01
```

If prompted:

- Confirm the server's host fingerprint by typing `yes` when appropriate.
- Enter the remote account password for the initial setup.

### Step 5: Copy the public key to App Server 2

```bash
ssh-copy-id steve@stapp02
```

Complete the initial host confirmation and password prompt if they appear.

### Step 6: Copy the public key to App Server 3

```bash
ssh-copy-id banner@stapp03
```

Complete the initial host confirmation and password prompt if they appear.

**Important:** We generated one key pair for `thor` and installed the same public key for all three remote accounts. The private key remained on the Jump Host.

---

## 5. Verification

We tested each connection from the Jump Host.

### App Server 1

```bash
ssh tony@stapp01
whoami
hostname
exit
```

Expected remote identity:

```text
tony
stapp01
```

### App Server 2

```bash
ssh steve@stapp02
whoami
hostname
exit
```

Expected remote identity:

```text
steve
stapp02
```

### App Server 3

```bash
ssh banner@stapp03
whoami
hostname
exit
```

Expected remote identity:

```text
banner
stapp03
```

The connections succeeded without asking for the remote account passwords. This confirmed that passwordless SSH authentication was configured for all three target accounts.

---

## 6. Do we need the username and IP address only?

No. Knowing a username and server IP address (or hostname) does not automatically give someone permission to install a public key.

For example:

```bash
ssh-copy-id john@192.168.1.10
```

The command still needs an initial way to authenticate and authorize the change. Common possibilities include:

- The remote account's password.
- An SSH key that is already authorized.
- An administrator installing the public key for the account.

The remote server controls which keys are trusted. A public key must be installed in the correct account's `authorized_keys` file, and the connecting client must possess the matching private key.

Only install keys on systems and accounts where you have permission.

---

## 7. Five things to remember

1. SSH provides secure remote access to another machine.
2. `ssh-keygen` generates a public/private key pair.
3. The private key stays on the client and must be protected.
4. `ssh-copy-id` installs a public key in the remote user's `authorized_keys` file.
5. A username and IP address alone do not grant access; the initial setup still requires valid authentication and authorization.

---

## 8. Revision questions

Try answering these without looking at the notes:

1. What is the difference between a public key and a private key?
2. Where should the private key stay?
3. What does `ssh-keygen -t rsa` do?
4. Why did `ssh-copy-id tony@stapp01` work without specifying the public-key filename?
5. What does the `-i` option do in `ssh-copy-id -i ~/.ssh/id_rsa.pub tony@stapp01`?
6. Where does the remote server store an authorized public key?
7. Does passwordless SSH mean that authentication is disabled?
8. If you know a server's username and IP address, can you automatically install your public key there? Why or why not?
9. Why did we generate the key as `thor` rather than as `root`?
10. Why is it important to protect the private key?

---

## Completion checklist

- [x] Confirmed the current user was `thor` on the Jump Host.
- [x] Checked for existing SSH keys.
- [x] Generated an RSA key pair.
- [x] Installed the public key for `tony@stapp01`.
- [x] Installed the public key for `steve@stapp02`.
- [x] Installed the public key for `banner@stapp03`.
- [x] Verified passwordless SSH access to all three app servers.

**Day 07 completed: Linux SSH Authentication.**
