# Day 6 - Create a Cron Job

## 1. What is Cron?

Cron is a time-based job scheduler in Linux.

It allows us to schedule commands or scripts to run automatically at
specific times without manually executing them.

In simple words:

**Cron = Automatically execute commands at scheduled times.**

### Why do we need Cron?

Imagine:

-   A server needs to take a backup every night.
-   A log file needs to be cleaned every Sunday.
-   A script needs to execute every five minutes.
-   A system needs to perform regular maintenance tasks.

Instead of manually running these commands, we can schedule them using
Cron.

------------------------------------------------------------------------

## 2. Cron, Crontab and Crond

These three terms are related but have different purposes.

  Term      Meaning
  --------- ------------------------------------------------------------
  Cron      Time-based job scheduling system
  Crontab   Stores scheduled jobs and their execution times
  Crond     Background service that checks schedules and executes jobs

### How do they work together?

1.  We define a schedule and command in the crontab.
2.  The `crond` service checks the scheduled jobs.
3.  When the scheduled time arrives, `crond` executes the command.
4.  The command performs its task.

Remember:

-   Crontab = Timetable.
-   Crond = Worker checking the timetable.
-   Command = Actual task.

------------------------------------------------------------------------

## 3. Understanding Cron Syntax

A cron expression contains five fields followed by the command.

``` bash
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of the week
│ │ │ └──── Month
│ │ └────── Day of the month
│ └──────── Hour
└────────── Minute
```

### Five fields explained

  Field          Allowed Values   Meaning
  -------------- ---------------- ------------------
  Minute         0--59            At which minute
  Hour           0--23            At which hour
  Day of Month   1--31            On which date
  Month          1--12            In which month
  Day of Week    0--7             On which weekday

### Important Cron Symbols

  Symbol   Meaning           Example
  -------- ----------------- -------------------------------
  `*`      Every             Every hour
  `*/5`    Every 5 units     Every 5 minutes
  `-`      Range             `1-5` means Monday to Friday
  `,`      Specific values   `1,5` means Monday and Friday

### Examples

**A. Every minute**

``` bash
* * * * * command
```

**B. Every 5 minutes**

``` bash
*/5 * * * * command
```

**C. Every hour**

``` bash
0 * * * * command
```

Runs at minute zero of every hour.

**D. Every day at 9 AM**

``` bash
0 9 * * * command
```

**E. Every Saturday at 2 AM**

``` bash
0 2 * * 6 command
```

**F. Every Friday at 6:45 PM**

``` bash
45 18 * * 5 command
```

**G. Every Monday and Friday at 7:30 PM**

``` bash
30 19 * * 1,5 command
```

### Day of Week Values

  Number   Day
  -------- ---------------------------
  0        Sunday
  1        Monday
  2        Tuesday
  3        Wednesday
  4        Thursday
  5        Friday
  6        Saturday
  7        Sunday (alternative to 0)

Remember: Both `0` and `7` represent Sunday in standard cron.

### Important: 12-Hour vs 24-Hour Time

Cron uses the 24-hour clock.

  Time    Cron Hour
  ------- -----------
  6 AM    6
  9 AM    9
  12 PM   12
  6 PM    18
  9 PM    21
  11 PM   23

------------------------------------------------------------------------

## 4. Understanding Output Redirection

Linux provides operators to redirect command output into files.

Two important operators are:

### A. Greater Than (`>`)

Overwrites the existing file content.

Example:

``` bash
echo hello > file.txt
```

If the file already contains something, its previous content is replaced
with `hello`.

If the file does not exist, Linux creates it.

### B. Double Greater Than (`>>`)

Appends new content to the end of the file.

Example:

``` bash
echo hello >> file.txt
```

If the file already contains:

``` text
Apple
```

After executing the command, it contains:

``` text
Apple
hello
```

### Remember the difference

  Operator   Behaviour
  ---------- -----------------------------
  `>`        Overwrites existing content
  `>>`       Appends new content

### Useful Commands

  Command            Purpose
  ------------------ ---------------------------------
  `echo hello`       Prints hello
  `cat file.txt`     Displays file content
  `touch file.txt`   Creates an empty file
  `>`                Redirects and overwrites output
  `>>`               Redirects and appends output

------------------------------------------------------------------------

## 5. Installing and Managing Cron

On our Nautilus application servers, we used the `cronie` package.

### What is Cronie?

Cronie is an implementation of the cron scheduling system for Linux.

It provides the `crond` service that executes scheduled jobs.

### Install Cronie

``` bash
sudo yum install -y cronie
```

### Start the Cron Service

``` bash
sudo systemctl start crond
```

### Check Service Status

``` bash
sudo systemctl status crond
```

Expected status:

``` text
Active: active (running)
```

### Important Commands

  Command                          Purpose
  -------------------------------- ---------------------------
  `sudo yum install -y cronie`     Installs Cronie
  `sudo systemctl start crond`     Starts the cron service
  `sudo systemctl status crond`    Checks service status
  `sudo systemctl stop crond`      Stops the cron service
  `sudo systemctl restart crond`   Restarts the cron service

Remember:

Installing Cronie and starting Crond are two separate operations.

------------------------------------------------------------------------

## 6. Managing Crontab

Linux provides the `crontab` command to manage scheduled jobs.

### Important Crontab Commands

**A. Edit Crontab**

``` bash
crontab -e
```

Opens the current user's crontab for editing.

**B. List Scheduled Jobs**

``` bash
crontab -l
```

Displays the current user's scheduled jobs.

**C. Remove All Scheduled Jobs**

``` bash
crontab -r
```

Removes all scheduled jobs belonging to the current user.

Be careful with this command because it deletes the user's entire
crontab.

### Root User vs Regular User

Each Linux user can have their own crontab.

For example:

``` bash
crontab -e
```

Edits the current user's crontab.

But:

``` bash
sudo crontab -e
```

Edits the root user's crontab.

Similarly:

``` bash
sudo crontab -l
```

Displays root's scheduled jobs.

Important: If a task specifically asks for a cron job under the root
user, use `sudo crontab -e`.

------------------------------------------------------------------------

## 7. Practical Lab: Create a Cron Job

### Task Requirements

The Nautilus system administrators want to test a sample cron job before
deploying automation scripts.

We need to:

1.  Install the `cronie` package on all Nautilus application servers.
2.  Start the `crond` service on all application servers.
3.  Add the following cron job for the root user:

``` bash
*/5 * * * * echo hello > /tmp/cron_text
```

### Application Servers

  Server                     SSH Username
  -------------------------- --------------
  App Server 1 (`stapp01`)   `tony`
  App Server 2 (`stapp02`)   `steve`
  App Server 3 (`stapp03`)   `banner`

### Step 1: Connect to App Server 1

From the Jump Host:

``` bash
ssh tony@stapp01
```

Verify the connection:

``` bash
hostname
whoami
```

### Step 2: Install Cronie

``` bash
sudo yum install -y cronie
```

### Step 3: Start the Cron Service

``` bash
sudo systemctl start crond
```

Verify:

``` bash
sudo systemctl status crond
```

Check for:

``` text
Active: active (running)
```

### Step 4: Add the Cron Job for Root

``` bash
sudo crontab -e
```

Add the following line:

``` bash
*/5 * * * * echo hello > /tmp/cron_text
```

Save and exit the editor.

### Step 5: Verify Root's Crontab

``` bash
sudo crontab -l
```

Expected output:

``` bash
*/5 * * * * echo hello > /tmp/cron_text
```

### Step 6: Verify the Output File

Wait for the scheduled job to execute, then run:

``` bash
cat /tmp/cron_text
```

Expected output:

``` text
hello
```

### Step 7: Repeat on App Server 2

Connect:

``` bash
ssh steve@stapp02
```

Repeat Steps 2--6.

### Step 8: Repeat on App Server 3

Connect:

``` bash
ssh banner@stapp03
```

Repeat Steps 2--6.

### Final Result

Successfully completed the task on all three Nautilus application
servers:

-   `cronie` package installed.
-   `crond` service started.
-   Cron job added to the root user's crontab.
-   Scheduled command configured to execute every five minutes.
-   Output written to `/tmp/cron_text`.

------------------------------------------------------------------------

## 8. Understanding Our Actual Cron Job

Let's break down the exact command used in the lab:

``` bash
*/5 * * * * echo hello > /tmp/cron_text
```

It has two parts.

### Part A: Schedule

``` bash
*/5 * * * *
```

This tells Linux to execute the command every five minutes.

### Part B: Command

``` bash
echo hello > /tmp/cron_text
```

This tells Linux to:

1.  Execute the `echo` command.
2.  Print the word `hello`.
3.  Redirect the output into `/tmp/cron_text`.

### What Happens Every Five Minutes?

  Time    Action
  ------- ------------------------------
  10:00   Writes `hello` into the file
  10:05   Writes `hello` again
  10:10   Writes `hello` again
  10:15   Writes `hello` again

Important: The `>` operator overwrites the existing content. It does not
add another `hello` each time.

If we used `>>` instead, each execution would append another `hello`.

### Understand the Complete Workflow

-   Crontab stores the schedule and command.
-   Crond checks the schedule and executes the command.
-   The command performs the operation.
-   `/tmp/cron_text` stores the command's output.

Remember: **The command runs every five minutes, not the file.**

------------------------------------------------------------------------

## 9. Five Things You Must Remember

1.  Cron is a time-based job scheduler used to automate commands and
    scripts.
2.  Crontab stores scheduled jobs, while Crond executes them.
3.  Cron syntax follows the order: Minute, Hour, Day of Month, Month,
    Day of Week.
4.  `sudo crontab -e` edits root's crontab, while `sudo crontab -l`
    displays it.
5.  `>` overwrites file content, whereas `>>` appends new content.

------------------------------------------------------------------------

## 10. Quick Revision Questions

Try answering these without looking at the notes:

1.  What is Cron, and why do we use it?
2.  What is the difference between Cron, Crontab and Crond?
3.  What are the five fields in cron notation?
4.  What does `*/5 * * * *` mean?
5.  What is the difference between `0 * * * *` and `* 0 * * *`?
6.  What number represents Saturday in cron?
7.  What is the difference between `>` and `>>`?
8.  What does `sudo crontab -e` do?
9.  Why did we use `sudo crontab -e` instead of `crontab -e`?
10. What is the purpose of the `cronie` package?
11. Which command starts the `crond` service?
12. What does `sudo crontab -l` display?
13. What happens when `echo hello > /tmp/cron_text` executes?
14. Does the cron job execute the file or the command?
15. What is the difference between the crontab schedule and the output
    file?

------------------------------------------------------------------------

## Final Memory Trick

  Command / Term            Remember It As
  ------------------------- -------------------------------
  `cronie`                  Cron package
  `crond`                   Background scheduling service
  `crontab -e`              Edit scheduled jobs
  `crontab -l`              List scheduled jobs
  `sudo crontab -e`         Edit root's schedule
  `systemctl start crond`   Start cron service
  `*/5 * * * *`             Every five minutes
  `>`                       Overwrite
  `>>`                      Append
  `/tmp/cron_text`          Output file

------------------------------------------------------------------------

**Day 6 Completed - Create a Cron Job.**

**Next Topic: Day 7 - Linux SSH Authentication.**
