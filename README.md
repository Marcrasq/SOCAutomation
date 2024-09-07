![image](https://github.com/user-attachments/assets/c7613c70-5d90-41c8-8f61-6b9a3530266a)![image](https://github.com/user-attachments/assets/7229770a-2279-4a6f-9ab9-ab1caae29dbc)

This will consist of:
  - Windows 10 with Sysmon on a VM (VirtualBox)
  - Wazuh Server on a Digital Ocean Droplet
  - TheHive Server on a Digital Ocean Droplet

Objective: Detect the use of mimikatz using Wazuh

Steps: After intial setup is done of the servers. 
  1. Go to the ossec-agent directory and look for the OSSEC File. Here we will make a custom Log Analysis section with Sysmon in it. We want TheHive to capture Sysmon. For simplicity I have removed Application and       
  Security Locations. Restart Wazuh Service.

  ![image](https://github.com/user-attachments/assets/19b55af8-d2e7-4fdd-88e1-9ce741e0c77a)
  
  2. Go Back to Wazuh and here we can see it has captured events from the Windows Machine.
    ![image](https://github.com/user-attachments/assets/d6f63935-34fc-4bad-83c2-644294995856)
 
  3. After disabling Windows Defender and downloading Mimikatz, cd into the directory and run the mimikatz.exe file.
    ![image](https://github.com/user-attachments/assets/c723a673-17d7-4fd8-9085-321b74c342d3)

  4. Had an error where I couldn't see mimikatz, after some digging I foudnd that sysmon events didn't trigger any alerts because Wazuh does not log everything by default, it only logs things that a rule or alert is triggered. We need to configure it, went back to the         wazuh terminal and enabled in json to capture everything instead of only rules.
     
  5. After modifying the json, we can finally see our archives capturing mimikatz.
     ![image](https://github.com/user-attachments/assets/05a63364-7f31-42ea-9430-480d4ba320f7)
     ![image](https://github.com/user-attachments/assets/55270ce2-7c9b-45ee-89e6-f1ef17d083cc)

  6. Now lets make a rule since its only logging the originalFileName, if the attacker were to change the name of mimikatz.exe, Wazuh will not catch it...
     I had to make a custom rule which looks for mimikatz.exe and with an mitre ID of T1003 which is credential dumping.
     ![image](https://github.com/user-attachments/assets/654e0e23-1190-4e32-9ccb-fe1ff1b37a9f)
     

  7. I decided to change the name of mimikatz to see if the custom rule solved the bypass, and it did. Wazuh was able to detect mimikatz.
     ![image](https://github.com/user-attachments/assets/c5598e32-76a7-43c4-87d6-de644af655e1)

     ![image](https://github.com/user-attachments/assets/474060c7-2de4-46d4-ba1a-71ee11aa95ce)

Last Objective: Connect to a SOAR which is Shuffle and sending an alert to a case management system like theHive and an Email to a SOC Analyst.

Workflow: 
  1. Mimikatz sends an alert to Shuffle using a Webhook
  2. After receiving the Mimikatz alert the SHA256 Hash will be extracted from the file.
  3. Then using Virustotal the reputation score will be checked.
  4. Then the information will be sent over to theHive to create an Alert
  5. Lastly, an Email will be send to a SOC analyst to begin investigation.

Steps:
1. After setting the hook into the wazauh cli file. I had to configure Shuffle to get the SHA256 from the Mimikatz file alert
![image](https://github.com/user-attachments/assets/b5492481-5783-4dd2-a7c0-5ce2b7092a87)

2. Next is to connect the VirusTotal API to Shuffle. Here we had to configure it to send the Hash to Virustotal then return data back. Here it shows how many scanners found Mimikatz malicious.
![image](https://github.com/user-attachments/assets/db747392-6cec-41c4-acbc-b15dde2e6d83)

3. Next I need to send this details to theHive which then an alert will be created for case management. Setup Shuffle to send alert to theHive with the date, computer and user. Here I setup the alert going to be sent.
![image](https://github.com/user-attachments/assets/64ca6580-c077-4874-aed6-298ed19f94ac)

4. Boom was able to get it through to theHive after running into some trouble with the formatting in Shuffle.
![image](https://github.com/user-attachments/assets/22cba805-8764-4fb1-aa2d-fbc1e6bd3a0a)
![image](https://github.com/user-attachments/assets/74b40d57-2420-4b14-a4b5-b1f9f28f684a)

5. Now we got the alerts to send, we need to get the emails to send next. The first image is our workflow so far, we are making progress and the next image will show the email arriving at my inbox, not one you want to expect.
   ![image](https://github.com/user-attachments/assets/b75ea7d3-44c5-41d7-915c-f8afdbaabc52)
   ![image](https://github.com/user-attachments/assets/422c078a-3fba-412d-95de-1fba34c851d6)
   ![image](https://github.com/user-attachments/assets/7ef65725-19f4-42b9-9ec0-1bc154e86d6d)

7. Thats it. Next couple of lines ill be working on automating a block feature for outside connections trying to SSH in, I got this idea when I was testing the lab and since I did not set a firewall I got scanners hitting my box, so thats my next step.
   
   
