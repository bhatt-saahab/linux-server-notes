# GRC (Generic Colouriser)

GRC (Generic Colouriser) is a tool that colorizes log files and command outputs using regular expressions for easier readability. It provides shell aliases to automatically colorize commands like `ls`, `ping`, `netstat`, etc.

**GitHub Repository**: [garabik/grc](https://github.com/garabik/grc)

## 

### Download and Setup

1. Download the GRC source code archive:
    
    ```
    wget https://github.com/garabik/grc/archive/refs/tags/v1.13.tar.gz
    
    ```
    
    echo "GRC_ALIASES=true" >> ~/.bashrc
    
2. Unpack the downloaded tarball:
    
    ```
    tar -xvf v1.13.tar.gz
    
    ```
    
3. Move the extracted folder to `/opt`:
    
    ```
    mv -v grc-1.13 /opt
    
    #We move extracted folders to /opt to keep third-party apps organized, isolated from system files, and available for all users.
    ```
    
4. Navigate to the installation directory:
    
    ```
    cd /opt/grc-1.13
    
    ```
    
5. Run the installation script:
    
    ```
    ./install.sh
    
    ```
    

### Configuration for Automatic Aliases

(Automatic alias configuration means saving aliases in shell config files so they load every time you open a terminal.) 

- Edit the configuration file:
    
    ```
    vim /etc/default/grc
    
    ```
    
- Change or add the following line to enable automatic aliases:
    
    ```
    echo "GRC_ALIASES=true" >> /etc/default/grc
    
    ```
    
- Example configuration file content:
    
    ```
    # Add colourifying aliases for sh and bash shells
    # true or false
    #
    # Since this is rather invasive, it defaults to false
    # Changing it makes your shell source /etc/profile.d/grc
    GRC_ALIASES=true
    
    ```
    

### Shell Integration

1. Copy the provided initialization script to `/etc`:
    
    ```
    cp -v /opt/grc-1.13/grc.sh /etc/grc.sh
    
    ```
    
2. Check permissions and content:
    
    ```
    ls -lh /etc/grc.sh
    
    ```
    
    ```
    cat /etc/grc.sh
    
    ```
    
3. For Bash, append the following lines to your `~/.bashrc` file:
    
    ```
    echo "GRC_ALIASES=true" >> ~/.bashrc
    
    ```
    
    ```
    echo '[[ -s "/etc/profile.d/grc.sh" ]] && source /etc/grc.sh' >> ~/.bashrc
    
    ```
    
4. Reload your Bash shell:
    
    ```
    source ~/.bashrc
    
    ```
    
5. For Zsh, append the following line to your `~/.zshrc`:
    
    ```
    echo '[[ -s "/etc/grc.zsh" ]] && source /etc/grc.zsh' >> ~/.zshrc
    
    ```
    
6. Reload your Zsh shell:
    
    ```
    source ~/.zshrc
    
    ```
    

### Testing GRC

- Run a command to verify colorized output:
    
    ```
    grc ping google.com
    
    ```
    

# Debian Package Manager (dpkg)

The Debian Package Manager (`dpkg`) is the core low-level tool for handling `.deb` packages in Debian-based systems (e.g., Ubuntu, Kali Linux, Raspberry Pi OS). It works directly with packages without automatically resolving dependencies, so tools like `apt` or `apt-get` are often used alongside it.

## System Information Commands

1. View distribution release information:
    
    ```
    cat /etc/os-release
    
    ```
    
2. Print system architecture:
    
    ```
    dpkg --print-architecture
    
    ```
    

## Basic dpkg Commands

### Installing and Removing

1. Install a `.deb` package:
    
    ```
    dpkg -i package_name.deb
    
    ```
    
2. Fix missing dependencies:
    
    ```
    apt-get install -f
    
    ```
    
3. Remove a package (keep config files):
    
    ```
    dpkg -r package_name
    
    ```
    
4. Remove a package and its config files (purge):
    
    ```
    dpkg --purge package_name
    
    ```
    

### Querying Packages

| Command | Description |
| --- | --- |
| `dpkg -l` | List all installed packages |
| `dpkg -l | wc -l` |
| `dpkg -l | grep package_name` |
| `dpkg -s package_name` | Check if a package is installed |
| `dpkg -p package_name` | Get details of an installed package |
| `dpkg -L package_name` | List files installed by a package |
| `dpkg -S /path/to/file` | Find which package owns a file |
| `dpkg -S /usr/bin/zip` | Example: Find package owning `/usr/bin/zip` |
| `dpkg-query --search /path/to/file` | Alternative: Search for package owning a file |
| `dpkg-query --search /usr/bin/zip` | Example: Alternative search for `/usr/bin/zip` |

**Copy and Run Commands**:

- List all installed packages:
    
    ```
    dpkg -l
    
    ```
    
- Count total installed packages:
    
    ```
    dpkg -l | wc -l
    
    ```
    
- Search for a package in the list:
    
    ```
    dpkg -l | grep package_name
    
    ```
    
- Check if a package is installed:
    
    ```
    dpkg -s package_name
    
    ```
    
- Get details of an installed package:
    
    ```
    dpkg -p package_name
    
    ```
    
- List files installed by a package:
    
    ```
    dpkg -L package_name
    
    ```
    
- Find which package owns a file:
    
    ```
    dpkg -S /path/to/file
    
    ```
    
- Example: Find package owning `/usr/bin/zip`:
    
    ```
    dpkg -S /usr/bin/zip
    
    ```
    
- Alternative: Search for package owning a file:
    
    ```
    dpkg-query --search /path/to/file
    
    ```
    
- Example: Alternative search for `/usr/bin/zip`:
    
    ```
    dpkg-query --search /usr/bin/zip
    
    ```
    

### Working with `.deb` Files

1. Download a `.deb` file:
    
    ```
    wget <package_url>.deb
    
    ```
    
2. Show package contents without installing:
    
    ```
    dpkg --contents package.deb
    
    ```
    
    ```
    dpkg-deb -c package.deb
    
    ```
    
3. Extract contents of a package:
    
    ```
    dpkg -x package.deb /output_directory/
    
    ```
    
    ```
    dpkg -x nmap_7.95+dfsg-3_amd64.deb /tmp/
    
    ```
    
    ```
    dpkg-deb -xv package.deb /output_directory/
    
    ```
    
    ```
    dpkg-deb -xv nmap_7.95+dfsg-3_amd64.deb /tmp/
    
    ```
    

### Fixing Issues

1. Reconfigure an installed package:
    
    ```
    dpkg-reconfigure package_name
    
    ```
    
2. Repair a broken installation:
    
    ```
    dpkg --configure -a
    
    ```
    

### Useful Tips for Package Discovery

1. Find the binary path:
    
    ```
    which command_name
    
    ```
    
    ```
    which zsh
    
    ```
    
2. Locate which package owns a binary:
    
    ```
    dpkg -S /usr/bin/zsh
    
    ```
    
3. Check package details:
    
    ```
    dpkg -s zsh
    
    ```
    
4. Locate all related files:
    
    ```
    whereis command_name
    
    ```
