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
  B. request quota increase
  C. make VM(s) <br>
  D. prepare the VM
  E. install / configure / monitor FAH
  F. VM auto restart scheduling
  G. checking credits
  H. Shutdown

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

### Request Quota Increase

6. Click the hamburger again, navigate into "IAM & Admin" and click on "Quotas"

7. Along the top there is a single 'filter table' drop down menu. Click it and type GPU

8. select the result for "GPUs (all regions)" which will list a Compute Engine API on the page. Mark its checkbox then click on "Edit Quotas" that's above it.

9. fill out your information in the open pane on the right and click next. 
Here's a story: I previously read that if you managed to get a VM with x4 T4, it was half price as x2 P100 yet PPD-wise performs about the same but I have not been able to ever get more than 1 GPU spot approved. Maybe one of you could get lucky? If you do, you might also have to request a quota increase on the specific region as well, i.e. 4 gpu slots in us-central1

10. in the box for New Quota Limit, you can try 4 if you're hopeful, otherwise just put 1. 

11. for reason, write anyhing, like helping with Folding@Home, then submit request

12. Your email will say you'll get your answer within 2 business days, but most likey it will arrive in under 2 minutes. If you tried for 4 and get denied, request again, but this time just for 1 and it should get approved.

<align="right" [back to contents](#gcp-guide)>
### Make A VM
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


### [Install / Configure / Monitor FAH](#gcp-guide)



### [VM auto restart scheduling](#gcp-guide)




### [Checking Credits](#gcp-guide)



### [Shutdown](#gcp-guide)


