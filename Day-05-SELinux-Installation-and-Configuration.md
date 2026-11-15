1. What is SELinux?

SELinux = Security-Enhanced Linux

SELinux is a Linux security mechanism that provides Mandatory Access Control (MAC).

In simple words, SELinux controls what a process is allowed to access, even when normal Linux permissions allow that access.

Why do we need SELinux?

Imagine:

An Apache web server gets compromised.

An attacker tries to access sensitive system files.

Normal Linux permissions might allow some access.

SELinux can block that access based on its security policy.

Remember:

Linux permissions = Who can access a file?

SELinux = Is this process allowed to access this resource?

Both security layers work together.

2. DAC vs MAC

DAC

MAC

Discretionary Access Control

Mandatory Access Control

Traditional Linux permissions

SELinux security policies

Uses chmod, chown

Uses security contexts and policies

Based on users and groups

Based on process/resource labels and policy

Memory trick: DAC = Permissions. MAC = Policy.

3. Three SELinux Modes

Mode

Behaviour

Enforcing

Blocks policy violations and logs them

Permissive

Allows actions but logs policy violations

Disabled

SELinux is turned off

Important commands

Check current mode:

getenforce

Show detailed SELinux status:

sestatus

Temporarily change to Permissive:

sudo setenforce 0

Temporarily change to Enforcing:

sudo setenforce 1

Memory trick:

0 = Permissive

1 = Enforcing

These commands change the running mode, not the persistent boot configuration. setenforce works when SELinux is enabled and currently in Enforcing or Permissive mode.

4. SELinux Security Contexts

SELinux attaches security labels to files, directories and processes.

Example:

system_u:object_r:httpd_sys_content_t:s0

There are four fields:

Field

Meaning

system_u

SELinux user

object_r

SELinux role

httpd_sys_content_t

SELinux type

s0

Security sensitivity level

Understanding SELinux Type

Example:

httpd_sys_content_t

httpd = HTTP daemon / web server

sys_content = Web content

_t = Type

This type is commonly used for files that web servers can read.

For example, Apache may be allowed to read a file with the correct web-content label but blocked from reading a file with an unsuitable label.

What is s0?

Represents a security sensitivity level.

Used in SELinux security-level mechanisms such as MLS.

It is not a Linux permission.

Basic SELinux configurations commonly use s0.

Remember: Type identifies the resource category used in policy decisions. s0 represents its security level.

5. SELinux Configuration File

Main configuration file:

/etc/selinux/config

Why is this file important?

It stores SELinux settings used during system startup.

Example:

SELINUX=enforcing
SELINUXTYPE=targeted

A. SELINUX

Controls the intended SELinux operating mode at boot.

Possible values:

SELINUX=enforcing
SELINUX=permissive
SELINUX=disabled

B. SELINUXTYPE

Specifies which SELinux policy to use.

Common setting:

SELINUXTYPE=targeted

What is targeted?

It focuses SELinux restrictions on selected services and processes, rather than applying the same confinement to every process.

Remember the difference

SELINUX = Operating mode.

SELINUXTYPE = Security policy.

6. Temporary vs Permanent Configuration

Temporary configuration

sudo setenforce 0

Changes the running mode immediately.

Does not edit the configuration file.

Does not survive reboot as a persistent setting.

Permanent configuration

Edit:

sudo nano /etc/selinux/config

Change:

SELINUX=enforcing

To:

SELINUX=permissive

Or:

SELINUX=disabled

The configuration takes effect during boot.

Important: Switching from Disabled to Enforcing requires additional preparation and may require filesystem relabeling. Never casually disable SELinux on production servers.

7. Important SELinux Commands

Command

Purpose

getenforce

Shows current mode

sestatus

Detailed SELinux status

setenforce 0

Switches to Permissive

setenforce 1

Switches to Enforcing

ls -Z

Displays file security contexts

ps -Z

Displays process security contexts

restorecon

Restores default file contexts

semanage

Manages SELinux policy settings and labels

8. Practical Lab — SELinux Installation and Configuration

Task requirements

On App Server 1 (stapp01):

Install required SELinux packages.

Permanently disable SELinux.

Do not reboot the server.

Final status after reboot should be Disabled.

Step 1: Connect to App Server 1

From the Jump Host:

ssh tony@stapp01

Step 2: Identify the operating system

cat /etc/os-release

Our server was running:

CentOS Stream 9

Since it is CentOS Stream 9, we use dnf for package installation.

Step 3: Install required packages

sudo dnf install -y policycoreutils selinux-policy selinux-policy-targeted

Package purposes:

policycoreutils — SELinux management utilities.

selinux-policy — Base SELinux policies.

selinux-policy-targeted — Targeted policy package.

Step 4: Verify package installation

rpm -q policycoreutils selinux-policy selinux-policy-targeted

Our output confirmed all three packages were installed:

policycoreutils-3.6-9.el9.x86_64
selinux-policy-38.1.87-1.el9.noarch
selinux-policy-targeted-38.1.87-1.el9.noarch

Step 5: Edit SELinux configuration

sudo nano /etc/selinux/config

Set:

SELINUX=disabled

We used Nano instead of Vi to edit the configuration.

Step 6: Verify configuration

grep '^SELINUX=' /etc/selinux/config

Our output:

SELINUX=disabled

This confirmed that the persistent configuration was updated.

Step 7: Check SELinux status

sestatus

Our output:

SELinux status: disabled

Final result

All task requirements were satisfied:

SELinux packages installed.

Persistent configuration set to Disabled.

No reboot performed.

Current SELinux status showed Disabled.

Note: The task required the final status after reboot to be Disabled. The configuration file was the important confirmation for that requirement. In our lab, the current status was also already Disabled.

9. Five Things You Must Remember

SELinux adds another security layer on top of traditional Linux permissions.

Enforcing blocks, Permissive logs, Disabled turns SELinux off.

Security contexts label resources and processes.

SELINUX specifies the mode; SELINUXTYPE specifies the policy.

setenforce changes the running mode; /etc/selinux/config controls the intended boot configuration.

10. Quick Revision Questions

Try answering these without looking at the notes:

What is SELinux?

What is the difference between DAC and MAC?

What is the difference between Enforcing and Permissive?

What does setenforce 0 do?

What is the purpose of httpd_sys_content_t?

What does s0 represent?

Where is the SELinux configuration file?

What is the difference between SELINUX and SELINUXTYPE?

Which command displays the current SELinux mode?

Why did we not need to reboot immediately after changing the persistent configuration?

Final Memory Trick

Command / File

Remember it as

getenforce

Current mode

setenforce

Change running mode

/etc/selinux/config

Boot configuration

ls -Z

File security labels

sestatus

Detailed status

rpm -q

Verify installed packages
