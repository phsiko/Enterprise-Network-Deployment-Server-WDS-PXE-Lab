# Enterprise-Network-Deployment-Server-WDS-PXE-Lab

## Objective
The goal of this project was to architect a centralized bare-metal imaging server. It demonstrates proficiency in Windows Server deployment, advanced DHCP scope configuration, and Preboot Execution Environment (PXE) network routing. By configuring Windows Deployment Services (WDS) and troubleshooting iPXE firmware handoffs, I successfully deployed an enterprise operating system over a local network, eliminating the need for localized installation media.

## Technology Stack
* **Hypervisor:** Oracle VirtualBox (Isolated NAT Networking)
* **Servers:** Windows Server 2022
* **Infrastructure:** DHCP Server, Windows Deployment Services (WDS)
* **Endpoints:** Windows 10 Enterprise (Bare-metal Virtual Machine)
* **Protocols:** IPv4, PXE, TFTP, iPXE

---

## 1. Network Isolation & Centralized DHCP Routing
Because I built this in an isolated virtual environment, I first had to disable the hypervisor's built-in networking and engineer my own routing. I installed the DHCP Server role on Windows Server 2022 and configured a custom IPv4 scope (`10.0.5.100 - 10.0.5.200`). This ensured that any blank, bare-metal machine powered on within the network would immediately lease an IP address directly from my deployment server.

<img width="1018" height="852" alt="Screenshot 2026-06-22 190057" src="https://github.com/user-attachments/assets/c63d2f74-e1a5-47af-866e-53552f47fc19" />



## 2. Preboot Environment (PXE) Configuration
Next, I installed and integrated the Windows Deployment Services (WDS) role with Active Directory. To provide the initial pre-installation environment for blank computers, I extracted the `boot.wim` file directly from the Windows 10 installation media and imported it into the WDS Boot Images directory. This acts as the lightweight OS that loads into RAM over the network to initiate the setup process.

<img width="1020" height="847" alt="Screenshot 2026-06-22 192909" src="https://github.com/user-attachments/assets/55ae3085-35d6-4d98-8c55-1c22bd60c8bc" />



## 3. OS Payload Centralization
With the boot environment established, I staged the actual operating system payload. I created a new Install Image group within WDS and imported the multi-gigabyte `install.wim` file from the Windows 10 source files. This centralized the OS payload on the server, preparing it for high-speed local network distribution.

<img width="1003" height="847" alt="Screenshot 2026-06-22 193554" src="https://github.com/user-attachments/assets/e5d209b7-1965-4246-b460-3ab68afccfb4" />



## 4. Advanced Routing & TFTP Troubleshooting
During initial testing, the client machine successfully pulled an IP but failed to locate the boot file due to iPXE firmware routing conflicts. To resolve this, I implemented advanced DHCP Scope Options. I configured **Option 066 (Boot Server Host Name)** to point to the server's static IP, and **Option 067 (Bootfile Name)** to explicitly route the client to `boot\x64\wdsnbp.com`. Finally, I modified WDS properties to stop listening on Port 67, resolving the port conflict between the DHCP and WDS services.

<img width="1020" height="841" alt="image" src="https://github.com/user-attachments/assets/356073f9-0929-4845-b7f4-48524da98e8c" />



## 5. Bare-Metal Deployment Validation
After resolving the routing handoff, I powered on a completely blank virtual machine with no hard drive OS or attached ISO media. The client successfully pulled an IP address, located the `wdsnbp.com` boot file via TFTP, and loaded the Windows Setup environment purely over the network, validating the end-to-end deployment pipeline.

<img width="877" height="347" alt="Screenshot 2026-06-22 194800" src="https://github.com/user-attachments/assets/be190c4d-4b57-4c6d-a19e-432b73337e97" />

<img width="1308" height="997" alt="Screenshot 2026-06-22 194826" src="https://github.com/user-attachments/assets/d2c2e471-1969-49bb-85ee-c99113407564" />

<img width="1176" height="918" alt="Screenshot 2026-06-22 194933" src="https://github.com/user-attachments/assets/5c4bebc3-43fa-4ab1-a2bc-093235937c54" />


