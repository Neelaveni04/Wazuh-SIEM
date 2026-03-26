# Wazuh File Integrity Monitoring Project

## Project Title
**Wazuh File Integrity Monitoring (FIM) Lab Using Windows Agent and Ubuntu Manager**

## 1. Project Summary
This project demonstrates a basic Security Operations / Blue Team lab using **Wazuh** for endpoint monitoring and file integrity monitoring.

- **Wazuh Manager**: Ubuntu virtual machine
- **Wazuh Agent**: Windows host machine
- **Virtualization**: VMware Workstation / VirtualBox
- **Use Case**: Monitor file and folder changes on Windows in real time and send alerts to the Wazuh dashboard

---

## 2. Project Objective
The main objective of this project is to:
1. Install and configure Wazuh Manager on Ubuntu.
2. Install and enroll the Wazuh Agent on Windows.
3. Enable File Integrity Monitoring on a Windows folder.
4. Generate alerts in the Wazuh dashboard when files are created, modified, or deleted.
5. Document the setup in a way that can be shared on LinkedIn and GitHub.

---

## 3. Lab Architecture

| Component | System | Role |
|---|---|---|
| Wazuh Manager | Ubuntu VM | Collects logs, analyzes events, and stores alerts |
| Wazuh Agent | Windows Host | Sends system and file integrity events to the manager |
| Network Mode | Bridged Adapter | Places Ubuntu VM on the same network as the host for communication |

---

## 4. Prerequisites
Before starting, make sure you have:

- VMware Workstation or VirtualBox installed
- Ubuntu Server/Desktop VM with internet access
- Windows system to install Wazuh Agent
- Administrative/root privileges on both systems
- Stable network connectivity
- Wazuh official documentation for version reference

---

## 5. Step-by-Step Technical Implementation

### 5.1 Set Up Virtual Machines
1. Install VMware Workstation or VirtualBox.
2. Create an **Ubuntu VM** for the Wazuh Manager.
3. Ensure the Ubuntu VM is configured with enough resources:
   - CPU: 2 or more cores
   - RAM: 4 GB minimum (8 GB recommended)
   - Disk: 50 GB or more
4. Configure the network adapter as **Bridged Adapter** so that the Ubuntu VM receives an IP address in the same network as the host.
5. Confirm that the Windows host and Ubuntu VM can reach each other using `ping`.

---

### 5.2 Install Wazuh Manager on Ubuntu

#### Step 1: Update the system
```bash
sudo apt update && sudo apt upgrade -y
```

#### Step 2: Add the Wazuh GPG key
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg
```

This imports the official Wazuh package signing key and allows the system to verify package integrity.

#### Step 3: Download the installation script
```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh
```

#### Step 4: Run the installer
```bash
sudo bash ./wazuh-install.sh -a -i
```

**Flags used:**
- `-a` → installs all components
- `-i` → interactive installation mode

This script installs and configures the Wazuh Manager, indexer, and dashboard components automatically.

#### Step 5: Verify installation
After the installation completes, note the username and password shown at the end of the script output.

---

### 5.3 Access the Wazuh Dashboard
1. Find the Ubuntu VM IP address:
```bash
ifconfig
```
or
```bash
ip a
```

2. Open a browser on the host machine.
3. Go to:
```text
https://<ubuntu-vm-ip>
```

4. Accept the browser warning because Wazuh uses a self-signed certificate by default.
5. Log in using the credentials generated during installation.

---

### 5.4 Install the Wazuh Agent on Windows
1. Download the latest Wazuh Agent MSI package from the official Wazuh documentation.
2. Run the installer on the Windows machine.
3. Use the default options unless your environment requires a custom path.
4. Complete the installation.

---

### 5.5 Register the Agent with the Manager

#### On Ubuntu Manager
Open the Wazuh agent management utility:
```bash
sudo /var/ossec/bin/manage_agents
```

#### In the menu:
1. Press **A** to add a new agent.
2. Enter a name such as `WindowsHost`.
3. Leave the IP address blank unless you want to bind it to a static IP.
4. Press **E** to extract the key after the agent is created.
5. Copy the generated key carefully.

#### On Windows Agent
1. Open **Wazuh Agent Manager** from the Start Menu.
2. Paste the copied key into the registration field.
3. Add the Wazuh Manager IP address.
4. Save the configuration.
5. Restart the Wazuh Agent service.

#### Validation
After registration, open the Wazuh dashboard and confirm that the Windows agent is visible and active.

---

### 5.6 Configure File Integrity Monitoring on Windows

Wazuh Syscheck monitors files and folders for changes in real time.

#### Step 1: Edit the agent configuration
Open:
```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

#### Step 2: Add the monitored directory
Inside the `<syscheck>` or directory block, add:

```xml
<directories realtime="yes">C:\Users\abc\Test</directories>
```

This tells Wazuh to monitor the `C:\Users\abc\Test` folder for:
- File creation
- File modification
- File deletion
- Permission changes

#### Step 3: Restart the agent
Restart the Wazuh Agent service to apply the changes.

#### Step 4: Test the alert
1. Create a file inside the monitored folder.
2. Modify the file.
3. Delete the file.
4. Check the Wazuh dashboard for FIM alerts.

---

## 6. Suggested Screenshots to Add

Add screenshots in the following places:

### Lab Setup
- Virtual machine network adapter configuration
- Ubuntu and Windows VM IP addresses
- Ping test between host and VM

### Wazuh Installation
- Terminal output showing Wazuh installation
- Wazuh dashboard login page
- Dashboard home page after login

### Agent Enrollment
- `manage_agents` screen showing agent creation
- Extracted agent key
- Windows agent manager showing manager IP and key entry
- Agent status in Wazuh dashboard

### File Integrity Monitoring
- `ossec.conf` with the added directory path
- File creation inside the monitored folder
- Alert generated in the Wazuh dashboard
- Alert details showing the file path, timestamp, and event type

---

## 7. Recommended GitHub Repository Structure

```text
wazuh-fim-project/
│
├── README.md
├── architecture/
│   └── lab-architecture.png
├── screenshots/
│   ├── 01-vm-network.png
│   ├── 02-ubuntu-ip.png
│   ├── 03-installation-output.png
│   ├── 04-dashboard-login.png
│   ├── 05-agent-registration.png
│   ├── 06-agent-key.png
│   ├── 07-ossec-config-fim.png
│   ├── 08-fim-test-file.png
│   └── 09-fim-alert-dashboard.png
└── notes/
    └── setup-notes.md
```

---

## 8. GitHub README Structure

Use the following structure in `README.md`:

### Suggested Sections
1. **Project Title**
2. **Overview**
3. **Objectives**
4. **Lab Architecture**
5. **Tools Used**
6. **Prerequisites**
7. **Installation Steps**
8. **Agent Enrollment**
9. **FIM Configuration**
10. **Validation and Testing**
11. **Screenshots**
12. **Lessons Learned**
13. **Conclusion**

### Example README Intro
> This project demonstrates how to deploy Wazuh for endpoint monitoring and File Integrity Monitoring using an Ubuntu-based Wazuh Manager and a Windows Wazuh Agent. The setup was built on a virtualized lab environment using VMware/VirtualBox with bridged networking to allow communication between systems.

---

## 9. Suggested LinkedIn Post Structure

Keep the LinkedIn post short, professional, and outcome-focused.

### Post Format
**Title:**  
Wazuh File Integrity Monitoring Lab | Blue Team Project

**Body Structure:**
1. What you built
2. Why you built it
3. What technologies you used
4. What you learned
5. A short call to action or professional note

### Example LinkedIn Post
I recently completed a hands-on **Wazuh File Integrity Monitoring (FIM)** lab as part of my Blue Team learning journey.

In this project, I configured:
- **Ubuntu VM** as the Wazuh Manager
- **Windows host** as the Wazuh Agent
- **Bridged networking** for communication between systems
- **Real-time file integrity monitoring** on a Windows folder

I also tested agent enrollment, key generation, dashboard verification, and alert generation after file changes.

This project helped me strengthen my understanding of:
- SIEM and endpoint monitoring
- Agent-manager architecture
- Real-time file integrity alerts
- Security event validation and investigation workflow

I will continue building more hands-on security projects to improve my SOC and Blue Team skills.

#CyberSecurity #Wazuh #SOCAnalyst #BlueTeam #SIEM #FileIntegrityMonitoring #Windows #Ubuntu #Linux #Homelab

---

## 10. Resume-Friendly Project Bullet Points

You can use these in your resume:

- Deployed a Wazuh-based monitoring lab with an Ubuntu Manager and Windows Agent using virtualized infrastructure.
- Configured real-time File Integrity Monitoring on Windows using Wazuh Syscheck to detect file creation, modification, and deletion.
- Enrolled and authenticated a Windows endpoint with the Wazuh Manager using agent keys and dashboard validation.
- Verified alert generation and monitoring visibility through the Wazuh dashboard after performing controlled file changes.

---

## 11. Lessons Learned
- How Wazuh Manager and Agent communicate
- How to use bridged networking in a virtualized lab
- How to register and authenticate agents
- How to configure real-time file monitoring
- How alerts appear in the Wazuh dashboard after endpoint activity

---

## 12. Conclusion
This project is a strong beginner-to-intermediate Blue Team / SOC project because it shows practical understanding of:
- Endpoint onboarding
- Log collection
- File integrity monitoring
- Alert validation
- Security monitoring in a real lab environment

It is a good project to include on both **LinkedIn** and **GitHub** because it demonstrates hands-on cybersecurity skills and practical implementation.

---

## 13. Screenshot Placeholder Template

Use this template in your README if you want to keep the file clean:

### Screenshot: Wazuh Dashboard Login
![Wazuh Dashboard Login](screenshots/04-dashboard-login.png)

### Screenshot: Agent Registration
![Agent Registration](screenshots/05-agent-registration.png)

### Screenshot: FIM Alert
![FIM Alert](screenshots/09-fim-alert-dashboard.png)
