**Covers the installation and configuration of PHP, MySQL, phpMyAdmin, WordPress, and Apache Virtual Hosts.**

- **Each step includes an explanation before executing the corresponding command.**

## PHP Installation and Configuration

### Install Required Repositories

- **Before installing PHP, we must enable the Extra Packages for Enterprise Linux (EPEL) repository and the Remi repository, which provides updated PHP versions.**
- **Run the following command to install EPEL and YUM utilities** (corrected typo: `yun-utils` → `yum-utils`):
    
    ```
    yum install epel-release yum-utils
    
    ```
    
- **After installation, verify the available repositories**:
    
    ```
    yum repolist all
    
    ```
    
- **Visit the official Remi repository to get the appropriate repository package**:
    - **Remi Repository**: https://rpms.remirepo.net/ (official site for reference; no command needed).
- **Now, install the Remi repository for Enterprise Linux 9**:
    
    ```
    yum install <http://rpms.remirepo.net/enterprise/remi-release-9.rpm>
    
    ```
    
- **Verify that the repository has been added by listing all available repositories again**:
    
    ```
    yum repolist all
    
    ```
    

> Notes:
> 
> - All data preserved and corrected for accuracy (e.g., command typos fixed).
> - Commands are in separate, copy-paste-ready code blocks for easy execution.
> - This setup is for Enterprise Linux 9 (RHEL 9/CentOS Stream 9); `yum` is an alias for `dnf`.
> - No data missed; structure follows the provided sequence.
> - Subsequent sections (MySQL, etc.) not included as they were not in the input data.
