# Detecting Attacks on the GCP Network

This guide documents how to detect and block malicious IP activity targeting your **Google Cloud Platform (GCP)** environment using **Wazuh SIEM** and **GCP Firewall rules**.

---

## Detection

- During monitoring in **Wazuh**, multiple attempts to access the Wazuh server were detected.  

<img width="624" height="234" alt="image" src="https://github.com/user-attachments/assets/c3ff353d-ea38-4c5f-b7da-dc935e5572f5" />

- Investigation of the **source IP address** confirmed it was malicious (verified via **VirusTotal**).  
<img width="624" height="189" alt="image" src="https://github.com/user-attachments/assets/5a8acbee-895f-4b68-babc-16cef94f5a73" />

<img width="624" height="295" alt="image" src="https://github.com/user-attachments/assets/4be55aae-93f7-4eb8-b932-f7e1d0cb9fbc" />

---

## Blocking the Malicious IP in GCP

1. In GCP Console, go to:  
   **VPC Network → Firewall → Create Firewall Rule**.  
2. Configure the rule:  
   - **Name:** block-malicious-ip  
   - **Targets:** All instances in the network (or restrict as needed)  
   - **Source IP Ranges:** `<malicious_IP>`  
   - **Action:** Deny  
   - **Protocols/Ports:** All (or restrict to specific ports if known)

  <img width="604" height="477" alt="image" src="https://github.com/user-attachments/assets/53a1eaf6-36f6-47b2-ae37-67897fb90a71" />

   <img width="586" height="540" alt="image" src="https://github.com/user-attachments/assets/b4568933-abff-4ed1-a1aa-1c12e6ad5af7" />
  
3. Click **Create**.  

---

## Validation

- After applying the firewall rule, monitoring continued.  
- Logs from the malicious IP **stopped completely**.  
- Example observation: screenshot confirmed at **9:40 PM** showing no further attempts.
  
<img width="624" height="177" alt="image" src="https://github.com/user-attachments/assets/08eb79c8-fcb0-4a31-894d-07b9f14a1989" />

---

##  Summary

This process demonstrates how to:  
1. Detect malicious traffic with **Wazuh**.  
2. Validate the threat with **VirusTotal**.  
3. Apply **network-level blocking** in GCP.  
4. Confirm effectiveness by monitoring logs post-blocking.  



---
