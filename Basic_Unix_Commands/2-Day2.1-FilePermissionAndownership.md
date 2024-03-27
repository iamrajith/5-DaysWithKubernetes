# Linux File Ownership and Permissions 


## File Type in Unix 

The first character in a Unix file permission string indicates the **file type**. It provides essential information about the nature of the file. Here are the common file types represented by the first character:

1. **Ordinary File (Regular File)**:
    - Ordinary files contain data, text, or program instructions.
    - Examples: Text files, images, compiled programs.
    - Output:
        ```
        -rw-r--r-- 1 user group 12345 Mar 25 10:00 myfile.txt
        ```
    - Explanation:
        - `-rw-r--r--`: The first `-` indicates an ordinary file.
        - `myfile.txt`: The file name.
        - Permissions (`rw-r--r--`): Read and write permissions for the owner, read-only permissions for the group and others.

2. **Directory**:
    - Directories are used to organize groups of files.
    - They can contain both files and other directories.
    - Output:
        ```
        drwxr-xr-x 2 user group 4096 Mar 25 10:05 mydir
        ```
    - Explanation:
        - `d`: Indicates a directory.
        - `mydir`: The directory name.
        - Permissions (`drwxr-xr-x`): Read, write, and execute permissions for the owner, read and execute permissions for the group and others.

3. **Character Device File**:
    - Character devices communicate with hardware devices character by character (e.g., terminals, serial ports).
    - Output:
        ```
        crw-rw---- 1 root tty 4, 1 Mar 25 10:10 tty1
        ```
    - Explanation:
        - `c`: Indicates a character device.
        - `tty1`: The device name.
        - Permissions (`crw-rw----`): Read and write permissions for the owner and group.

4. **Block Device File**:
    - Block devices communicate with hardware devices in fixed-size blocks (e.g., hard drives, USB drives).
    - Output:
        ```
        brw-rw---- 1 root disk 8, 0 Mar 25 10:15 sda
        ```
    - Explanation:
        - `b`: Indicates a block device.
        - `sda`: The device name.
        - Permissions (`brw-rw----`): Read and write permissions for the owner and group.


5. **Symbolic Links (Symlinks)**:
    - Symlinks are pointers to other files or directories.
    - They allow flexible organization without physically moving files.
    - Example: `mylink -> myfile`.

    - Output:
        ```
        lrwxrwxrwx 1 user group  4 Mar 25 10:00 my_link.txt -> my_file.txt
        ```
    - Explanation:
        - `l`: Indicates a symbolic link.
        - `my_link.txt`: The symlink name.
        - `my_file.txt`: The file it points to.

## 2. **File Permissions**

### Explanation
File ownership in Linux is crucial for access control. Each file or directory has an owner (user) and a group associated with it. Let's explore this further.

### Practical Exercise
1. Create a test file:
   ```bash
   touch mytestfile.txt
   ```
2. Check its ownership details:
   ```bash
   ls -l mytestfile.txt
   ```
   The output will show something like:
   ```
   -rw-r--r-- 1 username groupname 0 Mar 25 18:00 mytestfile.txt
   ```
   Here:
   - `username` is the owner.
   - `groupname` is the group.
   - The first `-` indicates it's a regular file.

## 3. **File Permissions**

### Explanation
File permissions control who can read, write, or execute a file. The three basic permissions are read (r), write (w), and execute (x).

### Practical Exercise

#### Exercise 1: Remove Write Permission
1. Create a test file:
   ```bash
   touch mytestfile.txt
   ```
2. Remove write permission for yourself:
   ```bash
   chmod -w mytestfile.txt
   ```
3. Try to modify the file:
   ```bash
   echo "Hello, World!" >> mytestfile.txt
   ```
   You'll receive an error because you no longer have write permission.
4. Add write permission back:
   ```bash
   chmod +w mytestfile.txt
   ```
5. Modify the file again:
   ```bash
   echo "Hello again!" >> mytestfile.txt
   ```
6. View the file content:
   ```bash
   cat mytestfile.txt
   ```

## 4. **Putting It All Together**

After changing permissions, let's check the updated permissions using `ls -l`.

## 5. **Changing Ownership**

Remember that changing ownership requires root privileges. Use `sudo` for this operation.

1. View ownership before:
   ```bash
   ls -l mytestfile.txt
   ```
2. Change ownership:
   ```bash
   sudo chown newuser:newgroup mytestfile.txt
   ```
3. View ownership after:
   ```bash
   ls -l mytestfile.txt
   ```

## 7. **Understanding Umask**

### Explanation
- Umask controls default permissions for newly created files and directories.
- Default umask value: 0022 (subtract from 666 for files, 777 for directories).

### Practical Example
1. Change the umask:
   ```bash
   umask 0027
   ```
2. Create a file and directory:
   ```bash
   touch myfile.txt
   mkdir mydir
   ```
3. Observe the permissions using `ls -l`.

## 9. **Calculating Umask**

- Umask value calculation:
  - Subtract umask from default permissions (666 for files, 777 for directories).

### Example 1: `666 - 002`
- Default permissions for files: 666
- Umask: 002
- Calculated permissions: 664 (rw-rw-r--)

### Example 2: `777 - 002`
- Default permissions for directories: 777
- Umask: 002
- Calculated permissions: 775 (rwxrwxr-x)



## 12. Let's explore the `stat` Command

The `stat` command provides detailed information about files. It allows you to retrieve various attributes of a file, including permissions, timestamps, and file type.

### 1. **Show Numeric Permissions**
- Display the numeric permissions (e.g., 644 for files, 755 for directories):
    ```bash
    stat -c %a filename
    ```
    **Example Output:**
    ```
    644
    ```

### 2. **Show Access Time**
- View only the access timestamp:
    ```bash
    stat -c %x filename
    ```
    **Example Output:**
    ```
    2023-03-25 18:30:00.000000000 +0000
    ```

### 3. **Show Modification Time**
- See only the modification timestamp:
    ```bash
    stat -c %y filename
    ```
    **Example Output:**
    ```
    2023-03-25 18:35:00.000000000 +0000
    ```

### 4. **Show Change Time**
- Check only the change timestamp:
    ```bash
    stat -c %z filename
    ```
    **Example Output:**
    ```
    2023-03-25 18:40:00.000000000 +0000
    ```

### 5. **Show File Type**
- Determine the file type (e.g., "regular file," "directory," etc.):
    ```bash
    stat -c %F filename
    ```
    **Example Output:**
    ```
    regular file
    ```

### Advantages of Using `stat`:
- Provides granular information about files.
- Useful for scripting and automation.
- Helps track file changes and access patterns.


Explore these concepts, practice exercises, and master Linux permissions! 🐧🔒 Don't miss the next session whcih talk about the special permission.
