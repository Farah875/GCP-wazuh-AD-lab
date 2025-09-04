# GCP Lab Setup

This lab shows how to create a **Google Cloud Platform (GCP) lab** that includes:
- **Kali Linux attacker VM**
- **Windows Server Active Directory victim**
- **Wazuh SIEM server** for monitoring and detection

The lab simulates brute-force RDP attacks from Kali → Windows and configures Wazuh for detection.

---

## Prerequisites

- GCP trial account (**$300 free credits**)  
  > Note: credit card confirmation is required at signup.  
- RDP client (for Windows Server)  
- SSH client (or use GCP browser SSH)  
- Basic understanding of Linux, Windows Server, and Active Directory  

---

##  Step 1: Deploy Kali Linux

1. Go to GCP Marketplace and select a Kali image:  
   [Kali Linux GCP Marketplace](https://console.cloud.google.com/marketplace/product/techlatest-public/kali-linux?inv=1&invt=Ab3KTQ&project=tidy-scholar-458718-u6)  
   <img width="592" height="263" alt="image" src="https://github.com/user-attachments/assets/f3b9e520-6b93-4cb4-9361-9d70e7c1a57e" />

  
2. Deploy the VM.  
3. After deployment, your VM will appear under:  
   **Compute Engine → VM Instances**.
   <img width="624" height="47" alt="image" src="https://github.com/user-attachments/assets/bbe3c810-03ef-4703-b00f-1197972fb6f1" />

4. By default, SSH is enabled → click **SSH** to access via command line.  

<img width="624" height="250" alt="image" src="https://github.com/user-attachments/assets/5f3ed2d2-2818-434f-9aa1-4b7009a90ed9" />

### GUI Setup (Optional)
If you want GUI access:  
- Install **Chrome** and **Chrome Remote Desktop** on Kali.  
- From your browser, set up a new device using SSH → run the provided command inside Kali.  
- **Important:** set a user password before configuring Chrome Remote Desktop.
  
<img width="624" height="250" alt="image" src="https://github.com/user-attachments/assets/c6f22eb8-662b-4cde-84ec-891239f88643" />

<img width="624" height="300" alt="image" src="https://github.com/user-attachments/assets/a1a4c1a9-6ddf-440c-961c-470d39c6e149" />

---

##  Step 2: Deploy Windows Server (Victim)

1. Go to **Compute Engine → VM Instances → Create Instance**.  
2. Choose:  
   - Machine type: **n2-medium**  
   - OS: **Windows Server 2016**  
     - ⚠️ If you select the *Core* version, it won’t have a GUI.

<img width="428" height="373" alt="image" src="https://github.com/user-attachments/assets/51236353-492d-4298-84f3-c828f4439a6a" />

     
3. Modify other settings as you like.  
4. Firewall: by default, RDP (3389) is enabled. Other ports can be opened later.  
5. Deploy the VM.  

### First Login
1. After deployment, click the VM → set a password.  
   - Google provides the username + password. Copy them.  
   - Note: this account is a **normal admin role**, not the built-in **Administrator**.  
2. RDP to the machine using its public IP.  
3. Inside the VM:  
   - Open **Local Users and Groups**.  
   - Enable the **Administrator** account.  
   - Set a strong password.  
<img width="428" height="110" alt="image" src="https://github.com/user-attachments/assets/169282ff-c5e8-4cfb-a444-07ee2832fb19" />
<img width="346" height="454" alt="image" src="https://github.com/user-attachments/assets/bfe19055-8924-49db-ac37-2f8e94312310" />

---

## Step 3: Configure Active Directory

1. Open **Server Manager → Add Roles and Features**.  
2. Add **Active Directory Domain Services (AD DS)**.

<img width="428" height="413" alt="image" src="https://github.com/user-attachments/assets/91b8bbbc-153b-42fb-ae3c-3234c895f44f" />

   
3. Click the post-deployment notification.

<img width="238" height="194" alt="image" src="https://github.com/user-attachments/assets/cb78e62e-bd2b-4578-b089-b0fd11ee8870" />

     
4. Select **“Add a new forest”** and name your domain (example: `lab.local`).  
5. Set a password for Directory Services.

<img width="478" height="422" alt="image" src="https://github.com/user-attachments/assets/5dceaf4e-fe0c-41e3-8a86-651582e22014" />

    
6. Under DNS options, change settings and configure your admin username + password.  
7. Click **Next** through the wizard and install.  

---

## Step 4: Deploy Wazuh SIEM

1. Go to **Compute Engine → Create Instance**.  
2. According to [Wazuh documentation](https://documentation.wazuh.com/), choose:  
   - Machine type: **n2** (better performance).  
   - OS: Ubuntu or Debian.
  
<img width="600" height="278" alt="image" src="https://github.com/user-attachments/assets/5561d8a2-70e0-42ae-a290-e66eecd92295" />

<img width="428" height="379" alt="image" src="https://github.com/user-attachments/assets/fb88f6f5-c520-4b00-9853-62e162d20475" />

  
3. In the **Networking tab**, allow:  
   - **HTTP (80)**  
   - **HTTPS (443)**
  <img width="428" height="156" alt="image" src="https://github.com/user-attachments/assets/f3f945cc-162b-409e-9c20-1b761e155da8" />

     
4. Deploy the VM.  
5. After deployment:
      - Change the default password.  
   - Update and upgrade the OS:  

   ```bash
   sudo apt update && sudo apt upgrade -y

6. Install Wazuh.

 <img width="624" height="218" alt="image" src="https://github.com/user-attachments/assets/6e418f76-9892-4e07-b508-1f898b9ab652" />
 
7. After installation:  
   - Copy the dashboard username + password.  
   - Access the Wazuh dashboard at the server’s **public IP**.  
   - Login with `admin` and the provided password.  

<img width="624" height="278" alt="image" src="https://github.com/user-attachments/assets/12df5e4b-5130-4ab6-885a-3cf7f07d77d5" />


---

##  Step 5: Install Wazuh Agent on Windows Server

1. From the **Wazuh Dashboard → Add a new agent**.

<img width="624" height="326" alt="image" src="https://github.com/user-attachments/assets/14cc8575-3cf2-4305-a605-eb3981388abf" />

   
2. Enter the **internal IP** of your Wazuh server.  
3. Copy the commands provided.  
4. On Windows Server (run PowerShell as Administrator):  

   ```powershell
   # Example command from Wazuh dashboard
   Invoke-WebRequest -Uri "<agent_installer_url>" -OutFile "wazuh-agent.msi"
   msiexec.exe /i wazuh-agent.msi /quiet
   ```

5. Start the agent:

   ```powershell
   net start wazuh-agent
   ```

6. Refresh the Wazuh dashboard → your Windows server should appear as **active**.  

<img width="624" height="96" alt="image" src="https://github.com/user-attachments/assets/288a0665-2fde-4699-81d9-4be1b765e46e" />

<img width="624" height="222" alt="image" src="https://github.com/user-attachments/assets/5414bfbb-8a6a-47a5-9f4b-0b0c98504470" />

---

##  Step 6: First Test – Log Collection

- Create a **new user in Active Directory**.  
- Check the Wazuh dashboard → the log should appear.  

<img width="624" height="193" alt="image" src="https://github.com/user-attachments/assets/552c19e1-218b-4694-89c4-cc0a23804bc2" />

---

##  Step 7: Brute-Force RDP Attack Simulation

1. On Kali, install Ncrack (from Nmap toolkit):  

   ```bash
   sudo apt install ncrack -y
   ```

   <img width="624" height="282" alt="image" src="https://github.com/user-attachments/assets/ef62e7fd-c3ac-44ce-aca2-cd9fa8c0b4fc" />


2. Run brute-force attack against the Windows VM:  

   ```bash
   ncrack -p 3389 -u testuser -P /usr/share/wordlists/rockyou.txt <WINDOWS_PUBLIC_IP>
   ```
<img width="624" height="80" alt="image" src="https://github.com/user-attachments/assets/3c76f485-dab7-42f3-ba80-a5b34fdc46be" />


3. In Wazuh → check **Security Events**.  
   - Alerts for failed RDP logons should appear.  

<img width="624" height="204" alt="image" src="https://github.com/user-attachments/assets/cb57aa6a-51c0-484a-9b51-2f8f70e1d214" />

---


## Step 8: Visualizations & Dashboards

1. In **OpenSearch Dashboards** → go to **Visualizations → Create New**.  
2. Select **Wazuh Alerts** index.  
3. Example:  
   - Choose **Table**.  
   - In search bar, filter for MITRE classification = brute force attack.
4. Split the table using **buckets**.  
5. Add rows using different **metrics**.  
6. Save visualization.  
7. Create a new **Dashboard** → add your visualizations.  

<img width="624" height="278" alt="image" src="https://github.com/user-attachments/assets/709cea0f-a3d7-4b86-9e58-0e74a523c2b1" />

<img width="624" height="274" alt="image" src="https://github.com/user-attachments/assets/42177416-7d01-4715-8e1d-0163f4cfcea7" />


## End of Lab

At this stage you have a working **GCP Security Lab**:
- **Attacker:** Kali Linux  
- **Victim:** Windows Server Active Directory  
- **Defender:** Wazuh SIEM  

From here you can extend the lab to simulate more attacks and detection use cases.

## Additional Guides

- [Detecting Attacks on the GCP Network](detecting-attacks.md)
