## YUM Package Manager

Yellowdog Updater Modified (yum)  is a much easier package manager which works on rpm RPM-based system. The two attractive features of 'yum' when compared with 'rpm' is it resolves dependency automatically. It also can automatically perform the system update.

### 1. Overview
- **YUM** is also known as the **Red Hat package manager**.
- It provides the following functionalities:
    - Fetching information about available packages.
    - Installing and uninstalling packages.
    - Updating individual packages or the entire system to the latest versions.
    - Automatically resolving dependencies during package installation, removal, or updates.

### 2. Key Features
- **Dependency Resolution**: YUM ensures that all required dependencies are met when installing or updating packages.
- **Repositories**: YUM can be configured with additional repositories (sources of packages) beyond the default ones.
- **Plug-ins**: YUM supports various plug-ins to enhance its capabilities.
- **Fast and Efficient**: YUM performs tasks quickly, making it ideal for system administrators.

### 3. Common YUM Commands
Let's explore some common YUM commands:

#### a. Checking for Updates
You can verify available updates for installed packages using the following command:
```bash
yum check-update
```
This command lists package names, their versions, CPU architecture, and the repository where each package is available.

#### b. Installing and Updating Packages
- To install a package:
  ```bash
  yum install package-name
  ```
- To update a package:
  ```bash
  yum update package-name
  ```

#### c. Working with Transaction History
- Listing recent transactions:
  ```bash
  yum history list
  ```
- Examining a specific transaction:
  ```bash
  yum history info transaction-ID
  ```
- Reverting a transaction:
  ```bash
  yum history undo transaction-ID
  ```

#### d. Configuring YUM and Repositories
- Edit the main YUM configuration file:
  ```bash
  vi /etc/yum.conf
  ```
- Add or enable repositories:
  ```bash
  yum-config-manager --add-repo repository-URL
  ```
#### e. `yum search <package-name>`
- Searches for packages matching the specified name or keywords.
- Example:
  ```bash
  yum search htop
  ```

#### f. `yum provides <file>`
- Determines which package provides a specific file.
- Example:
  ```bash
  yum provides /usr/bin/htop
  ```

#### g. `yum repolist`
- Lists enabled repositories along with their status.
- Example:
  ```bash
  yum repolist
  ```

#### h. `yum clean all`
- Cleans temporary files and cached data.
- Removes metadata and improves system performance.
- Example:
  ```bash
  yum clean all
  ```


### 4. Lab Exercise
Let's create a simple lab exercise to practice using YUM:

1. **Objective**: Install the `htop` package.
2. **Steps**:
    - Check if `htop` is already installed:
      ```bash
      yum list installed htop
      ```
    - If not installed, install it:
      ```bash
      yum install htop
      ```
    - Verify the installation:
      ```bash
      htop
      ```
