<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this showcase, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>Steps</h2>

<h3> 1. Create a resource group </h3>

  - In the Azure portal, you will start by clicking on "Resurce groups".
  <p align="center">
  <img src="https://i.imgur.com/QfyAlBl.png" height="80%" width="80%" alt="Create a resource group steps"/> 
  </p>

  - Once you see the page like the image below, go ahead and click on "Create".
 <p align="center">
  <img src="https://i.imgur.com/tRtZnw7.png" height="80%" width="80%" alt="Create a resource group steps"/>
  </p>

  
  -  In the box "Resource group" will be the name of your resource group. I chose mine to be RG-Lab-02.
  -  Then in "region" box,  will be the sever hosting our resource group. I chose South Central US since this is the clostest to me.
  -  Once you are done will out the boxes, click on "Review and create".
<p align="center">
  <img src="https://i.imgur.com/mpJPego.png" height="50%" width="50%" alt="Create a resource group steps"/> 
  </p>

  -  Alright! Once you click on "Create", you have successfully created a Resource group in Azure! Good stuff~!
   <p align="center">
  <img src="https://i.imgur.com/oM3IWzI.png" height="50%" width="50%" alt="Create a resource group steps"/> 
  </p>

<h3> 2. Create two Vitual machines. One in Windows 10 (21H2) and the other in Ubuntu Server 20.04. </h3>

  -  Go to the Search bar to type in vitual machines and the option should appear.
![Screenshot 2024-07-24 210807 r](https://github.com/user-attachments/assets/20a6aede-42e4-4558-a1bd-38ad0e3a160a)

  - If you see this page, good! You're on the right page. Go ahead and hit "Create" button to start on your first Vitual machine.
![Screenshot 2024-07-24 210842 r](https://github.com/user-attachments/assets/7479bc25-d2c6-460d-b241-22b296e559da)

  - A popup should appear once you hit the "Create" button similar to the image below.
  - Click on "Azure Vitual machine".

![Screenshot 2024-07-24 210857 r](https://github.com/user-attachments/assets/c5825af2-8daf-4eb9-80d6-97253fe810f2)

  - Under "Resource group" choose the one you have already created.
  - Pick your Virtual machine name.
  - For this VM, we are going to choose the Window 10 Pro (free servies eligible) for our image
> [!NOTE]
> Then for Region, this part is a little tricky because depending on the Region you choose, certain VM (virtual machines) are only  available to a specific "Region". You might have to adjust your Region to try and find a VM that is under $150~ per month.  Don't worry much about the VM's specs since we're not doing anying strenuous that will cause an issue in this tutorial. The reason we are trying to find a VM under $100 or somewhere close to this, is to save your free Azure credits if you happen to forget to delete your resource group once you're done with this tutorial. Don't worry, I'll make a reminder later in this tutorial.
  - Once you chosen your size, make your username and password.

> [!IMPORTANT]
> Remember to save the username and password some where! Because if you happen to forget, you are going to remake the VMs again.

![Screenshot 2024-07-24 213031](https://github.com/user-attachments/assets/708d7e57-d198-46d3-a6c5-38896558ec83)
![Screenshot 2024-07-24 213235](https://github.com/user-attachments/assets/7c09f24e-cbff-4df1-accf-6b16fbb9a117)

  - Check off the box on the bottom next to "I confirm I have an eligible window 10/11..."
  - Then click on "Review + create"
    
![Screenshot 2024-07-24 213254 r](https://github.com/user-attachments/assets/9e9b1a1c-b912-4784-888d-56cf3696baf1)


  -  Then once you see this image, you can finally see your VM being created in real time!
![Screenshot 2024-07-24 214050](https://github.com/user-attachments/assets/ad0ad9ae-8a14-4df7-9056-0c8258a343a3)


 <h3> 3. Going into our VMs </h3>
    
  -  A couple things to talk about this next step,
       - We're going to mostly use VM1, VM2, and VM1-nsg in this tutorial
       - VM1-nsg is VM1's firewall that we will configure to our liking
![Screenshot 2024-07-24 215259 r](https://github.com/user-attachments/assets/b80e119c-fb1e-4fd9-a8d8-0631b524edaf)


  -  We're going into VM1, here is all the infomation about this VM
> [!IMPORTANT]
> Save VM1 Public IP address and Private IP address on notepad or the same place you're saving both VM username and PW. We will need both IP address soon!
![Screenshot 2024-07-24 215322 r](https://github.com/user-attachments/assets/8cf2f063-0a9c-4a39-9e76-ed4e30d0cf8f)


  -  Finally going to remote access our VM!
  -  Go to your start menu and type in "Remote" and the app should appear
![Screenshot 2024-07-27 115710](https://github.com/user-attachments/assets/9e63bc80-81de-4280-824e-43fd96fffb43)

  - Here, we're going to put in VM1 Public IP address, and press "Connect" once you have done so. 
 ![Screenshot 2024-07-24 215603](https://github.com/user-attachments/assets/826701a2-15f4-4f31-b111-9c89794c05af)

  - A pop up will appear, just press "Yes" for this.
![Screenshot 2024-07-24 215626 r](https://github.com/user-attachments/assets/acb9aa71-ffca-4133-9944-dbd8e801daf2)

  - And we're in our VM!
  - The blue bar with the VM's IP address will indicate that you are in the VM.
  - Turn everything off and start your new VM! 
![Screenshot 2024-07-24 215707 r](https://github.com/user-attachments/assets/dbd31599-ed35-445c-a06f-78944cc9fea9)

 <h3> 4. Installing Wireshark and learning a little about this program. </h3>

  - Go to Mircrosoft Edge and download Wireshark.
  - We're going to download the Windows x64 Installer
![Screenshot 2024-07-24 215844 r](https://github.com/user-attachments/assets/9eb4f7a9-81de-45c0-903a-6e49a2de43eb)

  - Once you installed and have Wireshark running, press the button to boxed in red start the program.
![Screenshot 2024-07-26 212834 r](https://github.com/user-attachments/assets/76c6af2a-8923-455b-ab3b-81e8d2b9389c)

  - Here, we can see everything that goes on behind the scene of our network within our VM!
  - What cool about this is that in the red box, shows the different OSI layers.
      - Starting from the top, is the OSI physical layer 
      - Next is the Data link layer.
      - Next is the Network layer.
      - Finally is the Transport layer.
   ![Screenshot 2024-07-24 220617](https://github.com/user-attachments/assets/98b71168-75df-4821-9de7-b523b99e20a5)

  - Since we're now seeing EVEYTHING that is comming through the Ethernet, we're going to filter the traffic by typing in icmp. Icmp is the protocal that ping uses.
  - Ping is used for the connectivity to different host on the network.
![Screenshot 2024-07-24 220908 R](https://github.com/user-attachments/assets/96a2bb8c-dcab-417e-845f-34235ebc1372)

  -  To ping VM2, we're going to need the private IP address of VM2.
![Screenshot 2024-07-24 220740 r](https://github.com/user-attachments/assets/b4c2f31a-35b7-4331-abe1-6af60bf55845)

    
  - Go to your console command and type in ping then VM2's private address ash shown below
![Screenshot 2024-07-24 220939 r](https://github.com/user-attachments/assets/0088965b-9cec-4945-a95b-5ca63e11f868)

  - After you ping, you should see in the console command and Wireshark syncing with request and reply between both VMs.
![Screenshot 2024-07-24 220908 r](https://github.com/user-attachments/assets/5bbc350d-0df7-4d16-9749-e1e78125598a)
![Screenshot 2024-07-24 220939 r](https://github.com/user-attachments/assets/e052c980-782b-4af0-aaaa-2a02350bfc77)

  - Now we can ping between two VM, we're are going to block  
  - 
  - 
  - 
  - 
  - 

  







  - 
 


  
 

  
- Step 2

- Step 3
- Step 4

![image](https://github.com/user-attachments/assets/84dcdc12-2410-48ea-a3a8-b3c462fd8d33)

  

<h2>Actions and Observations</h2>

<p>

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
