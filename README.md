# Wazuh Homelab

Deploying the open-source SIEM/EDR solution Wazuh and stress testing the capabilities of the platform.

**Tags:** Wazuh, SIEM, Virtual Machines, Cyber Security, PowerShell, Metasploit  
**Prepared by:** Tristan Graff

## Table of Contents
1. [Environment Setup](#environment-setup)
2. [Deploying Wazuh Server VM](#deploying-wazuh-server-vm)
3. [Deploying Kali VM](#deploying-kali-vm)
4. [Deploying Windows VM](#deploying-windows-vm)
5. [Deploying Wazuh Agents](#deploying-wazuh-agents)
    - [Deploying Wazuh Agent to Kali VM](#deploying-wazuh-agent-to-kali-vm)
    - [Deploying Wazuh Agent to Windows VM](#deploying-wazuh-agent-to-windows-vm)
6. [Stress Testing our Agents](#stress-testing-our-agents)
    - [Manual Testing](#manual-testing)
    - [Automated Testing](#automated-testing)

## Environment Setup

### Deploying Wazuh Server VM

To get started we’re going to deploy the Wazuh Server locally on a VirtualBox VM. The documentation is up to date and very robust.

1. Install the OVA file from the linked documentation and deploy it within a VirtualBox VM.
2. Launch the VM and log in using default credentials.
3. Find the IP address of the Wazuh Server using `ip a`.
4. Browse to the Wazuh Server login page at `https://<WAZUH_SERVER_IP>`.

### Deploying Kali VM

We want to test the Wazuh Agents on a range of devices. We’re going to test on a Linux VM (Kali) and a Windows VM.

1. Download the relevant files from Kali.org.
2. Verify the hash checksum of the downloaded file.
3. Deploy the VM within VirtualBox and authenticate to access the Kali Desktop.
4. Verify internet connectivity by pinging Google (`8.8.8.8`).

### Deploying Windows VM

1. Download the necessary files for the VirtualBox VM.
2. Check the hash checksum using PowerShell:
    ```powershell
    Get-FileHash –Path <path\to\zip\file> -Algorithm SHA256
    ```
3. Deploy the ISO within VirtualBox and launch the VM.

## Deploying Wazuh Agents

### Deploying Wazuh Agent to Kali VM

1. From the Wazuh Server login page, click “Add Agent”.
2. Select OS/Architecture of the host machine.
3. Assign a server address (use the Wazuh Server IP).
4. Set a name for this agent (e.g., `KaliAgent`).
5. Copy/paste the given command onto the Kali VM to install the agent.
6. Verify the agent is checking into the Wazuh Server.

### Deploying Wazuh Agent to Windows VM

1. Follow the same process of going through the agent deployment wizard.
2. After running the command, start the service with `NET START WazuhSvc`.
3. Verify the agent is checking into the Wazuh Server.

## Stress Testing our Agents

### Manual Testing

1. Install the Metasploit Framework on the Windows VM using `wget`.
2. Perform a port scan from the Kali VM using Metasploit:
    ```sh
    msfconsole
    search portscan
    use 5
    set RHOSTS <target server IP>
    exploit
    ```
3. Check if Wazuh has alerted to any activity from the Metasploit installation or port scan.
4. Create a new user on the Windows VM and add it to the local administrator group:
    ```powershell
    net user <username> <password> /add
    net localgroup Administrators <username> /add
    ```
5. Verify high severity rules generated in the Wazuh web interface.

### Automated Testing

1. Download an Endpoint Detection and Response (EDR) testing script.
2. Fetch the file using `wget` at the terminal.
3. Unzip the file and run `runtests.bat`:
    ```sh
    ./runtests.bat
    ```
4. Check for alerts in Windows Defender and the Wazuh MITRE ATT&CK dashboard.
5. Investigate any interesting events, such as the creation of a new service.

By following these instructions, you can set up and test the Wazuh platform in a homelab environment, and then upload the project to GitHub for others to replicate and learn from.
