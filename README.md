# WSL RStudio Setup Guide

This guide provides step-by-step instructions for setting up Ubuntu and RStudio Server using Windows Subsystem for Linux (WSL) on Windows.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Install WSL](#step-1-install-wsl)
- [Step 2: Install Ubuntu on WSL](#step-2-install-ubuntu-on-wsl)
- [Step 3: Update Ubuntu System](#step-3-update-ubuntu-system)
- [Step 4: Install R](#step-4-install-r)
- [Step 5: Install RStudio Server](#step-5-install-rstudio-server)
- [Step 6: Start RStudio Server](#step-6-start-rstudio-server)
- [Step 7: Access RStudio in Browser](#step-7-access-rstudio-in-browser)
- [Troubleshooting](#troubleshooting)
- [Additional Tips](#additional-tips)

## Prerequisites

- Windows 10 version 2004 or higher (Build 19041 or higher), or Windows 11
- Administrator access on your Windows machine
- Internet connection

## Step 1: Install WSL

1. Open PowerShell or Windows Command Prompt as Administrator:
   - Right-click on the Start button
   - Select "Windows PowerShell (Admin)" or "Terminal (Admin)"

2. Run the following command to install WSL:
   ```powershell
   wsl --install
   ```

3. Restart your computer when prompted.

**Note:** The `wsl --install` command enables the required features and installs the Ubuntu distribution by default.

### Alternative Method (Manual Installation)

If you prefer to manually enable WSL:

1. Open PowerShell as Administrator and run:
   ```powershell
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```

2. Restart your computer.

3. Download and install the WSL2 Linux kernel update package from Microsoft's website.

4. Set WSL 2 as the default version:
   ```powershell
   wsl --set-default-version 2
   ```

## Step 2: Install Ubuntu on WSL

If Ubuntu wasn't installed automatically with `wsl --install`, you can install it manually:

### Option A: Using Microsoft Store

1. Open the Microsoft Store
2. Search for "Ubuntu" (or a specific version like "Ubuntu 22.04 LTS")
3. Click "Get" or "Install"
4. Launch Ubuntu from the Start menu

### Option B: Using Command Line

```powershell
wsl --install -d Ubuntu
```

### First Time Setup

When you launch Ubuntu for the first time:

1. Wait for the installation to complete
2. Create a new UNIX username (lowercase, no spaces)
3. Create a password (you won't see characters as you type)
4. Confirm your password

**Important:** Remember this username and password - you'll need them for sudo commands.

## Step 3: Update Ubuntu System

Once inside your Ubuntu terminal:

1. Update the package list:
   ```bash
   sudo apt update
   ```

2. Upgrade installed packages:
   ```bash
   sudo apt upgrade -y
   ```

## Step 4: Install R

1. Install dependencies and add the CRAN repository:
   ```bash
   sudo apt install -y software-properties-common dirmngr
   
   # Download and add CRAN GPG key
   wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | sudo tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
   
   # Verify the key fingerprint (optional but recommended)
   gpg --show-keys /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
   
   # Add CRAN repository
   sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
   ```

2. Install R base:
   ```bash
   sudo apt update
   sudo apt install -y r-base r-base-dev
   ```

3. Verify R installation:
   ```bash
   R --version
   ```

## Step 5: Install RStudio Server

1. Install required dependencies:
   ```bash
   sudo apt install -y gdebi-core
   ```

2. Download RStudio Server:
   
   First, check https://posit.co/download/rstudio-server/ for the latest version.
   
   Then download it (replace the version number with the current one):
   ```bash
   # Example for Ubuntu 22.04 (Jammy) - adjust version as needed
   VERSION="2023.12.1-402"
   wget https://download2.rstudio.org/server/jammy/amd64/rstudio-server-${VERSION}-amd64.deb
   ```
   
   **Optional but recommended**: Verify the download using SHA256 checksum from Posit's website:
   ```bash
   # Get the expected checksum from https://posit.co/download/rstudio-server/
   # Compare with: sha256sum rstudio-server-${VERSION}-amd64.deb
   ```

3. Install RStudio Server:
   ```bash
   sudo gdebi rstudio-server-${VERSION}-amd64.deb -n
   ```

   Or use dpkg:
   ```bash
   sudo dpkg -i rstudio-server-${VERSION}-amd64.deb
   sudo apt-get install -f -y
   ```

## Step 6: Start RStudio Server

1. Check RStudio Server status:
   ```bash
   sudo rstudio-server status
   ```

2. If it's not running, start it:
   ```bash
   sudo rstudio-server start
   ```

**Note:** RStudio Server needs to be started manually in each WSL session. You'll need to enter your password when starting the service.

## Step 7: Access RStudio in Browser

1. Open your web browser (Chrome, Firefox, Edge, etc.)

2. Navigate to:
   ```
   http://localhost:8787
   ```

3. Log in with your Ubuntu WSL username and password (created in Step 2)

4. You should now see the RStudio interface!

## Troubleshooting

### RStudio Server won't start

- Check if the service is active:
  ```bash
  sudo rstudio-server status
  ```

- View logs for errors:
  ```bash
  sudo rstudio-server verify-installation
  sudo cat /var/log/rstudio-server.log
  ```

- Try restarting the service:
  ```bash
  sudo rstudio-server restart
  ```

### Can't connect to localhost:8787

- Ensure RStudio Server is running (see above)
- Check if port 8787 is in use:
  ```bash
  sudo lsof -i :8787
  ```
- Try accessing from a different browser
- Check Windows Firewall settings

### WSL2 Network Issues

- Restart WSL:
  ```powershell
  wsl --shutdown
  ```
  Then restart Ubuntu from the Start menu

### Permission Issues

- Ensure you're using the correct username and password
- Check that your user is in the correct groups:
  ```bash
  groups
  ```

### Installing R Packages

If you encounter permission issues when installing R packages:

1. Create a personal library directory:
   ```bash
   mkdir -p ~/R/library
   ```

2. Set it in your `.Renviron` file:
   ```bash
   echo 'R_LIBS_USER="~/R/library"' >> ~/.Renviron
   ```

## Additional Tips

### Accessing Windows Files from WSL

Your Windows drives are mounted under `/mnt/`:
```bash
cd /mnt/c/Users/YourUsername/Documents
```

### Accessing WSL Files from Windows

In File Explorer, type:
```
\\wsl$\Ubuntu\home\yourusername
```

Or navigate to the path shown in:
```bash
explorer.exe .
```

### Installing Additional R Packages

From within RStudio or R console:
```r
install.packages("tidyverse")
install.packages("ggplot2")
```

### Stop RStudio Server

To stop RStudio Server when not in use:
```bash
sudo rstudio-server stop
```

### Automate RStudio Server Startup (Optional)

If you want RStudio Server to start automatically, you have a few options:

**Option 1: Manual start alias**
Add an alias to your `.bashrc` for easy starting:
```bash
echo "alias start-rstudio='sudo rstudio-server start'" >> ~/.bashrc
source ~/.bashrc
```
Then just run `start-rstudio` when needed.

**Option 2: Configure passwordless sudo for RStudio Server (Advanced)**
⚠️ **Security Note**: Only do this if you understand the security implications.

1. Edit sudoers file:
   ```bash
   sudo visudo
   ```

2. Add this line at the end (replace `yourusername` with your actual username):
   ```
   yourusername ALL=(ALL) NOPASSWD: /usr/sbin/rstudio-server
   ```

3. Then add to `.bashrc`:
   ```bash
   echo "sudo rstudio-server start 2>/dev/null" >> ~/.bashrc
   ```

### Update RStudio Server

1. Download the latest version
2. Install using the same method as Step 5
3. Restart the service

### System Resource Management

To check memory and CPU usage in WSL:
```bash
htop
```

(Install htop if needed: `sudo apt install htop`)

## Useful Commands

- **Check WSL version:** `wsl --list --verbose` (in PowerShell)
- **Restart WSL:** `wsl --shutdown` (in PowerShell)
- **Update WSL:** `wsl --update` (in PowerShell)
- **Check Ubuntu version:** `lsb_release -a`
- **Update all packages:** `sudo apt update && sudo apt upgrade -y`

## Resources

- [WSL Documentation](https://learn.microsoft.com/en-us/windows/wsl/)
- [RStudio Server Documentation](https://posit.co/products/open-source/rstudio-server/)
- [R Project](https://www.r-project.org/)
- [Ubuntu Documentation](https://ubuntu.com/wsl)

## License

This guide is provided as-is for educational purposes.

---

**Happy coding with R in WSL!** 🚀