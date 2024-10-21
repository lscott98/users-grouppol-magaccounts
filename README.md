<p align="center">
<img src="https://i.imgur.com/aMMGyHQ.jpeg" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />

<h1>Creating Users, Group Policy, and Managing Accounts with Active Directory in Azure</h1>



<h2>Description</h2>
 This project will illustrate how to configure Remote Desktop for non-administrative users, automate user creation using PowerShell, and manage group policies. It will also address account lockouts and log monitoring to replicate a real-world IT environment. br/>
<br />


<h2>Environments and Utilities Used</h2>

- <b>Microsoft Azure</b>
- <b>Virtual Machines</b>
- <b>Remote Desktop Connection</b>
- <b>Active Directory</b>

<h2>Operating Systems Used </h2>

- <b>Windows Server </b>
- <b>Windows 10</b>

<h2>Project Walkthrough:</h2>

<p align="center">
To start, log into client-1 as the domain admin (jane_admin), using Remote Desktop Connection: <br/>


![image](https://github.com/user-attachments/assets/e2e2563c-6240-4f7e-8aaa-df1eae4ad90e)



To enable Remote Desktop for non-administrative users, right-click the Start button, select System, then on the right side, click Remote Desktop. Next, click Select users that can remotely access this PC.<br/>


![image](https://github.com/user-attachments/assets/fa9c220e-0a52-414c-9dc2-341f8e9f8274)



Here, enter "Domain Users" > Check Names > OK > OK. Now all domain users can access this client via Remote Desktop:  <br/>

![image](https://github.com/user-attachments/assets/0b302700-e9fc-4b30-be77-a83acc55e21a)



Next, you'll create multiple users using PowerShell. To begin, log into the Domain Controller (DC) as the admin user (jane_admin).<br/>

![image](https://github.com/user-attachments/assets/fdfd9d3c-8585-49de-983d-6cafffb89b34)



Once logged in, open Powershell ISE as an admin and start a new script:  <br/>


![image](https://github.com/user-attachments/assets/7b75aa8f-0e8a-4419-8615-6c50cf210d51)



Use Ctrl + S and save:  <br/>



![image](https://github.com/user-attachments/assets/131ab83a-a258-43ce-b080-a8c3804f40e0)





Now, paste the user creation script into the top section of PowerShell, then click the Run button, identified by the green play symbol in the top toolbar.<br/>




![image](https://github.com/user-attachments/assets/54809320-0ee8-44ac-9ef5-6b0bec5a9504)






PowerShell will execute the script, creating random users and placing them in the "_EMPLOYEES" Organizational Unit (OU) you created earlier. To verify, search for Active Directory Users and Computers > mydomain.com > _EMPLOYEES. From there, select a user and log into client-1 using their username and the default password set by the script, which is "Password1".<br/>


![image](https://github.com/user-attachments/assets/722fdaff-7ad7-4499-bf6f-d99194bc17c2)



Now, log out of Jane's account on client-1:  <br/>



![image](https://github.com/user-attachments/assets/c001665f-70db-41cc-81f7-be8dce7c8434)





You can now log back into client-1 using one of the newly created users, making sure to specify the domain context by adding "mydomain.com" before the username.<br/>




![image](https://github.com/user-attachments/assets/4194fa96-e639-424f-914f-61928288e3c7)



Once logged in, you can view a folder for the current user by navigating to File Explorer > C: > Users. You'll also notice folders for other users who have previously signed into this client. However, you won't be able to access those folders, as you don’t have the necessary permissions for them. <br/>





![image](https://github.com/user-attachments/assets/4c65b591-1d30-4a40-b19e-19facf3680af)






Now, log out of the current user, as the next step involves setting up an account lockout group policy. After configuring the policy, you'll attempt to trigger an account lockout for this user. This process will simulate how incorrect login attempts can lead to account security measures being enforced.<br/>


![image](https://github.com/user-attachments/assets/3ebf2c44-6cc0-4439-8715-9cde5f73ea41)





Go back to the DC and right click the start button > Run > type "gpmc.msc" to bring up the Group Policy Managment Console:  <br/>


![image](https://github.com/user-attachments/assets/bd25ba44-a463-4399-9a00-5e7f095e3f0a)





Expand Domains > mydomain.com > select Default Domain Policy:  <br/>


![image](https://github.com/user-attachments/assets/ba3112ae-356e-4263-a597-940f5cb206b6)



Under Computer Configuration, expand Policies > Windows Settings > Security Settings, then select Account Lockout Policy. This section allows you to define security settings that determine how many failed login attempts will trigger an account lockout, ensuring user accounts are protected from unauthorized access attempts.<br/>


![image](https://github.com/user-attachments/assets/2d69bdbb-b51f-4c24-8d8e-1c1826d7c65d)


In this section, select Account lockout threshold and set it to lock the account after 5 invalid login attempts. Ensure you click Apply to save the changes. This setting will help prevent unauthorized access by locking the account after multiple failed attempts.<br/>

![image](https://github.com/user-attachments/assets/9da67526-ce8d-4677-930b-e477d0eb5184)




You can verify the configuration by navigating to the group policy settings and scrolling down to review the account lockout policy, ensuring it reflects the changes you made.<br/>

![image](https://github.com/user-attachments/assets/8e98e35c-d11a-425c-aaaf-ddefb0fdcc81)


The policy will update automatically, but this process may take some time. To expedite the update on client-1, log in as the admin and run the command "gpupdate /force" in the command prompt. This command forces the group policy to refresh immediately.<br/>

![image](https://github.com/user-attachments/assets/cc78e1a6-0b7e-4ceb-a539-ff52cf861358)







![image](https://github.com/user-attachments/assets/00fb5b36-1315-4c38-a06e-9431f44f000d)

  

Now, we can sign out of client-1 and attempt to sign back into it with our user with an invalid password 5 times. A lockout message will appear:  <br/>



![image](https://github.com/user-attachments/assets/427b54c5-7965-4843-ae75-4e4109e1475d)








To unlock the user's account, return to the Domain Controller (DC) and navigate to Active Directory Users and Computers. From there, expand mydomain.com and locate the _EMPLOYEES Organizational Unit (OU). Double-click on the locked-out user account to open its properties. In the Account tab, find the option labeled Unlock account and check the box to unlock the account. This will allow the user to regain access after being locked out due to multiple failed login attempts.<br/>


![image](https://github.com/user-attachments/assets/2e23f696-cabc-4ad5-be9c-b9b720b94fce)


 





Now the user can sign into clinet-1 without any hassle :  <br/>



![image](https://github.com/user-attachments/assets/69483b27-e658-49d6-aea8-174b669d5cbf)




To disable users, go to the DC and in Active Directory Users and Computers > mydomain.com > _EMPLOYEES > right-click on user you want to disable > Disable:  <br/>




![image](https://github.com/user-attachments/assets/151db3a6-f207-47e9-b11f-d2af1ea15ade)



  If you log out of client-1 now and attempt to sign back in with the disabled account, you will see the following message: <br/>



![image](https://github.com/user-attachments/assets/9d053f5a-46ac-4af2-97a5-d681a4cdde34)




  

To re-enable the user account, return to the Domain Controller (DC), right-click on the user, and select Enable.<br/>



![image](https://github.com/user-attachments/assets/c1cc1b1a-94ad-4d51-b885-64c1ce94e9f8)



The user can now log into client-1, so proceed to do that. Once logged in, you will access some logs through the Event Viewer. To do this, search for Event Viewer in the Start search bar, then navigate to Windows Logs > Security. You may notice that you are unable to view these logs.<br/>



![image](https://github.com/user-attachments/assets/ee2e5e3c-129f-40fc-9297-ad7a3fb49a65)


![image](https://github.com/user-attachments/assets/069ddcd4-a71a-4239-a382-6e3dc37be31a)




Now, if you return to the Security log page, you'll be able to view the logs. Here we can see a bunch of different information like successful and failed log on attempts, who attempted them and from what IP address, along with the date and time they happened:<br/>



![image](https://github.com/user-attachments/assets/efe3fdee-c40c-4cba-999d-991ddab1154d)



  <br/>

<h2>Active Directory: Creating Users, Group Policy, and Managing Accounts in Azure is Now Complete </h2>
