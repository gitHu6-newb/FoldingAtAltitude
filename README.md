# FoldingAtAltitude
A guide on how to upgrade your free GCP and Azure tiers and then set them up for FAH as well as remote monitoring.

Take advantage of GCP's free $300 credits and Azure's $200 credits in the fight against Covid19. The guide covers 3 months of GCP Nvidia T4 folding and 1 month of Azure Nvidia P100 folding, all on linux for greater PPD. Feel free to fold with a P100 on GCP but it will only last just under 1 month. Remember to Terminate your projects before your credits run out (and before your 30-day expiration date for Azure) in order to avoid getting a credit card charge!

# GCP guide
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
  [C. make VM(s)](#make-vm(s)) <br>
  [D. prep the VM](#prep-the-vm) <br>
  [E. install / config / monitor FAH](#install--config--monitor-fah) <br>
  [F. VM restart automation](#vm-restart-automation) <br>
  [G. check credits](#check-credits)
  [H. Azure Shutdown](#azure-shutdown) <br>

=====================================================================================

### GCP sign up and upgrade
1. We start with a gmail account. GCP will occasionally send you emails, but you can unsubscribe. If you want to use a different gmail account, you can. Create a new gmail now if that's what you need to do.

2. Then go to https://console.cloud.google.com
Sign in, fill out the credit card verification, and make sure to create the INDIVIDUAL account, (not the business one). [fig 1](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/gcpfree/fig01.gif) (GCP will send some emails, like "Account confirmation - Complete your GCP Profile" but you can ignore all these b/c it's just checkboxes for your interests in order to send you more mail)

3. After finishing sign up, click on "Compute Engine" [fig 2](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/gcpfree/fig02.jpg)
This will take some minutes for GCP to initialize on their backend.

4. When that's done, click the the top left corner 'hamburger,' which is the 3 horizontal lines to open the Navigation Menu. Let's pin commonly used items to the top of the menu. Scroll down and look for "Compute Engine," and click on the 'push-pin' that appears when you mouse over the space to the left of the '>' which should pin Compute Engine to the top the menu. Now scroll about 40 items down into the TOOLS section to pin "Cloud Scheduler." Now scroll back up and just click on "Billing."

5. On the right side of the Billing page, under the circular graphic displaying your credits, click on the button to Upgrade your account.
This could take a minute or two to take effect. You will receive an email saying "You've Upgraded." You can ignore it

### [Request Quota Increase](#gcp-guide)

6. Click the hamburger again, navigate into "IAM & Admin" and click on "Quotas"

7. Along the top there is a single 'filter table' drop down menu. Click it and type GPU

8. select the result for "GPUs (all regions)" which will list a Compute Engine API on the page. Mark its checkbox then click on "Edit Quotas" that's above it.

9. fill out your information in the open pane on the right and click next (any phone number will do). <br>
  I previously read that if you managed to get a VM with x4 T4, it was half price as x2 P100 yet PPD-wise performs about the same but I have not been able to ever get more than 1 GPU spot approved. Maybe one of you could get lucky? If you do, you might also have to request a quota increase on the specific region as well, i.e. filter the quotas by 'preemptible' this time, and select the one with T4. Then filter by location and choose either central1, east1 or west1. 

10. in the box for New Quota Limit for the GPUs (all Regions), you can try 4 if you're hopeful, otherwise just put 1. 

11. for the reason, write anyhing, like helping with Folding@Home, then submit request

12. Your email will say you'll get your answer within 2 business days, but most likey it will arrive in under 2 minutes. If you tried for 4 and get denied, request again, but this time just for 1 and it should get approved.


### [Make A VM](#gcp-guide)
13. click the hamburger and click on Compute Engine

14. click on "Create an Instance"

15. Name is up to you

16. Underneath "Add label" are Region & Zone. The cheapest Regions are: us-central1, us-east1, and us-west1. 
Zone: Since we're looking for T4, respectively you may choose a/b/f; c/d; or a/b.
(If you are looking for P100 instead, choose, respectively c/f; b/c; or a/b.

17. In Machine configuration change Machine type to "Custom" (it will be the very top item of that drop down list)
change the number of Cores to 1
change the Memory to 1.75
[fig 3](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/gcpupgr/fig_03.gif)

18. Click "CPU platform and GPU" then click on "Add GPU"
GPU type: select T4 (if selection not present, you'll have to change Zone from step (6))
Number of GPUs: 1 (unless you somehow were approved 4 GPUs, then use 4 and also change (17) to 4 cores

19. a couple lines below, double check to make sure Boot disk is set to Debian GNU/Linux 10 (buster)
if it's not that, then hit "Change" to select "Debian" and "Debian 10 buster"

20. towards the bottom click on "Management, security, disks, networking..."

21. scroll down to Metadata, then in "Key" type env
in "Value" type dev
[fig 4](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/gcpfree/fig04.gif)

22. for Availability policy >Preemptibility: change "Off(recommended)" to "On" (this option gives us 80-90% discounted rates compared to a dedicated setup)
[fig 5](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/gcpfree/fig05.gif)

23. click "Create" (although it says "you will be billed for this instance" it just means it's going to be using your free credits)

  Once you see a green circle check mark ![image](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/gcpupgr/greenchk.gif) next to the name, the VM is ready to put in GPU drivers and software.







### [Prepare the VM](#gcp-guide)
24. (The VM must be running, indicated by the green circle). On the right end of the table, click on "SSH" (the actual box that says SSH, not the drop down menu)
[fig 6](https://encouragingcleanamazonprchase.s3-us-west-1.amazonaws.com/gcpfree/fig06.gif)

  A new window should pop open, saying "Connecting..."

25. When it looks like it's waiting for you to type something in, copy and paste the below commands.
To paste into the black terminal window, try right clicking the mouse to find the paste option. Other methods are to hold SHIFT and press Insert
or CTRL SHIFT v
If you're not using chrome, there's a chance none may work as intended, so you'll have to type them.
```
sudo apt install wget
```
 when it says Do you want to continue? [Y/n] 
 press y and then ENTER 

```
wget download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.6/fahclient_7.6.13_amd64.deb
```

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
wget us.download.nvidia.com/tesla/410.104/NVIDIA-Linux-x86_64-410.104.run

chmod +x NVIDIA-Linux-x86_64-410.104.run
```
 if you're typing these out, after you write 'N' you can hit TAB and it will autocomplete
 (that command also doesn't output anything, so continue to next command)
 (also-also, if you opted for P100 instead of T4, you might be better off with this driver us.download.nvidia.com/tesla/450.51.05/NVIDIA-Linux-x86_64-450.51.05.run )

```
sudo ./NVIDIA-Linux-x86_64-410.104.run
```
  Screen will fill with a bunch of dots before changing to a progress bar. A minute later, there will be a "WARNING: nvidia installer was forced to guess..." Just hit ENTER for OK
 [fig 7]()
   The next "WARNING: Unable to find suitable destination to install 32-bit compatible libraries..." just hit ENTER to continue
   At "Installation... complete" Just hit ENTER for OK 
   You can check info on the GPU with command
```
nvidia-smi 
```
[fig 8]()




### [Install / Configure / Monitor FAH](#gcp-guide)



### [VM auto restart scheduling](#gcp-guide)




### [Checking Credits](#gcp-guide)



### [Shutdown](#gcp-guide)

50.

  a) Emergency - if you forgot all about checking your credits and it's been over 3 months... you're defintely getting charged
      i) When at console.cloud.google.com, on the left, click "Go to project settings"
     ii) towards the top click the SHUTDOWN button and follow the directions
    iii) click hamburger, click Billing. (you can click View Report to find how much you owe, but let's proceed with shut down)
     iv) Right side of page, click on "Manage"
      v) top-middle click on "CLOSE BILLING ACCOUNT" and follow the instruction

  b) A Gentle Shutdown - because researchers depend on the results of these WUs in order to build the next set of problems to fold, it's best to hit "Finish" on FAHControl so that once it finishes the current WU, it doesn't download another one and it pauses itself. Then that would be the ideal time to shut down like in (50)(a)

  c) if you're trying to squeeze out more WU with the few remaining credits, remember that:
      i) credits shown may be delayed by up to a day
     ii) try to make sure you have enough credits to finish an unexpectedly longer WU.
    iii) if you're waiting with the VM in a Stopped state to make sure credits gets updated, while you're not being charged for the VM unless it gets turned on, there are still small costs always accruing for the storage that everything sits on.
     iv) also, if you have VMs in the Stopped state, if you don't delete or edit your Cloud Scheduler Jobs, they will start your VM 
      v) if you removed your Cloud Scheduler Jobs and you decide to fold some more, you might need to recreate them again to handle preemptions
     vi) just because FAH is paused doesn't mean you're not getting charged. If the VM is on, it is costing you
    vii) if you use up all your credits, you're now paying out of pocket

The End.



==============================================================================


# Azure
Azure puts a 30-day time limit on the $200 free credits, and the clock starts ticking as soon as you sign up. So pay attention not only to your credits, but also your last free day, because if you've still got resources when your credits expire, you could start to get charged no matter how many credits you still had.

### [sign up and upgrade](#azure-guide)



### [Req quota increase](#azure-guide)






### [Make VM(s)](#azure-guide)





### [Prep the VM](#azure-guide)




### [Install / config / monitor FAH](#azure-guide)
We're still in the SSH session using CloudShell. To prevent FAH from starting before we finish GPU configs, we're going to cut its Internet access using the hosts file. 
```
sudo nano /etc/hosts
```
And at the bottom of that file add in:
(make sure there's no space before each 127...)
```
127.0.0.1      assign1.foldingathome.org
127.0.0.1      assign2.foldingathome.org
127.0.0.1      assign3.foldingathome.org
127.0.0.1      assign4.foldingathome.org
```
To exit nano, Cntl x
hit y then ENTER to save

continue copy pasting commands (and if you're manually typing, TAB can autocomplete filenames)
```
sudo dpkg -i fahclient_7.6.13_amd64.deb
```
Follow the prompts to enter in Username, Team, and Passkey (if you have them)

Choose option "full" [fig ?](clientfull)

Choose option "YES" for FAH automatically starting
You might see some red words on screen for errors, but ignore them.

Continue commands (and note that FAHClient is not the same as Fahclient)
```
FAHClient --send-pause
```
The next commands write what you wrote back to the screen, which is how you tell it was successful
```
FAHClient --send-command "options gpu=true"
```
```
FAHClient --send-command "options allow=YOUR.HOME.IP.ADDRESS"
```
This IP is the network from behind which you want to monitor the cloud WUs. Plenty of sites will tell you your IP, like https://www.whatsmyip.org/
```
FAHClient --send-command "options password=YourFAHRemotePassword"
```
[fig ?](fahcmd)

Restore the hosts file by placing a number sign (#) in front of the 4 entries you made [fig ?](hosts)
```
sudo nano /etc/hosts
```
Cntl x then y then ENTER to save

Manually configure the GPU slot for FAH by adding some info at the end of the config file to make it look like [fig ?](config)
```
sudo nano /etc/fahclient/config.xml
```
you can copy paste this into the config
```
  <slot id='1' type='GPU'>
    <paused v='true'/>
  </slot>
```
Cntl x then y then ENTER to save

```
sudo reboot
```
In 30-45 seconds this cloud client should be "Online" in your FAHControl, still paused, and you'll see a CPU and a GPU slot. Push "Fold" to get it going.
Careful not to accidentally move the Folding Power slide bar. It took me way too long to notice that's what I had done once.
If you need to make name/team/passkey changes and are having trouble doing it in FAHControl, SSH back in the VM and use FAHClient --send-command "options user=xxxx"
or team or passkey in place of user

Lastly if you ever get a stuck download, where it looks like it started downloading a new WU but then progress updates just stopped coming in, SSH in again and sudo reboot





### [VM restart automation](#azure-guide)
After running my own and 2 other family members' accounts, a GPU spot VM has never been evicted, and only 1 cpu-folding VM has been evicted only once, so this section is almost unecessary if you monitor through FAHControl regularly, but here it is anyway.

Type auto in the search bar at the top in the blue area and click on "Automation Accounts"

Add/Create an Automation Account and fill in the info. Name: you creative (I named mine autobot), but the rest need to be chosen from their respective drop down menus, corresponding to the VM you made earlier. i.e. using the vm command from the beginning as an example, that would make Resource group = myCloudFolding and Location = East US. Then click Create

When it lists your automation account, clicking on it opens a panel, then 12th item down click on Runbooks gallery [fig ?](auto2)

Click on "Start Azure V2 VMs" and then click on "Import" and OK [fig ?](auto3)

After importing it, click back to your automation Runbook gallery [fig ?](auto4)

Now click on "Runbooks" and then the new "StartAzureV2Vm" [fig ?](auto5)

Along the top, click on the "Edit" button

Then click on the "Publish" button [fig ?](auto6), confirm "Yes"

In the panel, click on Schedules, then "Add a schedule"

Select "Schedule | Link a schedule to your runbook"

"Create a schedule," Give it a name, set the Starts date and time, change Recurrance from "Once" to "Recurring," and in the figure I have it set to check every 2 hours, then click "Create" [fig ?](auto7)

No need for "Parameters and run settings" so just click on "OK" 
This schedule should restart any and all VMs, even those you later create, whether it be in the same or different Resource Groups. If for some reason you need different schedules for different VMs, you can specifically target names in "Parameters and run settings." Lastly, the "Jobs" blade is how you check what/when schedules have ran if you're curious.






### [Check credits](#azure-guide)
From the home screen, click on "Cost Management," or click the hamburger and almost at the bottom is Cost Management -- Careful, the bar displaying your credits here only focuses on the current month, and it is NOT a running total

A couple blades down in the left panel, click on the "Cost Analysis" blade. 

About top-middle, open the date drop down and select "Custom date range" to pick your start day (or before it) to make sure you get your true cumulative total. In the following figure, the solid green is the cost spent per day, and the light green being projected costs. Here I realized well past the halfway point that I was well under spending $200 in 30 days, by about $40, so I spun up an identical VM. The projection can only be accurate if it has 7 days of steady history to work with, and at the end there on the 19th I have used up that $40 so I deleted that VM, but that change is too soon for the projection to make accurate predictions. Paper math says it'll perfectly work out though, with the 20th ending at $194.30 and my last day less than 199.50, depending on how early I end it synchronized with the WUs.
[fig ?]()


### [Azure Shutdown](#azure-guide)
Go to your portal, https://portal.azure.com

Click on Subscriptions (the key icon), or type subscription in the search bar.

Click on your subscription name, then at the top you can click on "Cancel subscription" and follow the instruction to type something to confirm.

Hopefully you have enough time to use "Finish" on your FAHControl first to complete all current work units, as opposed to rushing to terminate before you start accruing charges :)

