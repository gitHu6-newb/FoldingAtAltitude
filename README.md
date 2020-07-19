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
  A. sign up and upgrade<br>
  B. request quota increase <br>
  C. make VM(s) <br>
  D. prepare the VM <br>
  E. install / configure / monitor FAH <br>
  F. VM auto restart scheduling <br>
  G. checking credits <br>
  H. Shutdown <br>

=====================================================================================

### GCP sign up and upgrade
1. We start with a gmail account. GCP will occasionally send you emails, but you can unsubscribe. If you want to use a different gmail account, you can. Create a new gmail now if that's what you need to do.

2. Then go to https://console.cloud.google.com
Sign in, fill out the credit card verification, and make sure to create the INDIVIDUAL account, (not the business one). [fig 1]() (GCP will send some emails, like "Account confirmation - Complete your GCP Profile" but you can ignore all these b/c it's just checkboxes for your interests in order to send you more mail)

3. After finishing sign up, click on "Compute Engine" [fig 2]()
This will take some minutes for GCP to initialize on their backend.

4. When that's done, click the the top left corner 'hamburger,' which is the 3 horizontal lines to open the Navigation Menu. Let's pin commonly used items to the top of the menu. Scroll down and look for "Compute Engine," and click on the 'push-pin' that appears when you mouse over the space to the left of the '>' which should pin Compute Engine to the top the menu. Now scroll about 40 items down into the TOOLS section to pin "Cloud Scheduler." Now scroll back up and just click on "Billing."

5. On the right side of the Billing page, under the circular graphic displaying your credits, click on the button to Upgrade your account.
This could take a minute or two to take effect. You will receive an email saying "You've Upgraded." You can ignore it

### [Request Quota Increase](#gcp-guide)

6. Click the hamburger again, navigate into "IAM & Admin" and click on "Quotas"

7. Along the top there is a single 'filter table' drop down menu. Click it and type GPU

8. select the result for "GPUs (all regions)" which will list a Compute Engine API on the page. Mark its checkbox then click on "Edit Quotas" that's above it.

9. fill out your information in the open pane on the right and click next. 
Here's a story: I previously read that if you managed to get a VM with x4 T4, it was half price as x2 P100 yet PPD-wise performs about the same but I have not been able to ever get more than 1 GPU spot approved. Maybe one of you could get lucky? If you do, you might also have to request a quota increase on the specific region as well, i.e. 4 gpu slots in us-central1

10. in the box for New Quota Limit, you can try 4 if you're hopeful, otherwise just put 1. 

11. for reason, write anyhing, like helping with Folding@Home, then submit request

12. Your email will say you'll get your answer within 2 business days, but most likey it will arrive in under 2 minutes. If you tried for 4 and get denied, request again, but this time just for 1 and it should get approved.


### [Make A VM](#gcp-guide)
13. click the hamburger and click on Compute Engine

14. click on "Create an Instance"

15. Name is up to you

16. Underneath "Add label" is Region. Choose 1 of 3 only: us-central1, us-east1, us-west1. 
Zone: any will do EXCEPT for -c
In Machine configuration change Machine type to "Custom" (it will be the very top item of that drop down list)

17. In Machine configuration change Machine type to "Custom" (it will be the very top item of that drop down list)
change the number of Cores to 1
change the Memory to 1.75

18. Click "CPU platform and GPU"
GPU type: select T4 (if selection not present, you'll have to change Zone from step (6))
Number of GPUs: 1 in our case

19. a couple lines below, double check to make sure Boot disk is set to Debian GNU/Linux 10 (buster)
if it's not that, then hit "Change" to select "Debian" and "Debian 10 buster"

20. towards the bottom click on "Management, security, disks, networking..."

21. scroll down to Metadata, then in "Key" type env
in "Value" type dev

22. for Availability policy >Preemptibility: change "Off(recommended)" to "On" (this option gives us 80-90% discounted rates compared to a dedicated setup)

23. click "Create" (although it says "you will be billed for this instance" it just means it's going to be using your free credits)

  Once you see a green circle check mark next to the name, the VM is ready to put in GPU drivers and software.







### [Prepare the VM](#gcp-guide)
24. (The VM must be running). On the right end of the table, click on "SSH" (the actual box that says SSH, not the drop down menu)

  A new window should pop open, saying "Connecting..."

25. When it looks like it's waiting for you to type something in, copy and paste the below commands.
To paste into the black terminal window, try right clicking the mouse to find the paste option. Other methods are to hold SHIFT and press Insert
or CTRL SHIFT v
If you're not using chrome, there's a chance none may work as intended, so you'll have to type them.
---
sudo apt install wget
---
(when it says Do you want to continue? [Y/n]
( press y and then ENTER ) 

---
wget download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.6/fahclient_7.6.13_amd64.deb
---

---
sudo apt-get update
---

---
sudo apt install clinfo
---

---
sudo apt-get install gcc make linux-headers-$(uname -r)
---
 ( Y for yes to continue)

---
wget us.download.nvidia.com/tesla/410.104/NVIDIA-Linux-x86_64-410.104.run

chmod +x NVIDIA-Linux-x86_64-410.104.run
---
 (if you're typing these out, after you write 'N' you can hit TAB and it will autocomplete)
 (that command also doesn't output anything, so continue to next command)

---
sudo ./NVIDIA-Linux-x86_64-410.104.run
---
 (Screen will fill with a bunch of dots before changing to a progress bar. A minute later...
 (There will be a "WARNING: nvidia installer was forced to guess..." Just hit ENTER for OK
 (The next "WARNING: Unable to find suitable destination to install 32-bit compatible libraries" 
 (  we don't care about 32bit libraries. ENTER to continue
 (Third and final screen, "Installation... complete" Just hit ENTER for OK )
 (You can check with command nvidia-smi )



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

### [sign up and upgrade](#azure-guide)<br>



### [request quota increase](#azure-guide) <br>






### [make VM(s)](#azure-guide) <br>





### [prepare the VM](#azure-guide) <br>




### [install / configure / monitor FAH](#azure-guide) <br>



### [VM auto restart scheduling](#azure-guide) <br>



### [checking credits](#azure-guide) <br>



### [Shutdown](#azure-guide)

