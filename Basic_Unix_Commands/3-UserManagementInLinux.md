## User Management in Linux

User management in Linux involves creating, modifying, and deleting user accounts. It ensures that users have appropriate access levels and prevents unauthorized access to system resources. Here are the key aspects:

1. **User Types**:
    - **Regular Users**: Created by administrators for day-to-day tasks.
    - **System Users**: Created by the system or applications to run services.

2. **User Permissions**:
    - **Standard Users**: Regular users with limited permissions.
    - **Administrative Users**: Trusted users with elevated permissions.

3. **Files for User Management**:
    - `/etc/passwd`: Contains user account information.
    - `/etc/shadow`: Stores encrypted passwords and related details.
    - `/etc/group`: Defines user groups.

## Fields in `/etc/passwd` File

The `/etc/passwd` file contains one entry per line, representing a user account. Each line has seven comma-separated fields:

1. **Username**: The login name used to access the system.
2. **Encrypted Password**: Usually stored in `/etc/shadow`. If empty, no password is needed.
3. **User ID (UID)**: A unique numeric identifier for the user.
4. **Group ID (GID)**: The primary group ID for the user.
5. **User ID Info (GECOS)**: Additional user information (full name, room number, etc.).
6. **Home Directory**: The user's home directory path.
7. **Login Shell**: The absolute path to the user's login shell (e.g., `/bin/bash`).

## Fields in `/etc/shadow` File

The `/etc/shadow` file contains user login information, including encrypted passwords. Each line has nine comma-separated fields:

1. **Username**: The user account name.
2. **Encrypted Password**: Stored using a hash algorithm (e.g., MD5, SHA-512).
3. **Last Password Change**: Date when the password was last changed.
4. **Min Days**: Minimum days before password change is allowed.
5. **Max Days**: Maximum days before password expiration.
6. **Warn**: Days before password expiration to show a warning.
7. **Inactive**: Days after password expiration before disabling the account.
8. **Expire**: Date when the account is disabled.
9. **Reserved**: Reserved for future use.

## Creating a New User Account

1. Use the `useradd` command to create a new user (e.g., `sudo useradd jane`).
2. Set the user's password using `passwd` (e.g., `sudo passwd jane`).
3. The user's home directory is created (usually under `/home`).
4. Entries are added to `/etc/passwd`, `/etc/shadow`, `/etc/group`, and `/etc/gshadow`.
5. Files from `/etc/skel` (skeleton directory) are copied to the user's home directory.

## The Unix user login process 

1. **User Authentication**:
    - The user enters their username (login) at the system prompt.
    - The system checks the entered username against the user database (usually stored in `/etc/passwd`).
    - If the username exists, the system proceeds to the next step.

2. **Password Validation**:
    - The system prompts the user for their password.
    - The entered password is hashed and compared to the stored hash in the `/etc/shadow` file (or equivalent).
    - If the password matches, authentication is successful.

3. **Session Initialization**:
    - A new shell process is created for the user.
    - The shell reads configuration files in a specific order:
        - `/etc/profile`: System-wide profile settings.
        - `~/.bash_profile`, `~/.bash_login`, or `~/.profile`: User-specific profile settings (only the first existing file is read).
        - `~/.bashrc`: User-specific shell settings (for non-login shells).
    - Environment variables are set based on these files.

4. **Home Directory Setup**:
    - The user's home directory is determined from the `/etc/passwd` entry.
    - Files from the `/etc/skel` directory (skeleton directory) are copied to the user's home directory.
    - These files include default configuration files, such as `.bashrc`, `.bash_profile`, etc.

5. **User Lock and Restrictions**:
    - The system checks if the user account is locked (e.g., due to failed login attempts or administrative action).
    - If the account is locked, the user is denied access.

6. **Login Shell Execution**:
    - The shell (e.g., Bash) is executed with the user's environment.
    - The user is presented with a command prompt (PS1).

7. **User Interaction**:
    - The user interacts with the shell, runs commands, and performs tasks.
    - The shell manages input/output and executes requested programs.

8. **Logout**:
    - When the user logs out, the shell process exits.
    - Any cleanup tasks (e.g., closing files, terminating processes) are performed.
    - The user is returned to the system prompt.


## User Management Commands

1. **`useradd`**:
    - Creates new user accounts.
    - Syntax: `useradd [options] username`
    - Common options:
        - `-b, --base-dir BASE_DIR`: Specifies the default base directory for user home directories.
        - `-c, --comment COMMENT`: Sets the GECOS (user information) comment.
        - `-d, --home HOME_DIR`: Specifies the user's home directory.
        - `-e, --expiredate EXPIRE_DATE`: Sets the account expiry date.
        - `-f, --inactive INACTIVE`: Specifies the number of days after password expiration until the account is disabled.
        - `-g, --gid GROUP`: Sets the primary group for the user.
        - `-G, --groups GROUP1[,GROUP2,...]`: Adds the user to supplementary groups.
        - `-m, --create-home`: Creates the user's home directory.
        - `-s, --shell SHELL`: Sets the user's default shell.
    - Example:
        ```bash
        sudo useradd -c "John Doe" -d /home/johndoe -m -s /bin/bash johndoe
        ```

2. **`usermod`**:
    - Modifies existing user accounts.
    - Common options:
        - `-a, --append`: Adds the user to supplementary groups.
        - `-c, --comment COMMENT`: Changes the GECOS comment.
        - `-d, --home HOME_DIR`: Changes the user's home directory.
        - `-s, --shell SHELL`: Changes the user's default shell.
    - Example:
        ```bash
        sudo usermod -a -G developers -s /bin/zsh johndoe
        ```

    - Other most common Options

    -`-g, --gid GROUP`**:
    - The `-g` option allows you to change the primary group for a user.
    - By default, a user's primary group is the one specified in `/etc/passwd`.
    - Example:
        ```bash
        sudo usermod -g developers johndoe
        ```
    - This changes the primary group of the user "johndoe" to the "developers" group.

    - `-G, --groups GROUP1[,GROUP2,...]`**:
    - The `-G` option adds the user to supplementary groups.
    - Specify multiple groups separated by commas.
    - Existing group memberships are preserved.
    - Example:
        ```bash
        sudo usermod -G developers,admins johndoe
        ```
    - This adds the user "johndoe" to both the "developers" and "admins" groups.



3. **`userdel`**:

1. **Delete User Without Home Directory**:
    - If you want to delete a user without removing their home directory, use the following command:
        ```bash
        sudo userdel username
        ```
    - This removes the user account but leaves the home directory intact.

2. **Delete User With Home Directory**:
    - To delete a user along with their home directory, use the `-r` option:
        ```bash
        sudo userdel -r username
        ```
    - This removes the user's account and their home directory.

4. **`groupadd`**:
    - Creates new groups.
    - Syntax: `groupadd [options] groupname`
    - Common options:
        - `-f, --force`: Silently aborts if the group already exists.
        - `-g, --gid GID`: Assigns a custom group ID to the new group.
    - Example:
        ```bash
        sudo groupadd -g 1001 developers
        ```
