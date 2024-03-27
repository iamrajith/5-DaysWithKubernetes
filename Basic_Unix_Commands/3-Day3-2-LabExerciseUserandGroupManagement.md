
## Lab Exercise: User and Group Management

1. **Add a User with `useradd` Command**:
    - We'll create a new user named "johndoe" using the `useradd` command:
        ```bash
        sudo useradd johndoe
        ```
    - This command will add an entry for "johndoe" in the `/etc/passwd` file.

2. **Check the Entries in Files**:
    - Verify the entries in the following files:
        - `/etc/passwd`: Contains user account information.
        - `/etc/shadow`: Contains encrypted passwords and other user-specific settings.
        - `/etc/group`: Contains group information.
    - You can use commands like `cat`, `grep`, or `less` to view the contents of these files.

3. **Add User with Common Options**:
    - Let's create a new user named "alice" with specific options:
        ```bash
        sudo useradd -g developers -G admins -s /bin/bash -d /home/alice -u 1001 alice
        ```
    - This command:
        - Sets the primary group to "developers."
        - Adds "alice" to the "admins" group.
        - Sets the login shell to `/bin/bash`.
        - Specifies the home directory as `/home/alice`.
        - Assigns a custom user ID (UID) of 1001.

4. **Check Shadow Entry Before and After Password**:
    - Before setting a password for "alice," check the `/etc/shadow` entry for her.
    - Set a password for "alice" using the `passwd` command:
        ```bash
        sudo passwd alice
        ```
    - After setting the password, verify the updated `/etc/shadow` entry.

5. **Verify Home Directory and `/etc/skel`**:
    - Check the contents of "alice"'s home directory (`/home/alice`).
    - Also, explore the contents of the `/etc/skel` directory, which serves as a template for new user home directories.

6. **Use `usermod` to Change Group and Login Shell**:
    - Change "alice"'s primary group to "devteam":
        ```bash
        sudo usermod -g devteam alice
        ```
    - Change her login shell to `/bin/zsh`:
        ```bash
        sudo usermod -s /bin/zsh alice
        ```

7. **Create a New Group and Add It to an Existing User as a Secondary Group**:
    - Create a new group named "designers":
        ```bash
        sudo groupadd designers
        ```
    - Add "alice" to the "designers" group:
        ```bash
        sudo usermod -a -G designers alice
        ```

8. **Remove the User**:
    - To remove "alice" and her home directory:
        ```bash
        sudo userdel -r alice
        ```

9. **Use `finger` Command**:
    - The `finger` command provides information about users. For example:
        ```bash
        finger alice
        ```

10. **Lab Exercise**:
    - Here's a lab exercise for you:
        - Create a new user named "bob."
        - Set his password.
        - Verify the entries in `/etc/passwd`, `/etc/shadow`, and `/etc/group`.
        - Explore Bob's home directory.
        - Change Bob's primary group to "developers."
        - Add him to the "designers" group.
        - Remove Bob from the system.
