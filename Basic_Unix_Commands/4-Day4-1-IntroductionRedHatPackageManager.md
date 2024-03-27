# Lab Exercise: Introduction to RPM (Red Hat Package Manager)

## Objective

There are several package management systems available in Linux, but most of them are based on either rpm (Red Hat Package Manager) or deb (Debian Packages). In this article, we will focus on rpm and its higher-level interface, yum.

## Table of Contents
1. **Introduction to RPM**
2. **RPM Query Commands**
    - `rpm -qa`
    - `rpm -q`
    - `rpm -qa | grep <package_name>`
3. **RPM Installation and Removal**
    - `rpm -ivh`
    - `rpm -ev`
4. **RPM Information Commands**
    - `rpm -ql`
    - `rpm -qc`
    - `rpm -qf`
    - `rpm -qi`
5. **RPM Package Dependencies**
    - `rpm -qR`
6. **RPM Package Scripts**
    - `rpm -q --scripts`
7. **Practical Examples**
8. **References**

## 1. Introduction to RPM
RPM (Red Hat Package Manager) is a package management system used in Red Hat-based Linux distributions. It allows users to install, upgrade, and manage software packages on their systems.

## 2. RPM Query Commands
### a. `rpm -qa`
- Lists all installed packages on the system.

### b. `rpm -q <package_name>`
- Displays information about a specific package.

### c. `rpm -qa | grep <package_name>`
- Searches for a specific package among installed packages.

## 3. RPM Installation and Removal
### a. `rpm -ivh <package.rpm>`
- Installs a package from an RPM file.

### b. `rpm -ev <package_name>`
- Removes an installed package.

## 4. RPM Information Commands
### a. `rpm -ql <package_name>`
- Lists files installed by a package.

### b. `rpm -qc <package_name>`
- Displays configuration files provided by a package.

### c. `rpm -qf <file_path>`
- Determines which package a file belongs to.

### d. `rpm -qi <package_name>`
- Displays detailed information about a package.

## 5. RPM Package Dependencies
### a. `rpm -qR <package_name>`
- Lists dependencies (other packages required) for a package.

## 6. RPM Package Scripts
### a. `rpm -q --scripts <package_name>`
- Shows pre-installation and post-installation scripts of a package.

## 7. Practical Examples
### Example 1: Installing Apache HTTP Server
Suppose you want to install the Apache HTTP Server package. You can use the following command:
```bash
rpm -ivh httpd-2.4.41-1.el7.x86_64.rpm
```

### Example 2: Checking Installed Packages
To list all installed packages, run:
```bash
rpm -qa
```

## 8. References
- [Red Hat Documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-yum)
- [Linux Man Pages](https://linux.die.net/man/8/rpm)