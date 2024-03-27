# Special Permissions

## Special Permissions: SUID and Password Management

### 1. **SUID (Set User ID) Permission**

- The SUID permission is a special permission in Linux that allows an executable file to be executed with the same permissions as the owner of the file.
- When an executable has the SUID bit set, it runs with the privileges of the file owner (usually root), regardless of who executes it.
- The SUID bit is represented by an `s` in the user execute permission field (where `x` would normally be).

### 2. **Example: `/usr/bin/passwd` Command**

- The `/usr/bin/passwd` command is responsible for changing user passwords.
- If you examine the binary executable file of `passwd`, you'll notice that it has the SUID bit set:
    ```bash
    ls -l /usr/bin/passwd
    ```
    Output:
    ```
    -rwsr-xr-x 1 root root 59640 Mar 22  2019 /usr/bin/passwd
    ```
- What does this mean?
    - Any user running the `passwd` command will execute it with the same permissions as the root user.
    - The `passwd` command needs to edit files like `/etc/passwd` and `/etc/shadow` to change passwords. These files are owned by root and can only be modified by root.
    - Thanks to the SUID flag, a regular user can modify these files (owned by root) and change their own password.
    - This is why you can use the `passwd` command to change your own password, even though the files it modifies are owned by root.

### 3. **Password Management in `/etc/shadow`**

- The `/etc/shadow` file contains information about system users' passwords.
- It is a text file owned by the root user and the group `shadow`, with restrictive permissions (`640`).
- Each line in `/etc/shadow` represents a user account and contains several fields:
    - Encrypted password (using various cryptographic hash algorithms).
    - Last password change date.
    - Minimum and maximum password age.
    - Warning period before password expiration.
    - Inactivity period.
    - Expiration date.
    - Unused field (reserved for future use).

### 4. **Why `/etc/shadow`?**

- Security: The `/etc/shadow` file is readable only by the root user, preventing unauthorized access.
- Separation of Sensitive Data: By keeping password-related information separate from the `/etc/passwd` file (which contains other user details), Linux enhances security.
- Hashed Passwords: The actual passwords are stored in hashed form in `/etc/shadow`, making it harder for attackers to retrieve them.

### 5. **Conclusion**

- The SUID concept is powerful but should be used cautiously to avoid security gaps.
- The `/usr/bin/passwd` command demonstrates how SUID allows regular users to manage their own passwords securely.
- Understanding special permissions like SUID is essential for maintaining a secure Linux system.

# Understanding SGID (Set Group ID) in Linux

## What is SGID?

The **SGID (Set Group ID)** permission is a special permission in Linux that affects both files and directories. Let's dive into the details:

1. **Files with SGID:**
    - When the SGID bit is set on an executable file, it allows the file to be executed with the permissions of the group that owns the file.
    - In other words, when a user runs an executable with SGID set, the process runs with the permissions of the members of the file's group, rather than the permissions of the person who launched it.
    - This is similar to the SUID (Set User ID) permission, but it applies to groups.

2. **Directories with SGID:**
    - When the SGID bit is set on a directory, any files or subdirectories created within that directory inherit the group ownership of the parent directory.
    - This ensures that files created in a shared directory maintain the same group ownership as the parent directory.

## Example: `/usr/bin/wall` Command

- The `/usr/bin/wall` command is used to send messages to all users' terminals.
- Let's examine its permissions and understand how SGID works:

1. Check the permissions of `/usr/bin/wall`:
    ```bash
    ls -l /usr/bin/wall
    ```
    Output (excerpt):
    ```
    -rwxr-sr-x 1 root tty 23880 Mar 22  2019 /usr/bin/wall
    ```
    - The `s` in the group execute permission indicates SGID.
    - The group is set to `tty`.

2. What does this mean?
    - When a user runs `wall`, it executes with the permissions of the `tty` group.
    - This allows users to send messages to all terminals, even if they don't have direct write access to those terminals.

## Conclusion

SGID is a powerful permission that ensures proper group ownership for files and directories. It plays a crucial role in managing shared resources and maintaining security. 🐧🔒

# Understanding the Sticky Bit in Linux

The **sticky bit** is a special permission that plays a crucial role in managing file deletion within directories. Let's explore what it is and how it works:

## 1. Sticky Bit Explanation

- The sticky bit is a permission bit that can be assigned to both files and directories on Unix-like systems.
- When set on a directory, it restricts file deletion or renaming within that directory.
- Only the owner of a file (or the root user) can remove or modify files within a sticky bit directory.

## 2. Example: `/tmp` Directory

- The `/tmp` directory is a common example of using the sticky bit.
- Permissions for `/tmp`:
    ```
    drwxrwxrwt. 14 root root 4096 Mar 25 18:00 /tmp
    ```
    - The `t` in the others' execute permission indicates the sticky bit.
    - What does this mean?
        - Any user can create files in `/tmp`.
        - Only the owner (or root) can delete or modify their own files within `/tmp`.

## Practical Exercise

1. Create a file in `/tmp`:
    ```bash
    touch /tmp/myfile.txt
    ```

2. Try deleting the file as a regular user( other than the owner ): 
    ```bash
    rm /tmp/myfile.txt
    ```
    You'll receive a "Permission denied" error.

3. Delete the file as the owner (root):
    ```bash
    sudo rm /tmp/myfile.txt
    ```

The sticky bit ensures that shared directories like `/tmp` remain organized and secure. 🐧🔒
