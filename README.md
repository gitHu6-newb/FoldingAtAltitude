# FoldingAtAltitude
A guide on how to upgrade your free GCP and Azure tiers and then set them up for FAH as well as remote monitoring.

Take advantage of GCP's free $300 credits and Azure's $200 credits in the fight against Covid19. The guide covers 3 months of GCP Nvidia T4 folding and 1 month of Azure Nvidia P100 folding, all on linux for greater PPD. Feel free to fold with a P100 on GCP but it will only last just under 1 month. Remember to Terminate your projects before your credits run out (and before your 30-day expiration date for Azure) in order to avoid getting a credit card charge!

# GCP guide
  [A. sign up and upgrade](###gcp-sign-up-and-upgrade)<br>
  [B. request quota increase](###-Request-Quota-Increase)<br>
  C. make a VM<br>
  D. prepare the VM<br>
  E. install / configure / monitor FAH<br>
  F. VM auto restart scheduling<br>
  G. checking credits<br>
  H. Shutdown<br>
# Azure guide
  A. sign up and upgrade<br>
  B. request quota increase<br>
  C. make VM(s)<br>
  D. prepare the VM<br>
  E. install / configure / monitor FAH<br>
  F. VM auto restart scheduling<br>
  G. checking credits<br>
  H. Shutdown<br>

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


### Make A VM
13. click the hamburger and click on Compute Engine

14. click on "Create an Instance"

15. 
