# Debian Dev PC Bootstrap Installation

This guide walks you through setting up a Debian-based personal PC with various system configurations and applications. It covers everything from configuring `vi` to enabling hibernate, setting DNS resolvers, preserving your Bash history, and installing useful applications.

---

## Table of Contents

1. [Enable INSERT in vi](#enable-insert-in-vi)
2. [Configure Sudo Access](#configure-sudo-access)
3. [System Update](#system-update)
4. [Disable IPv6](#disable-ipv6)
5. [Setup SSH Keys](#setup-ssh-keys)
6. [Enable Hibernate](#enable-hibernate)
7. [Install Applications](#install-applications)
    - [Sublime Text](#sublime-text)
    - [PHP](#php)
    - [Heroku CLI](#heroku-cli)
    - [Additional Tools](#additional-tools)
    - [Visual Studio Code](#visual-studio-code)
    - [Other Applications](#other-applications)
8. [Additional Configuration](#additional-configuration)
    - [Set DNS](#set-dns)
    - [Preserve Bash History](#preserve-bash-history)
9. [Clean Up](#clean-up)

---

## 1. Enable INSERT in vi

To improve your `vi` experience, switch to the root user and install `vim-gui-common` which provides better support for input modes.

```bash
su
sudo apt-get install vim-gui-common -y
```

---

## 2. Configure Sudo Access

Add your user to the sudoers file to allow administrative commands without switching users. Open `/etc/sudoers` with your favorite editor (using `visudo` is recommended) and add:

```text
my_username ALL=(ALL:ALL) ALL
```

> **Note:** Replace `my_username` with your actual username.

---

## 3. System Update

Update the package lists and upgrade installed packages:

```bash
sudo apt update -y && sudo apt full-upgrade -y
```

---

## 4. Disable IPv6

*Instructions for disabling IPv6 are not detailed here. Please consult your system documentation or online resources for the correct steps.*

---

## 5. Setup SSH Keys

If you need to manage SSH keys:

1. **Edit or create your private key (if necessary):**

    ```bash
    vi ~/.ssh/id_rsa
    ```

2. **Set the correct permissions:**

    ```bash
    chmod 600 ~/.ssh/id_rsa
    ```

3. **Generate the public key from your private key:**

    ```bash
    ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.pub
    ```

---

## 6. Enable Hibernate

To enable hibernate functionality on your system, follow these steps:

### 6.1 Adjust Swappiness

Set the swappiness to a low value (1) to favor hibernation over swap usage:

```bash
sudo sysctl -w vm.swappiness=1
```

To make this change permanent, edit (or create) the local sysctl configuration file:

```bash
sudo vi /etc/sysctl.d/local.conf
```

Add the following line:

```
vm.swappiness=1
```

### 6.2 Create and Configure Swapfile

Create a 15G swapfile, set the appropriate permissions, and initialize it:

```bash
sudo fallocate -l 15G /swapfile
sudo chmod 600 /swapfile
mkswap /swapfile
sudo swapon /swapfile
```

Make the swapfile persistent by adding it to `/etc/fstab`:

```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### 6.3 Configure Hibernate Boot Parameters

You will need to determine your system’s root UUID and the swapfile offset:

- **Find the UUID:**

  ```bash
  findmnt / -o UUID -n
  ```

- **Find the swapfile offset:**

  ```bash
  sudo filefrag -v /swapfile | awk 'NR==4{gsub(/\./,""); print $4;}'
  ```

With your UUID and swapfile offset in hand, update the bootloader and initramfs configurations:

1. **Update GRUB:**

   Edit `/etc/default/grub`:

   ```bash
   sudo vi /etc/default/grub
   ```

   Append or modify the `GRUB_CMDLINE_LINUX_DEFAULT` line to include the resume parameters (replace the example UUID and offset with your actual values):

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="resume=UUID=178f7336-5875-40bc-be93-58dca79a49dc resume_offset=58673152"
   ```

2. **Configure initramfs resume settings:**

   Edit (or create) the resume configuration file:

   ```bash
   sudo vi /etc/initramfs-tools/conf.d/resume
   ```

   Add the following line (replace the example UUID with your actual UUID):

   ```
   RESUME=UUID=178f7336-5875-40bc-be93-58dca79a49dc
   ```

### 6.4 Update Bootloader and Initramfs

Apply the changes by updating GRUB and the initramfs:

```bash
sudo update-grub
sudo update-initramfs -k all -u
```

### 6.5 Test Hibernate

Test the hibernate function with:

```bash
systemctl hibernate
```

> **Note:** Replace the example UUID (`178f7336-5875-40bc-be93-58dca79a49dc`) and offset (`58673152`) with the values obtained from your system.

---

## 7. Install Applications

### Sublime Text

Add the Sublime Text repository and install the editor:

```bash
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/sublimehq-archive.gpg > /dev/null
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt update -y && sudo apt install sublime-text -y
```

### PHP

Install PHP without extra recommended packages:

```bash
sudo apt install php --no-install-recommends -y
```

### Additional Tools

Install a collection of useful tools and applications:

```bash
sudo apt install curl vlc gimp ffmpeg nodejs npm apt-transport-https filezilla python3-pip python3-venv -y
```

### Heroku CLI

Install the Heroku Command Line Interface:

```bash
curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
```

### Visual Studio Code

Follow these steps to install Visual Studio Code:

1. **Install prerequisites:**

    ```bash
    sudo apt-get install wget gpg -y
    ```

2. **Add Microsoft's GPG key:**

    ```bash
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
    sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
    ```

3. **Add the VSCode repository:**

    ```bash
    echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
    ```

4. **Clean up the temporary key file:**

    ```bash
    rm -f packages.microsoft.gpg
    ```

5. **Update and install VSCode:**

    ```bash
    sudo apt update -y && sudo apt install code -y
    ```

### Other Applications

For additional software, refer to the following resources or their official installation guides:

- **1Password:** [Install 1Password for Linux](https://support.1password.com/install-linux/)
- **Docker**
- **Studio 3T**
- **Android Studio**

> **Note:** Installation steps for Docker, Studio 3T, and Android Studio are not included here. Please consult their official documentation for setup instructions.

---

## 8. Additional Configuration

This section covers extra configuration steps for setting DNS resolvers and preserving your Bash history.

### 8.1 Set DNS

Reset your DNS configuration and add preferred nameservers:

```bash
sed -i '/nameserver/d' /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee -a /etc/resolv.conf
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
```

> **Note:** This removes existing nameserver entries in `/etc/resolv.conf` and adds Cloudflare (`1.1.1.1`) and Google (`8.8.8.8`) DNS servers. Adjust as necessary.

### 8.2 Preserve Bash History

Ensure that your Bash history is preserved across sessions by appending the following lines to your `~/.bashrc`:

```bash
echo 'shopt -s histappend' >> ~/.bashrc
echo 'PROMPT_COMMAND="history -a; $PROMPT_COMMAND"' >> ~/.bashrc
source ~/.bashrc
```

The `source ~/.bashrc` command reloads your Bash configuration immediately. The final `exit` command (if desired) will end your current session:

```bash
exit
```

> **Note:** Use `exit` only when you are finished configuring your shell or if you wish to log out.

---

## 9. Clean Up

Remove any unnecessary packages to keep your system tidy:

```bash
sudo apt autoremove -y
```
