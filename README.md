# Wazuh SIEM Lab - Kali Linux Manager & Windows Agent
### Overview
This project demonstrates the deployment of a centralized **Wazuh SIEM** environment using a **Kali Linux-based manager** and a **Windows 11 endpoint**. The setup demonstrates log ingestion, agent-based monitoring, and real-time **File Integrity Monitoring (FIM)**, simulating a basic **Security Operations Center (SOC)** detection workflow suitable for entry-level security operations roles.

The primary objective of this lab is to gain hands-on experience with SIEM deployment, endpoint monitoring and alert validation in a controlled environment

_____
### Prerequisites
1. CPU virtualization enabled via BIOS
2. Internet access between Kali VM and Windows machine
3. Basic knowledge of Linux

_____
### System Requirements
1. Host machine
    - 64-bit system with hardware virtualization support
    - 16 GB of RAM recommended (8 GB minimum)
    - At least 60 GB of available disk space
    - VirtualBox 7.x or newer installed
    - Internet connectivity for package downloads and agent communication

3. Wazuh manager (Kali Linux VM)
      - Kali Linux
      - 2 vCPUs (minimum
      - 4 GB RAM minimum; 6-8 GB recommended for stability
      - 45 GB disk space
      - Network adapter
          1. Bridged Adapter (lab enviornments only) or
          2. NAT with port forwarding configures
      - Root privileges

5. Windows Endpoint (Wazuh Agent)
      - Windows 11
      - Local administrator privileges
      - Network connectivity to the Wazuh manager
      - Latest stable version of Wazuh agent
      - Notepad or text editor capable of running with elevated privileges

7. Network Requirements
      - Bidirectional network connectivity between the Kali VM and Windows endpoint
      - TCP ports
        1. 1514 (Wazuh agent communication)
        2. 1515 (Agent registration)
        3. 443 (HTTPS web dashboard)

____
### Installation & Setup
- Kali Linux was installed using the official installer ISO with default pasritioning and desktop settings. The VM was allocated 6 GB RAM, 2 vCPUs, and 50 GB storage
    - In this lab, bridged networking is used to allow direct communication between the Windows endpoints and the Wazuh manager. This configuration should only be used in isolated lab environments, as it exposes the VM directly to the local network. NAT with port forwarding is recommended when isolating the lab environment from the host network
- Installing Wazuh on Kali
    - Via the terminal import the Wazuh GPG key

    `curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg`

    - Download and run the Wazuh installation script

      `curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i`

        - This script installs all required services and configures them automatically, this download will take a few minutes, after the installation a username and password will be printed, this will be sued to access the dashboard via a web browser
  
  - After the installation is complete verify that the services are running

    `systemctl status wazuh-manager`
    
    `systemctl status wazuh-indexer`
    
    `systemctl status wazuh-dashboard`

  - Find the VMs IP address via the command

    `ip a`

  - Access the dashboard

    `https://<Kali_VM_IP>`

      - Self-sgined certificate warnings are expected in lab enviroments

- Windows Wazuh agent
    - Adminstrative privileges are required during installation to allow service registration
    - Download and install the latest Wazuh agent for Windows using default settings
    - On the Kali manager, register the agent

      `sudo /var/ossec/bin/manage_agents`

        - Select 'A' to add an agent
        - Give the agent a recognizable name
        - In the Windows command line find the Windows host IP address via the `ipconfig` command, this will be used during registration
        - After creating a new agent input 'E' to extract the key for the agent
        - Copy the agent key information
            - Agent authentication ensures that only authorized endpoints can send telemetry to the Wazuh manager

    - In the Windows agent, paste the agent key into the 'Authentication key' section of the application
    - For manager IP, input the VMs IP address
    - Save the infomration, under 'Manage' in the toolbar select 'Restart' to start the agent

- File Integrity Monitoring
    - Run Notepad as Administrator
    - Open:

        `C:\Program Files (x86)\ossec-agent\ossec.conf`

    - Locate `<syscheck>` section and add:

        `<directories realtime="yes">PreferredDirectory</directories>`

    - Save the file and restart the Wazuh agent service
    - Generate a test alert by creating or modifying a file in the monitored directory
    - Verify alerts in:

      `Dashboard -> Endpoint Security -> File Integrity Monitoring`

_____

### Use Case

  File Integrity Monitoring alerts are commonly used to detect
  - Unauthorized file changes
  - Malware persistence mechanisms
  - Insider threats
  - Tampering with system or application files in critical directories

_____
### Future Improvements
- Sysmon integration on Windows endpoint
- Active response rules
- Additional monitored endpoints
- Custom detection rules
      
       
        
   
