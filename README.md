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
  3. After disabling Windows Defender and downloading Mimikatz, cd into the directory and run the mimikatz.exe file.
  ![image](https://github.com/user-attachments/assets/c723a673-17d7-4fd8-9085-321b74c342d3)

  4. Had an error where I couldn't see mimikatz, after some digging I foudnd that sysmon events didn't trigger any alerts because Wazuh does not log everything by default, it only logs things that a rule or alert is triggered. We need to configure it, went back to the         wazuh terminal and enabled in json to capture everything instead of only rules.
     
  5. After modifying the json, we can finally see our archives capturing mimikatz
     ![image](https://github.com/user-attachments/assets/05a63364-7f31-42ea-9430-480d4ba320f7)
     ![image](https://github.com/user-attachments/assets/55270ce2-7c9b-45ee-89e6-f1ef17d083cc)

  6. Now lets make a rule since its only logging the originalFileName, if the attacker were to change the name of mimikatz.exe, Wazuh will not catch it...
     ![image](https://github.com/user-attachments/assets/654e0e23-1190-4e32-9ccb-fe1ff1b37a9f)
     I had to make a custom rule which looks for mimikatz.exe and with an mitre ID of T1003 which is credential dumping.

  7. I decided to change the name of mimikatz to see if the custom rule solved the bypass, and it did. Wazuh was able to detect mimikatz
     ![image](https://github.com/user-attachments/assets/c5598e32-76a7-43c4-87d6-de644af655e1)

     ![image](https://github.com/user-attachments/assets/474060c7-2de4-46d4-ba1a-71ee11aa95ce)


