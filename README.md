# 🚀 VProfile Project - Docker Compose Setup

---

## 📖 Overview
This document provides a step-by-step guide to setting up the **VProfile Project** using **Docker Compose**. The project consists of multiple services such as MySQL, Memcached, RabbitMQ, Tomcat (application), and Nginx (web server). Using Docker Compose allows for easy deployment and management of these services in a containerized environment.

---

## 📑 Table of Contents
- [Prerequisites](#prerequisites) 🔑
- [Architecture](#architecture) 🗺️
- [Setup & Installation](#setup-and-installation) 🛠️
- [Docker Compose Configuration](#docker-compose-configuration) 🐳
- [Running the Project](#running-the-project) ▶️
- [Cleaning Up Resources](#cleaning-up-resources) 🧹
- [Conclusion](#conclusion) ✅

---

## 🔑 Prerequisites
Before starting, ensure you have the following installed:
- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install/)
- A terminal or command prompt with administrative privileges

---

## 🗺️ Architecture
This project utilizes **Docker Compose** to set up a multi-container environment with the following components:

- **vprodb (Database)**: MySQL container storing application data.
- **vprocache01 (Caching Service)**: Memcached for caching frequently accessed data.
- **vpromq01 (Message Queue)**: RabbitMQ for asynchronous messaging.
- **vproapp (Application Service)**: Tomcat-based Java application.
- **vproweb (Web Server)**: Nginx server acting as a frontend.

### Workflow:
1. **Pull the necessary images** from Docker Hub.
2. **Start all containers** using Docker Compose.
3. **Check running containers** to ensure they are operational.
4. **Access the application** using a web browser.
5. **Stop and remove containers** when testing is complete.

---

## 🛠️ Setup & Installation
### 1⃣ Create the Project Directory:
```bash
mkdir compose
cd compose/
```

### 2⃣ Download the Docker Compose Configuration:
```bash
wget https://raw.githubusercontent.com/devopshydclub/vprofile-project/docker/compose/docker-compose.yml
```

### 3⃣ Verify the Downloaded File:
```bash
ls
vim docker-compose.yml  # (Optional: Edit if needed)
```

---

## 🐳 Docker Compose Configuration
Below is the `docker-compose.yml` configuration used for this project:

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

---

## ▶️ Running the Project
### 1⃣ Start All Containers:
```bash
docker compose up -d
```

### 2⃣ Check Running Containers:
```bash
docker compose ps
```

### 3⃣ Find the VM's IP Address:
```bash
ip addr show
```

### 4⃣ Access the Application in Browser:
```bash
http://<VM_IP>:80
```

---

## 🧹 Cleaning Up Resources
To remove the containers and free up resources, run the following commands:

### 1⃣ Stop and Remove Containers:
```bash
docker compose down
```

### 2⃣ Remove Unused Docker Volumes:
```bash
docker volume prune -f
```

---

## ✅ Conclusion
This guide outlines how to set up and deploy the **VProfile Project** using **Docker Compose**. By leveraging containerization, the application can be efficiently managed, scaled, and deployed with minimal effort.

---

## 👨‍🏫 Instructor
This project was guided by **Imran Teli**, who provided valuable mentorship throughout the process.

