# Basic Unix Command Lab

This series is for people who love to enter the world of Linux. With the introduction of microservices, a lot of people started to show interest to learn Linux.

Here we are not covering any advanced topics. It is for the people who are stepping into the world of Linux. Especially for those who wanted to learn Linux as a stepping stone for Kubernetes.


---
1. **`tty`**: Reveals the current terminal.
2. **`whoami`**: Displays the currently logged-in user.
3. **`who am i`**: Shows session information for the user.
4. **`su`**: Switches the user and changes the effective user ID and group ID.
5. **`who`**: Lists information about all user sessions on the system.
6. **`w`**: Displays session information along with server load average.
7. **`last`**: Shows historical data of logged-in user sessions.
8. **`which`**: Reveals the full path of a given command.
9. **`uname`**:
   - `uname -n`: Displays the node name (hostname).
   - `uname -r`: Shows the current running kernel version.
   - `uname -a`: Provides detailed system information.
10. **`echo`**: Prints a string to the standard output.

Example: `echo "Hello, welcome to the world of Linux."`

---

Welcome to the Basic Unix Command Lab! In this lab, you'll learn essential Unix commands that will help you navigate your system, manage files and directories, and perform various tasks. Let's dive in!


## 1. Navigating the Filesystem

### `ls` - List Files and Directories
Use the `ls` command to display the contents of the current directory:
```bash
ls
```

### `pwd` - Show Current Working Directory
Check your current working directory:
```bash
pwd
```

### `cd` - Change Directory
Navigate between different folders:
```bash
cd /path/to/directory
```

## 2. File Operations

### `mkdir` - Create a Directory
Create a new directory:
```bash
mkdir my_directory
```

### `cp` - Copy Files
Move files from one directory to another:
```bash
cp file.txt /path/to/destination/
```

### `mv` - Rename or Move Files
Rename or move files:
```bash
mv old_name.txt new_name.txt
```

### `rm` - Delete Files
Remove files or directories (use with caution):
```bash
rm file.txt
```

## 3. Text File Operations

### `cat` - Display File Contents
View the contents of a text file:
```bash
cat file.txt
```

### `grep` - Search for Patterns
Search for specific strings in a file:
```bash
grep "pattern" file.txt
```

### `head` and `tail` - Display First/Last Lines
Show the first or last lines of a text file:
```bash
head file.txt
tail file.txt
```

## 4. System Information

### `uname` - Get Basic OS Information
Print information about the operating system:
```bash
uname -a
```

### `uptime` - System Uptime
Find out how long the system has been up:
```bash
uptime
```

## 5. Process Management

### `ps` - List Processes
Display running processes:
```bash
ps aux
```

### `kill` - Terminate a Process
Stop a process (use with caution):
```bash
kill PID
```

## 6. Networking

### `ifconfig` (or `ip`) - Show IP Addresses
View network interface information:
```bash
ifconfig
```

### `ping` - Check Host Reachability
Test if a remote host is reachable:
```bash
ping google.com
```

Remember to explore these commands further and practice using them. Feel free to experiment and learn more about Unix! 🚀


Feel free to customize this lab by adding more commands or exercises. If you have any questions or need assistance, feel free to ask!