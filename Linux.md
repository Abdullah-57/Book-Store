# Updated Linux Cheat Sheet: User Management, System Administration, Permissions, Init/Systemd, Cron, and Logs

## 1. User Management
### Key Files
- **/etc/passwd**: Stores user account info (username, UID, GID, home directory, shell).
  - View all users: `cat /etc/passwd`
  - Search for a user: `grep 'username' /etc/passwd`
    - Example: `grep 'alice' /etc/passwd` (Shows Alice’s user details, e.g., `alice:x:1001:1001:...` where GID 1001 is her primary group).
- **/etc/shadow**: Stores encrypted passwords and password policies.
  - View (requires root): `sudo cat /etc/shadow`
  - Search for a user: `sudo grep 'username' /etc/shadow`
    - Example: `sudo grep 'alice' /etc/shadow` (Shows Alice’s password info).
- **/etc/group**: Stores group info and secondary group memberships.
  - View groups: `cat /etc/group`
  - Example: `grep 'editors' /etc/group` (Shows `editors:x:3000:alice,bob`).

### Primary vs. Secondary Groups
- **Primary Group**: Default group for a user, set in `/etc/passwd` (GID). New files created by the user are owned by this group by default.
  - Example: If Alice’s primary group is `developers`, new files are owned by `developers`.
- **Secondary Group**: Additional groups a user belongs to, listed in `/etc/group`. These don’t affect new file ownership but grant access to files owned by those groups.
  - Example: If Alice is in `editors` (secondary), she can access files owned by `editors` if permissions allow.
- **Check Groups**: `id username`
  - Example: `id alice` → `uid=1001(alice) gid=2000(developers) groups=2000(developers),3000(editors)` (Primary: `developers`, Secondary: `editors`).

### Add a User
- **adduser**: Friendlier command to add a user with prompts.
  - Syntax: `sudo adduser [options] username`
  - Example: `sudo adduser --gid 2000 alice` (Adds Alice with primary group `developers` (GID 2000)).
- **useradd**: Low-level utility to add a user.
  - Syntax: `sudo useradd [options] username`
  - Example: `sudo useradd -m -g developers -s /bin/bash alice` (Creates user `alice` with home directory, primary group `developers`, and Bash shell).

### Add a Group
- **addgroup**: Friendlier command to add a group.
  - Syntax: `sudo addgroup groupname`
  - Example: `sudo addgroup editors` (Creates a group named `editors`).
- **groupadd**: Low-level utility to add a group.
  - Syntax: `sudo groupadd [options] groupname`
  - Example: `sudo groupadd --gid 3000 editors` (Creates group `editors` with GID 3000).

### Delete a User
- **deluser**: Removes a user.
  - Syntax: `sudo deluser username`
  - Example: `sudo deluser alice` (Deletes user `alice`).
- **userdel**: Low-level utility to delete a user.
  - Syntax: `sudo userdel username`
  - Example: `sudo userdel -r alice` (Deletes user `alice` and their home directory).

### Delete a Group
- **delgroup**: Removes a group.
  - Syntax: `sudo delgroup groupname`
  - Example: `sudo delgroup editors` (Deletes group `editors`).
- **groupdel**: Low-level utility to delete a group.
  - Syntax: `sudo groupdel groupname`
  - Example: `sudo groupdel editors` (Deletes group `editors` if empty).

### Modify a User
- **usermod**: Modifies user account details.
  - Change primary group: `sudo usermod -g groupname username`
    - Example: `sudo usermod -g developers alice` (Sets `developers` as Alice’s primary group).
  - Add to secondary group: `sudo usermod -aG groupname username`
    - Example: `sudo usermod -aG editors alice` (Adds Alice to `editors` group).
  - Change home directory: `sudo usermod -d /new/home username`
    - Example: `sudo usermod -d /home/newalice alice` (Changes Alice’s home to `/home/newalice`).

### Remove User from a Group
- **gpasswd**: Removes a user from a group.
  - Syntax: `sudo gpasswd -d username groupname`
  - Example: `sudo gpasswd -d alice editors` (Removes Alice from `editors` group).

### Change User Shell
- **chsh**: Changes a user’s login shell.
  - Syntax: `sudo chsh -s /path/to/shell username`
  - Example: `sudo chsh -s /bin/zsh alice` (Sets Alice’s shell to Zsh).
  - View available shells: `cat /etc/shells`

### Manage Passwords with `passwd`
- **Change Password**: `passwd username`
  - Example: `passwd alice` (Prompts to set a new password for Alice).
- **Lock Password (Disable User)**: `sudo passwd -l username`
  - Example: `sudo passwd -l bob` (Locks Bob’s account).
- **Unlock Password (Enable User)**: `sudo passwd -u username`
  - Example: `sudo passwd -u bob` (Unlocks Bob’s account).
- **Force Password Change on Next Login**: `sudo passwd -e username`
  - Example: `sudo passwd -e alice` (Forces Alice to change password at next login).
- **Set Max Password Age**: `sudo passwd -x max_days username`
  - Example: `sudo passwd -x 30 alice` (Password expires after 30 days).
- **Set Warning Period**: `sudo passwd -w warn_days username`
  - Example: `sudo passwd -w 7 alice` (Warns Alice 7 days before password expiry).

### Switch User
- **su**: Substitute user to run commands as another user.
  - Syntax: `su username`
  - Example: `su alice` (Switches to Alice’s account).

---

## 2. System Administration
### Cron Jobs
- **Purpose**: Schedules tasks to run automatically at specific times.
- **Crontab Fields**: `minute hour day_of_month month day_of_week command`
  - Example: `0 4 * * * /bin/backup.sh` (Runs backup script daily at 4 AM).

#### Manage Cron Jobs
- **View Cron Syntax**: `/etc/crontab`
- **List Cron Jobs**: `crontab -l`
  - Example: `crontab -l` (Shows scheduled jobs like `0 4 * * * /bin/backup.sh`).
- **Edit Cron Jobs**: `crontab -e`
  - Example: Add `49 16 * * * mkdir -p ~/Desktop/backup` (Creates `backup` folder daily at 4:49 PM).
- **Remove Cron Jobs**: `crontab -r`
  - Example: `crontab -r` (Clears all cron jobs for the user).

#### Example Scenarios
- **Create a Folder Daily**: `49 16 * * * mkdir -p ~/Desktop/backup` (Creates `backup` folder at 4:49 PM daily).
- **Run a Script**: `50 16 * * * ~/Desktop/run_me.sh` (Runs `run_me.sh` at 4:50 PM daily).
- **Write to File for an Hour**: `51 16 * * * ~/Desktop/write_group.sh` (Runs script at 4:51 PM to write “group” every minute for an hour).

---

## 3. Linux Permissions
### Permission Categories
- **User (u)**: Owner of the file/directory.
- **Group (g)**: Group assigned to the file/directory.
- **Others (o)**: Everyone else.

### Permission Types
- **Read (r)**: 4 (View file/directory).
- **Write (w)**: 2 (Edit/delete file/directory).
- **Execute (x)**: 1 (Run file or access directory).

### Permission String
- Example: `-rwxr-xr--`
  - First `-`: File type (`-` for file, `d` for directory).
  - `rwx`: User permissions (read, write, execute).
  - `r-x`: Group permissions (read, execute).
  - `r--`: Others permissions (read only).

### File Ownership with Primary and Secondary Groups
- **Default Ownership**:
  - When a user creates a file, it’s owned by the user and their primary group.
  - Example: If Alice’s primary group is `developers` (not `alice`):
    - `touch ~/Desktop/myfile.txt`
    - `ls -l ~/Desktop/myfile.txt` → `-rw-rw-r-- 1 alice developers 0 May 17 16:49 myfile.txt`
  - Secondary groups (e.g., `editors`) don’t get automatic ownership or permissions.
- **Grant Access to Secondary Group**:
  - Change group to secondary group: `sudo chown alice:editors ~/Desktop/myfile.txt`
    - Result: `-rw-rw-r-- 1 alice editors 0 May 17 16:49 myfile.txt`
  - Now `editors` group members can access the file based on group permissions.

### Change Permissions with `chmod`
- **Symbolic Mode**:
  - Syntax: `chmod [who][operator][permissions] file`
  - Example: `chmod u+w,g-r file.txt` (Adds write for user, removes read for group).
  - Recursive: `chmod -R 755 my_folder/` (Sets permissions for folder and contents).
- **Numeric Mode**:
  - Example: `chmod 644 file.txt` (User: rw, Group: r, Others: r).
  - Example: `chmod 755 script.sh` (User: rwx, Group: rx, Others: rx).

### Change Ownership with `chown`
- **Syntax**: `chown [user]:[group] file`
- **Examples**:
  - Change to primary group: `sudo chown alice:developers file.txt` (Sets group to Alice’s primary group `developers`).
  - Change to secondary group: `sudo chown alice:editors file.txt` (Sets group to Alice’s secondary group `editors`).
  - Change both: `sudo chown bob:developers file.txt` (Changes owner to Bob, group to `developers`).
  - Recursive: `chown -R alice:editors my_folder/` (Changes ownership of folder and contents).
- **Note**: `chown` doesn’t change a user’s primary/secondary group membership; it only changes the file’s group owner.

### Special Case: SGID on Directories
- **Set SGID**: `chmod g+s /path/to/directory`
- **Effect**: New files in the directory inherit the directory’s group, not the user’s primary group.
  - Example: Directory `/shared` belongs to `editors` with SGID: `drwxrwsr-x 2 root editors ...`
  - Alice (primary group `developers`) creates a file: `touch /shared/teamfile.txt`
  - Result: `-rw-rw-r-- 1 alice editors 0 May 17 16:49 teamfile.txt` (Group is `editors`, not `developers`).

---

## 4. Run Levels, Init, and Systemd
### Init
- **Purpose**: First process (PID 1), starts other processes, runs until shutdown.
- **Scripts Location**: `/etc/init.d/`

#### Manage Services (Init)
- **Start**: `service apache2 start`
- **Stop**: `service apache2 stop`
- **Restart**: `service apache2 restart`
- **Reload**: `service apache2 reload`
- **Status**: `service apache2 status`

#### Run-Levels (Init)
- **0**: Shutdown (`init 0`).
- **1**: Single-user mode (`init 1`).
- **2**: Multi-user, no networking (`init 2`).
- **3**: Multi-user with networking, CLI (`init 3`).
- **4**: User-definable (`init 4`).
- **5**: Multi-user with networking, GUI (`init 5`).
- **6**: Reboot (`init 6`).
- **Check Run-Level**: `runlevel` (e.g., `N 5` means current run-level is 5).

### Systemd (Modern Init Replacement)
#### Manage Services (Systemd)
- **List Services**: `systemctl list-units --type service --all`
- **Start**: `systemctl start apache2`
- **Stop**: `systemctl stop apache2`
- **Restart**: `systemctl restart apache2`
- **Reload**: `systemctl reload apache2`
- **Status**: `systemctl status apache2`
- **Enable at Boot**: `systemctl enable apache2`
- **Disable at Boot**: `systemctl disable apache2`
- **Check if Enabled**: `systemctl is-enabled apache2`

#### System Control
- **Halt**: `systemctl halt`
- **Power Off**: `systemctl poweroff`
- **Reboot**: `systemctl reboot`
- **Reload Config**: `systemctl daemon-reload` (After modifying service files).

#### Other Systemd Commands
- **Logs**: `journalctl` (View system logs).
- **Hostname**: `hostnamectl set-hostname mycomputer` (Sets hostname).
- **Time/Date**: `timedatectl set-time "2025-05-17 16:49:00"` (Sets time to 4:49 PM PKT).
- **Locale**: `localectl set-keymap us` (Sets keyboard layout).

---

## 5. Log Monitoring
- **View System Logs**: `cat /var/log/syslog`
  - Example: `cat /var/log/syslog` (Shows all system logs).
- **View Auth Logs**: `cat /var/log/auth.log`
  - Example: `cat /var/log/auth.log` (Shows authentication logs).
- **Search Logs**: `grep "word" filename`
  - Example: `grep "error" /var/log/syslog` (Finds lines with “error”).
- **View Live Logs**: `tail -f /var/log/syslog`
  - Example: `tail -f /var/log/syslog` (Shows real-time log updates).

---

## Example Scenarios
1. **User Management**:
   - Add user `alice` with primary group `developers`: `sudo adduser --gid 2000 alice`
   - Add `alice` to secondary group `editors`: `sudo usermod -aG editors alice`
   - Force password change: `sudo passwd -e alice`
   - Lock `alice`: `sudo passwd -l alice`

2. **File Ownership and Groups**:
   - Alice (primary: `developers`, secondary: `editors`) creates a file: `touch ~/Desktop/myfile.txt`
     - Result: `-rw-rw-r-- 1 alice developers 0 May 17 16:49 myfile.txt`
   - Share with `editors`: `sudo chown alice:editors ~/Desktop/myfile.txt`
     - Result: `-rw-rw-r-- 1 alice editors 0 May 17 16:49 myfile.txt`

3. **Permissions**:
   - Set `script.sh` to `rwxr-xr-x`: `chmod 755 script.sh`
   - Change ownership to `bob:editors`: `chown bob:editors script.sh`

4. **Cron Job**:
   - Schedule a backup daily at 2 AM: `0 2 * * * /bin/backup.sh` (Add via `crontab -e`).

5. **Systemd**:
   - Start and enable Apache: `systemctl start apache2 && systemctl enable apache2`
   - Check status: `systemctl status apache2`

6. **Logs**:
   - Find failed logins: `grep "Failed" /var/log/auth.log`
   - Monitor live logs: `tail -f /var/log/syslog`