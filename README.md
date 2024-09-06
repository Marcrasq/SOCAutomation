![image](https://github.com/user-attachments/assets/7229770a-2279-4a6f-9ab9-ab1caae29dbc)

This will consist of:
  - Windows 10 with Sysmon on a VM (VirtualBox)
  - Wazuh Server on a Digital Ocean Droplet
  - TheHive Server on a Digital Ocean Droplet

Objective: Detect the use of mimikatz

Steps: After intial setup is done of the servers. 
  1. Go to the ossec-agent directory and look for the OSSEC File. Here we will make a custom Log Analysis section with Sysmon in it. We want TheHive to capture Sysmon. For simplicity I have removed Application and Security Locations. Restart Wazuh Service
  ![image](https://github.com/user-attachments/assets/19b55af8-d2e7-4fdd-88e1-9ce741e0c77a)
  2. Go Back to Wazuh and here we can see it has captured events from the Windows Machine
  ![image](https://github.com/user-attachments/assets/7cfcdb8b-8a53-4407-a826-8445dbef461b)
  3. 
