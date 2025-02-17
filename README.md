
# 🚀 VProfile Project - Docker Compose Setup

---

## 📖 Overview
This document provides a step-by-step guide to setting up a **Vagrant Virtual Machine** using **Ubuntu Jammy**. The setup includes networking, resource allocation, and provisioning options to automate the development environment.

---

## 📑 Table of Contents

- [Prerequisites](#-prerequisites) 🔑
- [Architecture](#-architecture) 🗺️
- [Setup & Installation](#-setup-and-installation) 🛠️
- [Vagrant Setup](#-vagrant-setup) 🐾
- [Docker Setup](#-docker-setup) 🐳
- [Cleaning Up Resources](#-cleaning-up-resources) 🧹
- [Conclusion](#-conclusion) ✅

---

## 🔑 Prerequisites
Before starting, ensure you have the following installed:
- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- A terminal or command prompt with administrative privileges

---

## 🗺️ Architecture
This project utilizes Vagrant to set up an **Ubuntu virtual machine** with the following configurations:

- **Base OS:** Ubuntu Jammy 64-bit
- **Networking:**
  - Private network with a static IP (`192.168.56.14`)
  - Public network for external access
- **Resource Allocation:**
  - Memory: 1600MB
  - CPUs: 2
- **Synced Folder:**
  - Maps `D:\scripts\shellscripts` on the host to `/opt/scripts` in the VM
- **Provisioning (Optional):**
  - Can be configured to install packages (like Apache) automatically

### Workflow:
1. **Initialize Vagrant** – Create the required configuration.
2. **Start the VM** – Boot the virtual machine.
3. **Access the VM** – SSH into the machine.
4. **Validate Network Settings** – Check assigned IPs.
5. **Destroy the VM** – Cleanup once testing is complete.

---

## 🛠️ Setup & Installation
### 1⃣ Initialize Vagrant:
```bash
vagrant init
```

---

## 🐾 Vagrant Setup 🖥️

Below is the `Vagrantfile` configuration used for this project:

```ruby
Vagrant.configure("2") do |config|
  # Use Ubuntu Jammy 64-bit box
  config.vm.box = "ubuntu/jammy64"

  # Configure private network with specific IP
  config.vm.network "private_network", ip: "192.168.56.82"

  # Configure public network (Bridged network)
  config.vm.network "public_network"

  # VirtualBox specific configuration
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end

  # Keep the default insecure SSH key
  config.ssh.insert_key = false

  # Provisioning with shell script to install Docker and Docker Compose
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg -y

    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

    sudo curl -L "https://github.com/docker/compose/releases/download/v2.1.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose
  SHELL
end

```

### 2⃣ Start the Virtual Machine:
```bash
vagrant up
```

### 3⃣ Check VM Status:
```bash
vagrant status
```

### 4⃣ SSH into the VM:
```bash
vagrant ssh
```

### 5⃣ Check Network Configuration:
```bash
ip addr show
```

### 6⃣ Exit the VM:
```bash
exit
```

---

## 🐳 Docker Setup

### 1⃣ **Install Docker inside the VM**:
```bash
sudo apt-get update
sudo apt-get install -y docker.io
```

### 2⃣ **Verify Docker Installation**:
```bash
docker --version
```

### 3⃣ **Run a Test Container**:
```bash
docker run hello-world
```

### 4⃣ **Docker Compose Setup**:
Install Docker Compose inside the VM if it's not already installed:
```bash
sudo apt-get install -y docker-compose
```

### 5⃣ **Create a `docker-compose.yml` File**:
In your VM, create a `docker-compose.yml` file with the following content:
```yaml
version: '3.8'
services:
  vprodb:
    image: vprofile/vprofiledb
    ports:
      - "3306:3306"
    volumes:
      - vprodbdata:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=vprodbpass

  vprocache01:
    image: memcached
    ports:
      - "11211:11211"

  vpromq01:
    image: rabbitmq
    ports:
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest

  vproapp:
    image: vprofile/vprofileapp
    ports:
      - "8080:8080"
    volumes:
      - vproappdata:/usr/local/tomcat/webapps

  vproweb:
    image: vprofile/vprofileweb
    ports:
      - "80:80"

volumes:
  vprodbdata: {}
  vproappdata: {}
```

### 6⃣ **Start Docker Compose Services**:
Navigate to the directory where the `docker-compose.yml` file is located, then run:
```bash
docker-compose up -d
```

### 7⃣ **Verify Running Containers**:
```bash
docker-compose ps
```

### 8⃣ **Stop Docker Compose Services**:
```bash
docker-compose down
```

---

## 🧹 Cleaning Up Resources

To remove the VM and free up system resources, run the following commands in order:

### 1⃣ Destroy the Virtual Machine:
```bash
vagrant destroy
```

### 2⃣ Remove Unused Vagrant Instances:
```bash
vagrant global-status --prune
```

---

## ✅ Conclusion

This project demonstrates how to automate VM provisioning using **Vagrant and VirtualBox**, making it easier to manage development and testing environments efficiently. It also integrates Docker to facilitate containerized application deployment within the VM.

---

## 👨‍🏫 Instructor

This project was guided by **Imran Teli**, who provided valuable mentorship throughout the process.

---
