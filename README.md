# FoldingAtAltitude  
(get it? because we're in the cloud)<br>

### A guide on how to upgrade your free GCP and Azure tiers and then set them up for FAH as well as remote monitoring.

Take advantage of GCP's free $300 credits and Azure's $200 free credits in the fight against Covid19. The guide covers 3 months of GCP Nvidia T4 folding and 1 month of Azure Nvidia P100 folding. Feel free to fold with a P100 on GCP but it will only last just under 1 month. Remember to Terminate your projects before your credits run out (and before your 30-day expiration date for Azure) in order to avoid getting a credit card charge!

# GCP guide (still under construction)
  [A. sign up and upgrade](#gcp-sign-up-and-upgrade) <br>
  [B. request quota increase](#request-quota-increase)<br>
  [C. make a VM](#make-a-vm) <br>
  [D. prepare the VM](#prepare-the-vm) <br>
  [E. install / configure / monitor FAH](#install--configure--monitor-fah) <br>
  [F. VM auto restart scheduling](#vm-auto-restart-scheduling) <br>
  [G. checking credits](#checking-credits) <br>
  [H. Shutdown](#shutdown)<br>
# Azure guide
  [A. sign up and upgrade](#sign-up-and-upgrade) <br>
  [B. request quota increase](#req-quota-increase) <br>
  [C. make VM(s)](#make-vms) <br>
  [D. prep the VM](#prep-the-vm) <br>
  [E. install / config / monitor FAH](#install--config--monitor-fah) <br>
  [F. VM restart automation](#vm-restart-automation) <br>
  [G. check credits](#check-credits) <br>
  [H. Azure Shutdown](#azure-shutdown) <br>

==================================================================================

### GCP sign up and upgrade (still under construction)
1. We start with a gmail account. GCP will occasionally send you emails, but you can unsubscribe. If you want to use a different gmail account, you can. Create a new gmail now if that's what you need to do.

2. Then go to https://console.cloud.google.com
Sign in, fill out the credit card verification, and make sure to create the INDIVIDUAL account, (not the business one). [fig 1](https://raw.githubusercontent.com/gitHu6-newb/FoldingAtAltitude/gfrmedia/fig01.gif) (GCP will send some emails, like "Account confirmation - Complete your GCP Profile" but you can ignore all these b/c it's just checkboxes for your interests in order to send you more mail)

3. After finishing sign up, click on "Compute Engine" [fig 2](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/gfrmedia/fig02.jpg)
This will take some minutes for GCP to initialize on their backend.

4. When that's done, click the the top left corner 'hamburger,' the 3 little horizontal lines, to open the Navigation Menu. Let's pin commonly used items to the top of the menu. Scroll down and look for "Compute Engine," and click on the 'push-pin' that appears when you mouse over the space to the left of the '>' which should pin Compute Engine to the top the menu. Now scroll about 40 items down into the TOOLS section to pin "Cloud Scheduler." Now scroll back up and just click on "Billing."

5. On the right side of the Billing page, under the circular graphic displaying your credits, click on the button to Upgrade your account.
This could take a minute or two to take effect. You will receive an email saying "You've Upgraded." You can ignore it

### [Request Quota Increase](#gcp-guide)

6. Click the hamburger again, navigate into "IAM & Admin" and click on "Quotas"

7. Along the top there is a single 'filter table' drop down menu. Click it and type GPU

8. select the result for "GPUs (all regions)" which will list the Compute Engine API on the page. Mark its checkbox then click on "Edit Quotas" that's above it.

9. fill out your information in the open pane on the right and click next (any phone number will do). <br>
  I previously read that if you managed to get a VM with x4 T4, it was half price as x2 P100 yet PPD-wise performs about the same but I have not been able to ever get more than 1 GPU spot approved. Maybe one of you could get lucky? If you do, you might also have to request a quota increase on the specific region as well, i.e. filter the quotas by 'preemptible' this time, and select the one with T4. Then filter by location and choose either central1, east1 or west1 to request 4. 

10. in the box for New Quota Limit for the GPUs (all Regions), you can try 4 if you're hopeful, otherwise just put 1. 

11. For the reason, write something like helping with Folding@Home, then submit request

12. Your email will say you'll get your answer within 2 business days, but most likey it will arrive in under 2 minutes. If you tried for 4 and get denied, request again, but this time just for 1 and it should get approved.


### [Make A VM](#gcp-guide)
13. click the hamburger and click on Compute Engine

14. click on "Create an Instance"

15. Name is up to you

16. Underneath "Add label" are Region & Zone. The cheapest Regions are: us-central1, us-east1, and us-west1. 
Zone: Since we're looking for T4, respectively you may choose a/b/f; c/d; or a/b.
(If you are looking for P100 instead, choose, respectively c/f; b/c; or a/b.

17. In Machine configuration change Machine type to "Custom" (it will be the very top item of that drop down list)<br>
  change the number of Cores to 1<br>
  change the Memory to 1.75<br>
 [fig 3](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/media/fig_03.gif)

18. Click "CPU platform and GPU" then click on "Add GPU"<br>
 GPU type: select T4 (if selection not present, you'll have to change Zone from step (6))<br>
 Number of GPUs: 1 (unless you somehow were approved 4 GPUs, then use 4 and also change (17) to 4 cores

19. a couple lines below, double check to make sure Boot disk is set to Debian GNU/Linux 10 (buster)<br>
 if it's not that, then hit "Change" to select "Debian" and "Debian 10 buster"

20. towards the bottom click on "Management, security, disks, networking..."

21. scroll down to Metadata, then in "Key" type env<br>
 in "Value" type dev<br>
 [fig 4](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/gfrmedia/fig04.gif)

22. for Availability policy >Preemptibility: change "Off(recommended)" to "On"<br>
 (this option gives us 80-90% discounted rates compared to a dedicated setup)<br>
 [fig 5](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/gfrmedia/fig05.gif)

23. click "Create" (although it says "you will be billed for this instance" it just means it's going to be using your free credits)

  Once you see a green circle check mark ![green circle image](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/gfrmedia/greenchk.gif) next to the name, the VM is running and ready.







### [Prepare the VM](#gcp-guide)
24. (The VM must be running, indicated by the green circle). On the right end of the table, click on "SSH" (the actual box that says SSH, not the drop down menu)
[fig 6](https://raw.githubusercontent.com/gitHu6-newb/FoldingAtAltitude/gfrmedia/fig06.gif)

  A new window should pop open, saying "Connecting..."

25. When it looks like it's waiting for you to type something in, copy and paste the below commands.<br>
 To paste into the black terminal window, try right clicking the mouse to find the paste option. Other methods are to hold SHIFT and press Insert<br>
   or CTRL SHIFT v<br>
 If you're not using chrome, there's a chance none may work as intended, so you'll have to type them.
```
sudo apt install wget
```
 when it says Do you want to continue? [Y/n] <br>
 press y and then ENTER 

```
sudo apt-get update
```

```
sudo apt install clinfo
```

```
sudo apt-get install gcc make linux-headers-$(uname -r)
```
  Y for yes to continue

```
wget download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.6/fahclient_7.6.13_amd64.deb
```
```
wget us.download.nvidia.com/tesla/410.104/NVIDIA-Linux-x86_64-410.104.run

chmod +x NVIDIA-Linux-x86_64-410.104.run
```
 if you're typing this one out, after you write 'N' you can hit TAB and it will autocomplete<br>
 (that chmod command also doesn't output anything, so just continue to the next command)<br>
 (also-also, if you opted for P100 instead of T4, you might be better off with this driver us.download.nvidia.com/tesla/450.51.05/NVIDIA-Linux-x86_64-450.51.05.run )

```
sudo ./NVIDIA-Linux-x86_64-410.104.run
```
  Screen will fill with a bunch of dots before changing to a progress bar. A minute later, there will be a "WARNING: nvidia installer was forced to guess..." Just hit ENTER for OK<br>
 [fig 7](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/media/fig_xerr.gif)<br>
   The next "WARNING: Unable to find suitable destination to install 32-bit compatible libraries..." just hit ENTER to continue<br>
   At "Installation... complete" Just hit ENTER for OK <br>
   Optional: You can check info on the GPU with command
```
nvidia-smi 
```
[fig 8](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/media/fig_smi1.gif)




### [Install / Configure / Monitor FAH](#gcp-guide)

26. We're still in the same terminal. We're going to use the hosts file to cut FAH from talking on the internet during setup so that it doesn't download a Work Unit (WU) before we're ready. Copy paste (or use the editor of your choice if not nano) to edit the hosts file
```
sudo nano /etc/hosts
```
27. Copy/paste (or write out) the below text to the bottom of your hosts file<br>
```
127.0.0.1	    assign1.foldingathome.org
127.0.0.1	    assign2.foldingathome.org
127.0.0.1	    assign3.foldingathome.org
127.0.0.1	    assign4.foldingathome.org
```
press Ctrl x to exit nano, <br>
press y for yes to save,<br>
press ENTER to overwrite that filename

```
sudo dpkg -i fahclient_7.6.13_amd64.deb
```
if you're typing these out, after you type 'f' you can hit TAB and it will autocomplete

28. Follow the prompts to fill out a FAH username, team #, and passkey (if you have it)<br>
Choose option "full" [fig 9](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/media/fig_clientfull.gif)<br>
Choose option "yes" for auto starts

29. There might be some "failures" on screen; don't worry. Continue with the below commands:
```
FAHClient --send-pause
```

The next ones will echo your values back to the screen, meaning all is good [fig 10](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/gfrmedia/fig07.gif)
```
FAHClient --send-command "options gpu=true"
```
```
FAHClient --send-command "options allow=YOUR.HOME.IP.ADDRESS"
```
 (i.e. if you're going to monitor the cloud FAH from a device in your home network, then use any device in your network to visit a website that tells you what your IP address is. Such as https://whatsmyip.org Then enter that IP in place of Your.Home.IP.Address)
```
FAHClient --send-command "options password=YourRemoteControlPassword"
```
Note: this is not the same as the Passkey. If you've already been using remote monitoring, you know what goes here; otherwise just make up a password now

30. Restore the hosts file by placing a # in front of the 4 lines you added so that it now looks like
```
#127.0.0.1     assign1.foldingathome.org
#127.0.0.1     assign2.foldingathome.org
#127.0.0.1     assign3.foldingathome.org
#127.0.0.1     assign4.foldingathome.org
```

the command was 
```
sudo nano /etc/hosts
```
Ctrl x to exit, Y and ENTER to save


31. Now we fix the config file of FAH
```
sudo nano /etc/fahclient/config.xml
```
 the bottom of that file looks like: 
```
  <!-- Folding Slots -->
  <slot id='0' type='CPU'>
    <paused v='true'/>
  </slot>
</config>
```
 We want it to look like: [fig 11](https://github.com/gitHu6-newb/FoldingAtAltitude/blob/media/fig_config.gif) <br>
 (Just change the 0 to 1 and the C in CPU to a G
```
  <!-- Folding Slots -->
  <slot id='1' type='GPU'>
    <paused v='true'/>
  </slot>
</config>
```
 Ctrl x to exit, Y then ENTER to save 
 
```
sudo reboot
```

32. You can close the terminal window now (Cntl d)

33. Add a Firewall Rule: (Still on the Compute Engine page), click on bottom row for "Setup Firewall Rules"

34. click "CREATE FIREWALL RULE"<br>
`     `	a) Name: fah<br>
`     `	b) Scroll down to Targets: choose "All instances in the network"<Br>
`     `	c) Source filter: "IP ranges"<br>
`     `	d) Source IP ranges: enter IP address that was used in step (29), and any additional if needed<br>
`     `	e) check the box for "tcp:" and enter in 36330<br>
`     `	f) click "CREATE"

35. 











### [VM auto restart scheduling](#gcp-guide)




### [Checking Credits](#gcp-guide)



### [Shutdown](#gcp-guide)

50.

  a) Emergency - if you forgot all about checking your credits and it's been over 3 months... you're defintely getting charged<br>
      i) When at console.cloud.google.com, on the left, click "Go to project settings"<br>
     ii) towards the top click the SHUTDOWN button and follow the directions<br>
    iii) click hamburger, click Billing. (you can click View Report to find how much you owe, but let's proceed with shut down)<br>
     iv) Right side of page, click on "Manage"<br>
      v) top-middle click on "CLOSE BILLING ACCOUNT" and follow the instruction

  b) A Gentle Shutdown - because researchers depend on the results of these WUs in order to build the next set of problems to fold, it's best to hit "Finish" on FAHControl so that once it finishes the current WU, it doesn't download another one and it pauses itself. Then that would be the ideal time to shut down like in (50)(a)

  c) if you're trying to squeeze out more WU with the few remaining credits, remember that:<br>
      i) credits shown may be delayed by up to a day<br>
     ii) try to make sure you have enough credits to finish an unexpectedly longer WU.<br>
    iii) if you're waiting with the VM in a Stopped state to make sure credits gets updated, while you're not being charged for the VM unless it gets turned on, there are still small costs always accruing for the storage that everything sits on.<br>
     iv) also, if you have VMs in the Stopped state, if you don't delete or edit your Cloud Scheduler Jobs, they will start your VM when you don't want them to<br>
      v) if you removed your Cloud Scheduler Jobs and you decide to fold some more, you might need to recreate them again to handle preemptions<br>
     vi) just because FAH is paused doesn't mean you're not getting charged. If the VM is on, it is costing you<br>
    vii) if you use up all your credits, you're now paying out of pocket<br>

The End.



==============================================================================


# Azure
Azure puts a 30-day time limit on the $200 free credits, and the clock starts ticking as soon as you sign up. So pay attention not only to your credits, but also your last free day, because if you've still got resources when your credits expire, you could start to get charged no matter how many credits you still had.

### [sign up and upgrade](#azure-guide)
1. Go to https://azure.microsoft.com

2. Click on "Portal" in the upper right corner <br>
   Use an email address tied to a Microsoft account to log in, or alternatively, click their "Create One" link to use some other email address which will associate it with a Microsoft account.

3. Once signed in, fill out info and go through the text message and credit card verification processes. (Nothing gets charged) [fig 1](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_1.gif)

4. When completed, you'll be at the Home screen [fig 2](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_2.gif) <br>
   Click on "Cost Management" in the lower right, then in the panel click "Go to billing account" [fig 3](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_3.gif)

5. Top middle, click the blue UPGRADE button [fig 4](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_4.gif)

6. Chose the Free option for a suppport plan: the "No Technical Support" [fig 5](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_5.gif) <br>
  Then click on "Upgrade" in the lower left <br>
  You might still see the Upgrade button at the top after you just upgraded, so just ignore it. Time to request more quotas.
  
  

### [Req quota increase](#azure-guide)
We are just a couple CPU threads short of our ideal to fold with 2 P100's to burn through the $200 within 30 days if you are lucky enough to have consistently low pricing, so we're going to ask for a tad more, which Azure usually grants, just so that option is readily available should you need it.

7. Click the hamburger and select the very bottom option (MS calls these 'blades') "Help + support"

8. In the left panel click on "+ New support request"

9. In Basics:
   - The Issue type = "Service and subscription limits (quotas)"
   - Quota type = "Compute-VM (cores-vCPUs) subscription limit increases" (it's alphabetical, so scroll down to the C's)

10. Click "Next: Solutions>>" which actually takes you to Details:
    - click the link for "Enter details" to open a right panel [fig 6](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_6quo.gif)
      - Location = EastUS
      - Type = Spot
      - New vCPU Limit = 12
    - click "Save and Continue" to finish the Details section
      - Severity = keep it at C
      - Preferred contact = choose either email or phone
      - Click "Review + create>>"
      - Click the blue "Create" button
 
 (I have noticed EastUS to be the cheapest, followed by WestUS2, and in last place SouthCentralUS. After mid-July 2020 I noticed making Spot (preemptible) GPU VMs in West2 and SouthCentral got blocked or something, so you might see the same if you tried non East locations, which is why this guide focuses on East. Perhaps in the future the block will get lifted)

11. Check your email for the approval which should come within 2 minutes. 


### [Make VM(s)](#azure-guide)

12. Click the CloudShell icon in the blue area at the top, the first icon right of center, looks like >_ inside a square. <br>
[fig 7](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_cli.gif) <br>
This guide used Bash, so select "Bash"<br>

13. "You have no storage mounted," click "Create" to make it

14. Once it's like this with the cursor blinking, you're ready to copy and paste the below commands into that window one at a time and hit ENTER [fig 8](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_cli2.gif) Use the mouse to right click and paste; or the keyboard shortcut: hold Shift and press Insert

```
az group create --location eastus --name myCloudFolding
```
"myCloudFolding" is a customizable name for your Resource Group, just remember if you change it, you'll have to change it everywhere else it appears
```
az vm create -n AzureFolding -g myCloudFolding --image debian --generate-ssh-keys --size Standard_NC6s_v2 --priority Spot --max-price 0.30 --storage-sku StandardSSD_LRS
```
"AzureFolding" is the name of the VM, which you also can change. (the above command will take about 2 minutes to process) <br>
"max-price" sets a limit just in case demand causes a huge spike in pricing, and if passed, will preempt the VM (otherwise you'd be out of credits in days). Reasonable pricing is around 22 cents/hr, so worst case scenario this rides just under the max price all month, so **make sure you check credits at least once, 4 days before your 30-day expiration date to make sure you don't go over the limit.**




### [Prep the VM](#azure-guide)

15. During this wait, from the computer you wish to monitor cloud folding with, find its IP address by visiting a website such as https://whatsmyip.org <br>
You will need that number (I'll refer to as Your.Home.IP.Address) very soon, but first (still from your intended monitoring computer) find the FAH icon in the system tray (maybe hidden in the lower right corner), right click it and open the Advanced Control. This is FAHControl. [fig 9](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_con.gif)<br>
In the left panel at the bottom, click on "Add" which will open a configuration window for a connection to your cloud VM. Name it whatever you want, and for the password make something up unless you're already using one for your other clients (note: this is different from the passkey). [fig 10](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_con2.gif)

16. Back at your Azure CloudShell, if it has finished, it's displaying some text. The 2nd to last line should be "publicIpAddress":"xx.xx.xx.xx"<br>
Take this address and put it in the Hostname/IP entry of your FahControl configuration. With all boxes filled out, click Save. (Don't lose the address yet, there's 1 more use for it.)

17. Back at the CloudShell, to create the firewall at the cloud and allow it to talk to your home FAHControl, copy paste
```
az network nsg rule create --name fah --nsg-name AzureFoldingNSG --priority 2000 --resource-group myCloudFolding --destination-port-ranges 36330 --protocol Tcp --source-address-prefixes YOUR.HOME.IP.ADDRESS
```
Remember, if you customized your VM or Resource Group name, make sure to use them here as well. Note that "NSG" is attached to the end of the VM name, so if you changed yours to "xxxVM," the above command must be written with xxxVMNSG. <br>
Lastly, replace "YOUR.HOME.IP.ADDRESS" with the numbers you saved earlier from some website that told you your IP.

18. Now you can connect via SSH into your VM using CloudShell easily. Use that "publicIpAddress" to replace xxxx below
```
ssh xxxx
```
If you lost that address, you can click hamburger, scroll halfway down to "Virtual machines" and then click on your VM's name. <br>
In the right most column will be the Public IP address

19. When it makes a connection to an IP for the first time, at the prompt type the full word: yes and hit ENTER

 We're now in the VM via SSH session using CloudShell. To get nvidia drivers working:
```
wget us.download.nvidia.com/tesla/450.51.05/NVIDIA-Linux-x86_64-450.51.05.run
```
```
sudo apt-get update
```
```
sudo apt install clinfo
```
At the prompt press y then ENTER to continue
```
sudo apt-get install gcc make linux-headers-$(uname -r)
```
At the prompt press y then ENTER to continue
```
chmod +x NVIDIA-Linux-x86_64-450.51.05.run
```
 (if you're typing these out, after you write 'N' you can hit TAB and it will autocomplete)<br>
 (that command also doesn't write anything to the screen, so just continue to the next command)
```
sudo ./NVIDIA-Linux-x86_64-450.51.05.run
```
  (A bit different from GCP) Screen will fill with a bunch of dots before changing to a progress bar. A minute later... <br>
  There will be a "WARNING: nvidia installer was forced to guess..." Just hit ENTER for OK <br>
  It'll ask you to "install 32-bit compatible libraries?" <br>
    Move the white over to the "No" option and then hit ENTER [fig 11](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_nv.gif) <br>
  Finally, "Installation... complete" Just hit ENTER for OK )




### [Install / config / monitor FAH](#azure-guide)
20. We're still in the VM via SSH session using CloudShell. Download FAH
```
wget download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.6/fahclient_7.6.13_amd64.deb
```

 Unlike GCP, we have more than enough CPU cores here so we don't need to mess with the hosts file.

21. continue copy pasting commands (or if you're manually typing, TAB can autocomplete filenames)
```
sudo dpkg -i fahclient_7.6.13_amd64.deb
```
Follow the prompts to enter in Username, Team, and Passkey (if you have them)

Choose option "full" [fig 12](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_clientfull.gif)

Choose option "YES" for FAH automatically starting<br>
You might see some red words on screen for errors, but ignore them.

22. Continue commands (and note that FAHClient is not the same as Fahclient)
```
FAHClient --send-pause
```
23. The next commands return what you write back to the screen, which is how you tell it was successful [fig 13](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_fahcmd.gif)
```
FAHClient --send-command "options gpu=true"
```
```
FAHClient --send-command "options allow=YOUR.HOME.IP.ADDRESS"
```
This IP is the network from behind which you want to monitor the cloud FAH. Plenty of sites will tell you your IP, like https://www.whatsmyip.org/
```
FAHClient --send-command "options password=YourFAHRemotePassword"
```

24. Manually configure the GPU slot for FAH by adding some info at the end of the config file to make it look like <br>
[fig 14](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_config.gif)
```
sudo nano /etc/fahclient/config.xml
```
That opened the file in an editor. <br>
you can copy paste this into the config at the proper place
```
  <slot id='1' type='GPU'>
    <paused v='true'/>
  </slot>
```
To exit nano, hold Ctrl and press x<br>
It asks if you want to save; press y then ENTER to save

```
sudo reboot
```
and close the CloudShell

In 30-45 seconds this cloud client should be "Online" in your FAHControl, still paused, and you'll see a CPU and a GPU slot. Push "Fold" to get it going. If you don't see the GPU slot, re-examine the `config.xml` file again, by opening CloudShell, press the up arrow key and you can ssh again to try step (24) once more.<br>
Careful not to accidentally move the Folding Power slide bar away from "full." It took me way too long to notice that's what I had done once.
If you need to make name/team/passkey changes and are having trouble doing it in FAHControl, SSH back in the VM and use `FAHClient --send-command "options user=xxxx"`<br>
or team or passkey in place of user

Just in case somebody forgoes a monitoring program, that person would have to SSH in again to take FAH off the pause state with `FAHClient --send-unpause`<br>
The current logfile would be then viewed with `watch tail /var/lib/fahclient/log.txt` <br>
Then wait for the progress %'s to start climbing for BOTH lines that have 0xa7 and 0x22 in them for decent confidence that all is working.<br>
(To exit that mode, hit Ctrl c )<br>
Every 2 - 6 hrs a WU should complete, which you can check by entering the name you picked at https://apps.foldingathome.org/cpu (typical delay ~ 10 min)

Lastly if you ever get a stuck download, where it looks like it started downloading a new WU but then progress updates just stopped coming in for over 10 minutes, SSH in again and `sudo reboot`<br>
How do you tell? In FAHControl there's a "Logs" tab. Every several seconds you'll see "Download X%" until you get "Download complete" so if you see it got to some % but it's been over 10 minutes with no "Download complete" or other % update, then it's stuck and needs a reboot.





### [VM restart automation](#azure-guide)

25. Type auto in the search bar at the top in the blue area and click on "Automation Accounts" [fig 15](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_auto1.gif)

26. Add/Create an Automation Account and fill in the info. Name: you create (I named mine autobot), but the rest need to be chosen from their respective drop down menus, corresponding with the VM you made earlier, i.e. using the vm command from the beginning as an example, that would make Resource group = myCloudFolding and Location = East US. Then click Create

27. When it lists your automation account (may have to refresh), clicking on it opens a panel, then 12th item down click on Runbooks gallery [fig 16](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_auto2.gif)

28. Click on "Start Azure V2 VMs" and then click on "Import" and OK [fig 17](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_auto3.gif)

29. After importing it, click your automation Runbook gallery [fig 18](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_auto4.gif)

30. Click on "Runbooks" and then the new "StartAzureV2Vm" [fig 19](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_auto5.gif)

31. Along the top, click on the "Edit" button

32. Then click on the "Publish" button [fig 20](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_auto6.gif), confirm "Yes"

33. In the panel, click on Schedules, then "Add a schedule"

34. Select "Schedule | Link a schedule to your runbook"

35. Click "Create a new schedule," Give it a name, set the Starts date and time, change Recurrance from "Once" to "Recurring," and set for 1 per hour, then click "Create" [fig 21](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_auto7.gif)

36. No need for "Parameters and run settings" so just click on "OK" 
This schedule should restart any and all VMs, even those you later create, whether it be in the same or different Resource Groups. If for some reason you need different schedules for different VMs, you can specifically target a VM by name and/or resource group in "Parameters and run settings," but be aware that this one will still restart all resources unless you put in names or resource groups here. <br> Lastly, the "Jobs" blade is how you check what/when schedules have ran if you're curious.






### [Check credits](#azure-guide)
37. From the home screen, click on "Cost Management," or click the hamburger and almost at the bottom is Cost Management -- Careful, the bar displaying your credits there only focuses on the current month, and it is NOT a running total, so if you cross over into a new month, watch out for that.

38. A couple blades down in the left panel, click on the "Cost Analysis" blade. 

39. About top-middle, open the date drop down and select "Custom date range" to pick your start day (or before it) to make sure you get your true cumulative total. [fig 22](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/azupgr/fig_cm.gif) <br>
In that figure, the solid green is the cost already spent (true amount delayed about a day), and the light green is the projected cost. The Azure algorithm will need 7 days of steady history to make accurate projections, so any changes will take days to stabilize projections. My typical price for 1 NC6s_v2 P100 VM was $5.22 per day, and that would leave quite a bit of unused credits after 30 days, so spinning up a 2nd NC6s_v2 VM for 5 - 8 days solves that problem, depending on how much your second VM's price fluctuates. In any case, it might be a good idea to set up Budget Alerts to send yourself emails when you pass certain thresholds.
  -  Budgets (right underneath the Cost Analysis blade in the panel)
     - "+ Add" button near top
     - Reset period: I'd go with "Yearly" to avoid the costs resetting if you cross-over to a new month or new quarter
     - Put in your budget of 200
     - The next page you can set the alert thresholds, as many as you want, i.e. at 25%, 50%, 75%, 90%, 95%... (These have a few hrs delay too, so give yourself leeway)
     - Enter the email at which you wish to receive these alerts. 

_Important Note_: Azure Billing goes by [UTC](https://time.is/UTC), so if your credits are set to expire on the 30th and i.e. you're USA West coast (UTC-7) anything you use past 5pm on the 29th will be invoiced after your credits have expired.

If you decide to make a 2nd VM, you can repeat steps (14) - (24) with something like this for step (14)
```
az group create --location eastus --name myCloudFolding2
```
```
az vm create -n AzureFolding2 -g myCloudFolding2 --image debian --generate-ssh-keys --size Standard_NC6s_v2 --priority Spot --max-price 0.30 --storage-sku StandardSSD_LRS
```
That makes shutting down this second VM easy when the time comes with `az group delete -n myCloudFolding2 --no-wait` but check your credits often to stay on top of things<br>
Don't forget step (17) also requires a slight adjustment with "AzureFolding2NSG" and "myCloudFolding2"



### [Azure Shutdown](#azure-guide)
40. Go to your portal, https://portal.azure.com

41. enter CloudShell via the >_ icon
```
az group delete -n myCloudFolding --no-wait
```
If you custom named your Resource Group, use that instead of myCloudFolding<br>
press y to confirm and ENTER, and then you can close the CloudShell

42. Click on Subscriptions (the key icon), or type subscription in the search bar.

43. Click on your subscription name, then at the top you can click on "Cancel subscription" and follow the instruction to type something to confirm.

Hopefully you have had enough time to use "Finish" on your FAHControl first to complete all current work units, as opposed to rushing to terminate before you start accruing charges :) It's just nicer on the progress of science that way, but it's not mandatory.

