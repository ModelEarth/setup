## Updates

#### Environmental Tools and Form Components

State recycling directories, EPA input-output widgets, zip code industry lists.  

#### Deployment Updates

2022 - Update content editing UI. Rotate images based n camera metatag. "Additional Dates" editing remains on event list page.
2021 - Setup for AWS hosting using EC2 virtual machine and RDS SQL data.  
2020 - Streamlining server migration.  
2019 - Updates for deployment from Github. 

#### Cloudflare HTTPS Certs

2018 - Switch to free CloudFlare CDN proxies with free LetsEncrypt certs for https security.

#### Email Summary Updates

2017 - Email summaries of exceptions are now sent to admins once every 30 minutes containing the remote IPs and the counts per IP address.  Added to adjust for bursts of emails during high traffic.  

For PageName, we've added the highlighted line. If the script_name ends with a trailing slash, as in “/events/”, we return an empty string rather than “events”.  

For IContains, we now check for an empty string as well as null. If the string had been empty, IContains would have returned 0 as the index, and therefore true, rather than false as would be expected.  


#### Custom Page URLs

2016 - Added support for custom page URLs for improved search indexing and short URLs.  

#### Static Page Generation Updates

2015 - Pages generated from multiple queries are now stored in static folders for fast loading.  

Also see: [EE Additions](https://componentcore.com/ee/additions) (developer account required)  

<br>


# Steps for Configuring and Hosting on AWS

<a name="server"></a>

<!--Build a dev site locally or host on a server.  The following uses .NET 4.6.

Instruction below are for: Vista - Windows 7/10 / Windows Server 2008/2016 and forward.  
Commented out: XP - Windows XP / Windows Server 2005  
-->
Links: <a href="https://console.aws.amazon.com/ec2/v2/home?Instances#Instances:" target="_blank">Windows Instances EC2</a> | [RDS](https://console.aws.amazon.com/rds/home) | [AWS Billing Summary](https://console.aws.amazon.com/billing/home)

## AWS Windows Server Setup - EC2

EC2 (Elastic Compute Cloud) provides virtual server hosting.  
[Access EC2 with Microsoft Remote Desktop](https://www.freecodecamp.org/news/ec2-with-microsoft-remote-desktop/)  
In [AWS Management Console](https://aws.amazon.com/console/), Choose Services, then EC2. In the sidebar, click Instances.  

Set up [billing monitoring](https://console.aws.amazon.com/cost-management/home?#/anomaly-detection/overview?activeTab=subscriptions) before proceding under [Billing Preferences](https://console.aws.amazon.com/billing/home?#/preferences) or start with the free tier.
<!--
Simple Notifications
https://console.aws.amazon.com/sns/v3/home?region=us-east-1#/subscriptions
-->

**The cost is over 60% more if SQL Server reside on the EC2 Windows server due to the large size required.**  
Instead, use a medium-sized EC2 instance and create a medium-sized RDS instance for your data.  

[To save over $150 per month on your medium virtual server, migrate EC2 to the Reserved Model](https://danielmiessler.com/blog/saved-ec2-bill-5-minutes-switching-reserved-instance/) - A one-year reserved plan for the EC2 portion is $374 ($34/month). The way to get to Reserved Billing is counterintuitive. You have to pretend that you’re buying a new instance, of the "Reserved" type, with the exact same attributes as the one you want to convert. When you purchase, AWS finds your running On Demand instance and converts it to a Reserved instance. In billing you should see that reflected. We chose "Only show offerings that reserve capacity" and used the Availability Zone listed under the networking tab. [View settings](img/steps/reserved-instance.png).  

$47.22 - The RDS SQL database (based on a 3-year prepay of $1,700)  
$24.86 - EC2 Virtual Cloud Windows Server - Web Traffic (based on a 3-year prepay of $895)  
$65.00 - Average monthly AWS fees beyond prepays (Web Traffic, Data transfers, Offsite Backup Transfer)  
$9.99 - Crashplan Offsite Backup  
_____
$147.07 - Total AWS Monthly plus Offsite Backup

### Get the Public IP

In the EC2 list, slide to the right and get the IPv4 DNS.  
Click the row to see the public IPv4.  

Later you'll set a permanent IP since it will otherwise change when the instance is restarted.


### Elastic IP Address Setup and Review

Login to <a href="https://console.aws.amazon.com/">AWS Console</a> and select <a href="https://console.aws.amazon.com/ec2/v2/home?#Addresses:">EC2 Elastic IPs</a> to view the public DNS IPv4 addresses that are mapped to the private IPs you use in IIS.  

If Elastic IPs aren’t used, recycling or stopping/starting the instance will cause new IPs to be assigned to the server. This will not only cause websites to become inaccessible but also prevent connections to the server using Remote Desktop. Until Elastic IPs are activated, you'd have to use a different ip address each time the instance is restarted and the dns for each website would have to be updated.  


### Add Additional IP Addresses

Reference: <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html">AWS EC2 User Guide</a> 

Note that by avoiding entering IP addresses in IIS, you can reuse one IP with multiple sites.  

### Allocate Elastic IPs

[AWS elastic-ip-addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)

### Configure Windows Operating System to recognize the new IPs

<a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html">AWS EC2 User Guide</a>  


## AWS - Add Volumes - D: websites and E: Databases  

Use the following steps to add two volumes on the AWS server, one for the websites and another for the databases.

[Create a volume](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-creating-volume.html). Be sure to select the same Availability Zone as the instance that the volume will be attached to. Other than the size, use the default values. Be sure to assign a name to the volume so it can be identified in the AWS console application.  

[Attach the volume to an instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-attaching-volume.html). Use the suggested Device Name.

[Make the volume available to Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-using-volumes.html). You will see a prompt to initialize the disk. Use the default values to do this. Use the recommended Drive letter (D:, E:, etc.) or select another one. Be sure to assign the volume the same name that was used when creating the volume.

Once the drives have been set up, you should be able to see them in Windows Explorer.


[Post an issue](https://github.com/modelearth/setup/) if you'd like additional assistance with AWS EC2 Windows Server setup.   

## Add more disk space to an EBS Volume

If an EBS volume is running low on disk space, you can increase the volume size using the EC2 Management Console.

1. Connect to the [Amazon EC2 Console](https://console.aws.amazon.com/ec2)
1. Select the instance that contains the volume that you want to expand.
1. Select the Storage tab
1. Click on the Volume ID link for the volume that you want to expand.
1. Click on the Actions dropdown.
1. Click on the Modify Volume menu item. A dialog box will open.
1. Enter the new size in the the Size field.
1. Click on the Modify button and accept the confirmation prompt that appears.
1. A success (or failure) message will then be displayed that indicates that the modification process has started. Click OK to close it.
1. It can take a half hour or more for the increased space to be added to the volume, depending on the new size. The volume state will change from "In-use" to "In-use - optimizing ([percent complete]). You can click the refresh button periodically to see the progress. Note that the volume can still be accessed from Windows Explorer using a Remote Desktop connection.
1. Once the modification process has been completed, the Windows operating system will then need to be updated to recognize the additional space.
    1. Open a Remote Desktop connection to the instance.
    1. Open an administrator command prompt.
    1. Run the diskmgmt.msc command to open the Disk Management application.
    1. Right click the volume that was expanded and select the Extend Volumne menu item.
    1. Click Next. A dialog will display that shows the amount of new space that can be added to the volume.
    1. Click Next and then Finish to save the changes. The new volume size should now be visible in the Disk Management application as well as Windows Explorer and Windows Settings -> System -> Storage.

Reference: [Expand the EBS root volume of your EC2 Windows instance](https://aws.amazon.com/premiumsupport/knowledge-center/expand-ebs-root-volume-windows/)


## Setup AWS Backup

AWS Backup can be used to backup RDS, EC2, EBS (Elastic Block Storage - C drive: 50 GB, D drive: 75 GB), and other resources.

We use a third party cloud backup service (CrashPlan.com) to backup portions of EBS volumes (for storage of uploaded images), Documents & Settings and User folders.  

We also back EC2 monthly on AWS the last day of the month (which includes the EBS C and D drives), and use a 6-month retention period to purge older backups. Notifications can be setup to send emails when a scheduled backup fails. (See below.)

The steps below describe how to setup AWS Backup for RDS.  

**RDS Backups:**
- RDS automated backups are [enabled](img/database/RDS-7day-backup.png) for point-in-time recovery with a retention period of 7 days.
- We also run a weekly RDS Backup which is retained for 6 months.  

1. Connect to [AWS Backup > Backup plans](https://console.aws.amazon.com/backup/home?#/backupplan)  
1. Enable Services
    1. Select My Account -> Settings in the navigation pane.
    1. Click on Configure Resources.
    1. Enable EC2, EBS, and RDS. Disable the other resources.
1. Configure a backup plan for an Amazon RDS database
    1. Under My Account -> Backup plans, click Create Backup plan.
    1. Select Build a new plan, or if desired, Start with a Template, and then select a template. You can then customize the template settings in the new plan after it has been saved. The steps below assume you selected Build a new plan.
    1. Enter a name for the Backup plan: 
    1. Configure a Backup rule. Additional rules can be added after the initial plan has been saved.
        1. Enter a name for the Backup plan: RDS-Backup
        1. Optionally enter backup plan tags.
        1. Select the RDS backup vault if it exists, otherwise create a backup vault for RDS. This will allow you to group related backups together such as a vault for EBS and another for RDS.
            1. Click Create new Backup vault.
            1. Enter a new Backup vault name: RDS
            1. Select the default encryption key.
            1. Optionally enter Backup vault tags.
            1. Click Create Backup vault.
            1. On the Create Backup plan page, select the RDS backup vault.
        1. Select the Backup frequency: Weekly
        1. Select the days for the backup to occur: Sunday
        1. Do not check "Enable continuous backups for point-in-time recovery (PITR)" since that is already configured for the RDS instance.
        1. Select the Customize backup window option and enter the following options below:
            1. Backup window start time: 8:00 AM UTC (corresponds to 3:00 AM EST)
            1. Start within: 3 hours
            1. Complete within: 8 hours
            1. Transition to cold storage: Never
            1. Retention period: 6 Months
            1. Copy to destination: Leave blank
    1. Advanced backup settings
        1. Windows VSS: Leave unchecked.
    1. Click Create plan
1. Repeat the previous step to create backup plans for EC2 and optionally, EBS. Create separate backup vaults for EC2 and EBS.


Reference: [Amazon RDS Backup & Restore using AWS Backup](https://aws.amazon.com/getting-started/hands-on/amazon-rds-backup-restore-using-aws-backup/)

### Setup Notifications for AWS Backup

This section describes the steps needed in order for AWS Backup to notify email receipients when a backup has failed. You can also set this up to notify you when a backup occurs and whether it was successful or not.

1. Create an SNS topic to send AWS Backup notifications to.
    1. Open the [Amazon SNS console](https://console.aws.amazon.com/sns).
    1. From the navigation pane, choose Topics.
    1. Click Create topic.
    1. Under Type, select the Standard option.
    1. Enter a Name: AWS-Backup-Notifications
    1. Enter an optional Display Name if using SMS (text) notifications.
    1. Under Encryption, select the Disable encryption option (the default).
    1. Under Access Policy, use the default settings. This will be updated in later steps.
    1. Under Delivery retry policy (HTTTP/S) use the default settings.
    1. Under Delivery status logging, use the default settings.
    1. Add any optional tags.
    1. Choose Create topic.
    1. Select the topic you just created.
    1. Under the Details section, copy the value for the ARN (Amazon Resource Name). You will need this value for later steps.
    1. Above the Details pane, choose Edit.
    1. Expand Access policy.
    1. Replace the existing JSON with the following and update the Resource value with the Topic ARN that you copied previously:
    ```
    {
        "Version": "2008-10-17",
        "Id": "__default_policy_ID",
        "Statement": [
          {
            "Sid": "__default_statement_ID",
            "Effect": "Allow",
            "Principal": {
                "Service": "backup.amazonaws.com"
            },
            "Action": "SNS:Publish",
            "Resource": "Enter the topic ARN here"
          }   
        ]
    }
    ```
    18. Click Save changes
   
1. Configure your backup vault to send notifications to the SNS topic.
    1. [Install](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) the AWS Command Line Interface (AWS CLI) on the AWS EC2 instance. Note that on the install page, you will need to click a series of links to get to the actual install page which is the following for CLI Version 2: [Install AWS CLI Version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).
    1. [Configure](#configuringtheawscommandlineinterfacecli) AWS CLI which will allow you to use AWS CLI.
    1. Using the AWS CLI, run the put-backup-vault-notifications command with --backup-vault-events set to BACKUP\_JOB\_COMPLETED. Refer to the [Configuring the AWS Command Line Interface (CLI)](#configuringtheawscommandlineinterfacecli) section for more information. Replace the following values in the example command:

        <b>--profile:</b> Optional, specify the name of the CLI profile to use. If not specified, the default profile is used.
        <b>--backup-vault-name:</b> the name of your backup vault (Case sensitive)
        <b>--sns-topic-arn:</b> the ARN of the SNS topic that you created

    ```
    aws backup --profile profilename put-backup-vault-notifications --backup-vault-name examplevault --sns-topic-arn arn:aws:sns:eu-west-1:111111111111:exampletopic --backup-vault-events BACKUP_JOB_COMPLETED
    ```
    4. Run the get-backup-vault-notifications command to confirm that notifications are configured:

    ```
    aws backup --profile profilename get-backup-vault-notifications --backup-vault-name examplevault
    ```
    The output should be similar to the following:

    ```
    {
        "BackupVaultName": "examplevault",
        "BackupVaultArn": "arn:aws:backup:eu-west-1:111111111111:backup-vault:examplevault",
        "SNSTopicArn": "arn:aws:sns:eu-west-1:111111111111:exampletopic",
        "BackupVaultEvents": [
            "BACKUP_JOB_COMPLETED"
        ]
    }
    ```
    5. Repeat steps 3 and 4 to create notifications for the EC2 and EBS backup vaults. 

1. Create an SNS subscription that filters notifications to backup jobs that are unsuccessful.
    1. Open the [Amazon SNS console](https://console.aws.amazon.com/sns/).
    1. From the navigation pane, choose Subscriptions.
    1. Choose Create subscription.
    1. For Topic ARN, select the SNS topic that you created.
    1. For Protocol, select Email.
    1. For Endpoint, enter the email address where you want to get email notifications about failed backup jobs.
    1. Expand Subscription filter policy.
    1. In the JSON editor, enter the following:

    ```
    {
        "State": [
           {
                "anything-but": "COMPLETED"
           }
        ]
    }
    ```
    9.  Choose Create subscription.
    10. The email address that you entered in step 6 will receive a subscription confirmation email. Be sure to confirm the SNS subscription.

1. Test emails for notifications.
    To test that you will receive an email notification when a backup fails, create two on-demand backups and then cancel one of them. You should only receive a notification for the backup that was cancelled.
    1. Create an on-demand backup
        1. Open the [AWS Backup Console](https://console.aws.amazon.com/backup/home?region=us-east-1).
        1. On the navigation pane, click Protected Resources.
        1. Click Create on-demand backup
        1. Select the Resource Type: RDS
        1. Select the Database (Instance) name.
        1. Select Create backup now if not already selected.
        1. Select the Retention Period: 1 Day
        1. Select the Backup vault: RDS
        1. Select the IAM role: Default (selected by default)
        1. Optionally add tags
        1. Click Create on-demand backup
        1. Repeat the previous steps to create a second on-demand backup.
    1. Cancel one of the on-demand backups
        1. On the AWS Backup Console, clik Jobs on the navigation page.
        1. Click on one of the jobs that you want to stop.
        1. In the backup job's Detail pane, click the Stop button.
        1. You should receive just one failure notification from AWS. The notification can be sent immediately or within an hour or so.
        1. If you do not receive a notification, in the SNS subscription, try clearing the Subscription filter policy and then create another on-demand backup job and allow it to complete successfully. You should then receive a success notification from AWS.

Reference: [Get notifications for AWS Backup jobs that failed](https://aws.amazon.com/premiumsupport/knowledge-center/aws-backup-failed-job-notification/)

## Setup AWS Command Line Interface (CLI)

In order to use the AWS Command Line Interface (CLI), you will need to install and configure it. The sections below describe how to do that.

### Install AWS Command Line Interface (CLI)

 [Install](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) the AWS Command Line Interface (AWS CLI) on the AWS web server instance. Note that on the install page, you will need to click a series of links to get to the actual install page which is the following for CLI Version 2: [Install AWS CLI Version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).

### Configuring the AWS Command Line Interface (CLI)

1. After installing the CLI, you will need to configure it. Before that can be done, you will need to [create an Administrators Group and an Administrator Identity and Access Mangagement(IAM) user](#creatinganadministratoriamuserandgroup) if not already created.
1. Open the [IAM Console](https://console.aws.amazon.com/iam/)
1. In the navigation page, click Users.
1. Click Add users.
1. Enter the username: aws\_sdk\_user, aws\_cli\_user, or something similar
1. Under Select AWS access type, select Access key - Programmatic access. This will allow this IAM user to use the AWS CLI.
1. Click Next: Permissions.
1. Select Add user to group if not already selected.
1. Under the Add user to group section, Select the Administrators group. If you don't see this group refer to the first step to add it.
1. Click Next: Tags
1. Optionally add tags.
1. Click Next: Review.
1. Verify the user group memberships to be added to the new user. When you are ready to proceed, click Create user.
1. In the Users pane, Click the user you just created.
1. Select the Security credentials tab.
1. Click Create access key
1. To view the new access key pair, choose Show. You will not have access to the secret access key again after this dialog box closes. Your credentials will look something like this:
    <ul>
        <li>Access key ID: AKIAIOSFODNN7EXAMPLE</li>
        <li>Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY</li>
    </ul>
1. Copy the Access key ID and Secret access key and store it somewhere secure or click Download .csv file.
    <ul>
        <li>Store the keys in a secure location. You will not have access to the secret access key again after this dialog box closes.</li>
        <li>Keep the keys confidential in order to protect your AWS account and never email them. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon.com. No one who legitimately represents Amazon will ever ask you for your secret key.</li>
    </ul>
1. Click Close to close the access key dialog.
1. Now that you have the Access key and Secret access key, you can run aws configure
    1. Open an administrator command prompt on the web server.
    1. Run aws configure and substitute the actual Access key and Secret access key for the example keys below. The command will prompt you for each of the values. The optional --profile argument creates a collection of settings stored under the name that you specify, such as aws\_sdk\_user. If --profile is not used, then the default profile will be updated instead. For updating system settings such as for aws backup, it may be best to create a named profile rather than using the default profile. That way, you can use the default profile when running the aws cli as yourself rather than using the credentials of the named profile. If you use the CLI to update system settings as yourself, and your account is deleted later, those settings may become disabled (not sure if that actually happens or not, but it makes sense). So, create a default profile for your IAM account (don't specify the --profile option) and create a profile for a "system" user such as aws\_sdk\_user (specify the --profile option).
    
    ```
    aws configure --profile profilename
    AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
    AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    Default region name [None]: us-east-1
    Default output format [None]: json
    ```

References:
[Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
[Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)

## Creating an Administrator IAM User and Group

It is strongly recommended that the Root user be used only in the few circumstances where Root user access is required and an IAM User for routine adminstrative and other tasks is used instead.

1. Open the [IAM Console](https://console.aws.amazon.com/iam/) as the Root user. You will need to log out and log back in if you are currently logged in as an IAM user.
1. Allow IAM Users and Roles to access billing information. By itself, this setting will not allow IAM users access to the billing pages. Additional permissions must be granted to allow that. But, with this option turned off, no IAM users can access the billing information even if they have administrative access.
    1. On the navigation bar, choose your account name, and then choose My Account.
    2. Scroll down the page and for the IAM User and Role Access to Billing Information, click Edit. You must be signed in as the root user for this section to be displayed on the account page.
    1. Select the check box to Activate IAM Access and choose Update.
    1. On the navigation bar, choose Services and then IAM to return to the IAM console.
1. Use the following steps to create an Administrator user and Administrators group.
    1. In the IAM Console navigation pane, choose Users and then click Add users.
    1. For User name, type Administrator.
    1. Select the check box for AWS Management Console access, select Custom password, and then type your new password in the text box.
    1. By default, AWS forces the new user to create a new password when first signing in. You can optionally clear the check box next to User must create a new password at next sign-in to allow the new user to reset their password after they sign in.
    1. Click Next: Permissions.
    1. Click Add user to group.
    1. Click Create group.
    1. In the Create group dialog box, for Group name type Administrators.
    1. Select the check box for the AdministratorAccess policy.
    1. Click Create group.
    1. Back on the page with the list of user groups, select the check box for your new user group. Choose Refresh if you don't see the new user group in the list.
    1. Click Next: Tags.
    1. Optionally add tags.
    1. Click Next: Review.
    1. Verify the user group memberships to be added to the new user. When you are ready to proceed, click Create user.
    1. (Optional) On the Complete page, you can download a .csv file with login information for the user, or send email with login instructions to the user.

Reference: [Creating your first IAM admin user and user group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)

## Install and Configure IIS

[See IIS install notes](iis/)  

Launch Internet Information Services (IIS).  
You can create one IIS entry for multiple sites that use the same IP addreess.  

<strong>Set the IIS root directory to D:\Web</strong> (or other site root folder)<br>
Create a Bin folder in this new site root.  Copy in existing DLLs, or run a script to build.<br>

<!--
Make sure a primary website is viewable in a browser while on the machine itself.  Some networks may require changes to the firewall settings.  PDF generation requires that the machine can load content via the domains it hosts.<br>
-->


<h2>Application Pool Setup</h2>
In IIS, you can optionally create a new Application Pool for each website.<br>

<ul>
<li>Set the .NET Framework Version to ASP.NET 4.0.</li>

<li>Assign the App Pool in IIS under Basic Settings</li>
</ul>

Didn't do the following yet on new server:
<ul>
<li>Set up each Application Pool to run under the Network Service Identity rather than the default ApplicationPoolIdentity Identity. This allows write permissions to be set for common folders that are used by all websites, such as for mail or upload folders, to be set up using one identity. (Else the mail pickup folder will display error: Access to the path is denied.)</li>
</ul>

Might not need to do this:
<ul>
<li>For 64-Bit operating systems - may need to set "Enable 32-Bit Applications" to True  
-- For COM dlls used by the asp pages.</li>
</ul>


<h2>Certify the Web - for Windows Servers</h2>

Certifytheweb is a Windows installer for letsencrypt.org certs.  
The free version supports up to 3 certs per server, with one cert per IIS site.  

For Windows server, download app onto server from: [certifytheweb.com](https://certifytheweb.com/)  

Install the app. There is no account login or password requested.  
Click "New Certificate" and register yourself as a new contact.  
You'll be prompted to enter an email address.  
Didn't choose "Use Staging (test) Mode"  

Note: Assigning an IP does not seem necessary. All IPs can be omitted in IIS.
To remove: Associate a domain to an IP in IIS. You can use the same IP with multiple IIS sites.  
To replace with: Don't add an IP address. This will give you flexibility if moving the IIS setting to another machine.

Note that the generated cert will have no IP in IIS.  

In IIS, use only domain names.  No IPs.  
The IIS entries can be all https. Avoid using http in IIS.  
(Confirming this. Not sure if needed when first adding cert.)  

<p>Check "Require Server Name Indication".  Allows the bindings for a site to use different certificates. Also, if left unchecked, IIS will possibly update other
bindings for different sites that are also unchecked with the same certificate as the site being updated. IIS will display a warning if that situation occurs:
<i>"At least one other site is using the same HTTPS binding and the binding is configured with a different certificate. Are you sure that you want to reuse this HTTPS
binding and reassign the other site or sites to use the new certificate?"</i>
In some cases, that may be desirable so that you don't have to select the certificate for each site's bindings. For other cases, it may end up updating a binding with the wrong certificate. (CloudFlare hides the cert, so no impact unless the site is not using Cloudflare.)</p>

"Not Found" occurs initially when https match is not present for a domain in IIS.  
To solve, add an https 433 binding in IIS matching the domain.  
After adding once, Cloudflare will retain.

It's fine to leave multiple 443 entries in each IIS site.

You can point additional domains at the IP using a proxy in Cloudflare.  
With AWS EC2, the external IP differs from the IP on the machine.  

CloudFlare reuses one cert for all domains pointed at the IIS site. [Cloudflare setup](https://model.earth/localsite/start/cloudflare)  
(So unchecked additional domains that were also in the IIS site.)

"Certify the Web" icon will appear on desktop.  
Simply opening will update the certs. (Worked when IP range changed, but does't work when moving to a new machine.)  

A hidden folder called .well-known will be generated at the root of your website.  
to show the certificate authority (CA) that the requester controls the domain. 
Not sure if this can be removed later.

---  

If a failure requesting the new cert occurs, in Cloudflare under "SSL/TLS" change from "Full" to "Off (not secure)". After aquiring cert, turn "Full" back on in Cloudflare to prevent "redirected you too many times" error. And reactivate "Always Use HTTPS" under SSL/TLS > Edge Certificates.  (This works when hstspreload.org is already set for domain.)<!-- note that hstspreload.org has not been turned on for areamarket -->  

Another option is to use a domain that is not already using https at Cloudflare.
(Need to test to confirm this it the case.)  
<!-- 525 error handshake fail occurs before cert added for a domain requiring https in Cloudflare -->

<h2>Not using CloudFlare?</h2>

For domains that don't use CloudFlare or are not proxied through CloudFlare, ensure the following:
1. Requests using HTTP are redirected to HTTPS.
1. Add the following content security policy response headers for the Live and Review sites. Coordinate with the customer if part of the website is running on a different server for the Strict-Transport-Security header. Don't add the preload option until confirmed that all pages can be loaded using HTTPS:
    1. View the website settings in IIS.
    1. Open the HTTP Response Headers feature under the IIS section
    1. Add the following headers if not already present:<br />
        Name: Content-Security-Policy<br />
        Value: upgrade-insecure-requests<br /><br />
        Name: Strict-Transport-Security<br />
        Value: max-age=63072000; includeSubDomains; preload
    1. When requestng a page, you should see these responses header for the page request in the browser network tab.

<h2>Install and Configure .NET, Run Windows Update</h2>

Get the latest updates related to the .NET Framework - 
<a href="https://dotnet.microsoft.com/download/dotnet-framework/">Download .NET Framework</a> using the Web Installer.<!--
OLD NOTE (We're switching to .NET 4.8, use link above)  
If not already installed, install the <a target="_blank" href="http://www.microsoft.com/en-us/download/details.aspx?id=17718">.NET Framework 4.0 (Standalone Installer)</a> or the <a target="_blank" href="http://www.microsoft.com/en-us/download/details.aspx?id=17851">.NET Framework 4.0 (Web Installer)</a>.  
-->  
The Web Installer is easy to use and ensures that any prerequisites are installed before installing the framework.  
Install the .net 4.8 developer pack on the aws server. (If it initially has 4.7)  


If a .aspx page is not recognized, don't add a Mime type. Tried the following, but not resolved:   

<!--
Try skipping this, it has no effect:<br>
To complete the install, go to the Windows Control Panel, choose "Turn Windows features on or off."  In the Windows Features dialog box, click Internet Information Services to install the default features. Expand the Application Development Features node and click ASP.NET [your new version] to add the features that support ASP.NET. Also install lower versions of ASP.NET such as ASP.NET 3.5. This will ensure that older programs will still be able to run. A prompt to assignm roles may pop up first. You can choose "Role-based or feature based installation". When you get to the "Features" section, expand the .NET Framework [your version] and <b>choose ASP.NET [your version]</b>. Choose the restart if needed option.  
-->

If the website has an error when loading a Core page and the error is from one of the dlls listed below, you may need to add some folders
to the Global Assembly Cache (GAC). This is located in the C:\Windows\Microsoft.NET\assembly\GAC_MSIL folder. Copy the folders in the
"Tools\ASP.NET GAC Folders" folder to the GAC folder and try to reload the Core page. Then verify that a Net page loads.
    
    Microsoft.Web.Infrastructure


Run the utility C:\DreamStudioUtilities\CreateEventLogSource to create the event log source:  
CreateEventLogSource ManagementSuiteExceptionEmail

Also run for ManagementSuiteMail:  
CreateEventLogSource ManagementSuiteMail

[Add Web server roles (ISAPI)](img/steps/add-web-server-roles.gif)  

<!--

Check which framework version is in the %windir%\Microsoft.NET\Framework64 directory and run the command with that version:  

%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_regiis.exe -ir

XP
   
Install the <a target="_blank" href="http://www.microsoft.com/downloads/details.aspx?familyid=0856eacb-4362-4b0d-8edd-aab15c5e04f5&displaylang=en">
    .NET Framework 2.0 Redistributable Package:</a>

For laptops viewing localhost with ASP.NET 2.0, IIS also requires running:<br>
C:\WINDOWS\Microsoft.NET\Framework\<version>\aspnet_regiis -i<br><br>

<div class="projecttime" style="display:none"><strong>Time:</strong> 2-4 hrs depending on updates needed</div><br>
-->

<h2>Ensure Windows Update runs automatically</h2>

Instances launched from the latest Windows Server AMIs might show a Windows Update dialog message stating "Some settings are managed by your organization." and "Your organization has turned off automatic updates"

Manually checking for updates still works however.

To get rid of the message and enable automatic updates, follow the instructions on the [Windows Common Messages](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/common-messages.html#some-settings-managed-by-org) page.

Also do the following:
1. In Windows Update, click Advanced options
    1. Enable "Give me updates for other Microsoft products when I updates Windows."
    1. Enable "Automatically download updates, even over metered connections (charges may apply)." 
    1. Ensure Update notifications are disabled.
1. In Windows Update, click Change active hours
    1. Select a start time of 6:00 AM and an end time of 12:00 AM and then click Save. Note, after selecting a time, click the checkmark at the bottom of the dropdown list to save your settings and close the dropdown list, or press the X character to discard your changes.

<a name="database"></a>

## Setup RDS for SQL Databases

You'll be able to access the database by remote connecting to the EC2 machine, then use Microsoft SQL Server Managment Studio to connect to RDS.

<!--about/setup/Database.aspx'>Database Setup-->


**The cost is over 60% more if SQL Server reside on the EC2 Windows server due to the large size required.**  
Instead, use a medium-sized EC2 instance and create a medium-sized RDS instance for your data.  

IMPORTANT - Change the timezone from UTC to your local timezone when you create a new RDS instance.  

1. On the [RDS page](https://console.aws.amazon.com/rds/home), click Create Database.
1. Select Standard Create  
1. Select Sql Server engine  
1. Select Sql Server Web Edition  
1. Select the latest version (by default)
1. Enter the DB Identifier and Credentials  
1. Select the DB instance class: Select Burstable classes (includes t classes) and then select the db.t3.medium (2 vCPUs, 4 GB RAM, 2085 Mbps) option 
1. Select the Storage type: General Purpose (SSD)  
Enter the Allocated Storage: 100 GB (20 GB default) 100 GB per the recommendations for performance reasons. Burstability credits rebuild faster with larger storage volumes. Increases cost by about $10/mo.  
1. Storage autoscaling: Checked (by default)  
1. Maximum storage threshold: 1000 GB (the default value)    
1. Default VPC (vpc-95d988ed) - only one available  
1. Select the subnet group: default  
1. Select Public Access: No (Setting to Yes would be a security risk. See the setup instructions below to allow the EC2 instance to connect to the RDS instnce)  
1. Select the VPC Security Group – Select Choose existing option - default (this is the default option) or Select Create new option to create a new security group just for RDS. For future instances, it may be better to create a new VPC Security Group so a more descriptive name can be used other than "default"  
1. Select the Availability Zone: us-east-1f (Same as the EC2 instances)
1. Under Additional configuration, use the default value for the Database port  
1. Microsoft SQL Server Windows Authentication: Unchecked (the default option). Estimated cost to enable is $288/month as of 4/2021. (Use a separate RDS instance to significantly reduce this expense and turn on Reserved Instances).  
1. Additional configuration:
    1. DB parameter group: default value
    1. Option group: default value
    1. Time zone: US Eastern Standard Time
    1. Collation: empty (the default)
    1. Enable automatic backups: checked (the default)
    1. Backup retention period: 7 days (the default)
    1. Backup window: Choose the Select window option. Enter 7:00 am UTC for 1 hour (2am EST)
    1. Copy tags: checked (the default)
    1. Enable Encryption: checked (the default)
    1. Master key: Use the default value
    1. Enable Performance Insights: checked (the default)
        1. Retention period: 7 days (the default)
        1. Master key: Use the default value
    1. Enable Enhanced Monitoring: checked (the default)
        1. Granularity: 60 seconds (the default)
        1. Monitoring role: default (the default)
    1. Log Exports
        1. Agent log: checked
        1. Error log: checked
    1. Enable auto minor version upgrade: Checked
    1. Maintenance Window: Choose the Select window option. Enter Sunday 9:00 am UTC for 1 hour (4am EST)
    1. Deletion protection: Enabled 


Add an MSSQL inbound rule to the default security group.  

Add the option group to create the S3 bucket etc. Refer to the "Setup to allow importing/exporting SQL Database backups section" below.<!-- Don did this-->  

[Connecting to a DB Instance running Microsoft SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToMicrosoftSQLServerInstance.html)  
After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the DB instance.  

## Allow the EC2 instance to connect to the RDS instance

Reference: [Scenarios for accessing a DB instance in a VPC](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.Scenarios.html#USER_VPC.Scenario1)

The goal is to only allow the EC2 instance to access the RDS instance.  
This allows public access to the web server on the EC2 instance while keeping the RDS instance private.

See "Connect to RDS instance" notes in core-admin

## Setup to allow importing/exporting SQL Database backups

Since RDS does not allow access to the underlying operating system that hosts Sql Server, you need to use an S3 bucket to import or export database backups to and from RDS. After setting up components to allow RDS to access the S3 bucket, you will use stored procedures to import or export database backups to and from RDS. Note that importing and exporting is different from the automatic backups that RDS performs.

References:

[Creating an S3 Bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html)

[Importing and exporting SQL Server databases](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html#SQLServer.Procedural.Importing.Native.Using.Restore)

[Support for native backup and restore in SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.BackupRestore.html)

Three components are needed to set up SQL database imports and exports:
1. An Amazon S3 bucket
2. An AWS Identity and Access Management (IAM) role to access the bucket
3. The SQLSERVER\_BACKUP\_RESTORE option added to an option group on the DB instance. Note that a SQLSERVER\_BACKUP\_RESTORE option must be added for each DB instance that needs access to the S3 bucket.

if this is the first time you are setting this up, create a non-public S3 Bucket first if one doesn't already exist. You can have a new IAM role created for you when you add the SQLSERVER\_BACKUP\_RESTORE option to the option group. Otherwise, just select the existing IAM role when adding the SQLSERVER\_BACKUP\_RESTORE option.

If you are creating a new database instance and the option group already exists, you can:
- Specify that option group when creating the instance OR
- Assign the option group to the instance after it has been created. See the Associate the option group with the database instance section below.

### Creating an S3 Bucket

Note: There is no need to create an S3 bucket if an existing non-public S3 bucket already exists.

1. On the [S3 page](https://s3.console.aws.amazon.com/s3/home?region=us-east-1), click Create bucket.
1. Enter a descriptive bucket name
1. AWS Region: US East (N. Virginia) us-east-1 (the default)
1. Block all public access: checked (the default value)
1. Bucket Versioning: Disable (the default)
1. Tags: No tags unless desired
1. Server-side encryption: Disable (the default)
1. Advanced settings
    1. Object Lock: Disable (the default)
1. Review and click Create bucket

### Create an IAM Role and Option Group with the SQLSERVER\_BACKUP\_RESTORE option added

1. Open the [RDS Console](https://console.aws.amazon.com/rds/)
1. In the navigation pane, choose Option groups.
1. Assuming there is no existing option group that does not already have a SQLSERVER\_BACKUP\_RESTORE option, create a new option group by clicking Create Group.
1. Enter a Name: db-instance-2-option-group
1. Enter a Description: Custom option group for Sql Server Web Edition, engine version: 15.00
1. Select the Engine: sqlserver-web
1. Major Engine Version: 15.00
1. Click Create. This creates the option group.
1. Select the newly created option group and then click Add Option.
1. Select the Option name: SQLSERVER\_BACKUP\_RESTORE
1. Select or create an IAM role:
    1. Select an existing IAM role OR
    1. Select Create a New Role
        1. Enter a name: iam-sqlserver-backup-restore
        1. S3 bucket: Select a previously created bucket.
        1. S3 prefix (optional): Leave empty. If you enter a value, such as "backup", the IAM role will only have permissions for that folder and subfolders. If you then try to import from another folder off of the root, you will get an error with the message "Error Code Forbidden". If left empty, then any folder off of the root folder of the S3 bucket can be used for imports and exports.
        1. Enable Encryption: unchecked (the default)
1. Scheduling: Immediately
1. Click Add Option

### Associate the option group with the database instance

1. In the navigation pane, choose Databases.
1. Select the database instance and click Modify
1. Scroll down to the Database optons section and select the newly added option group.
1. Click Continue to preview the updates.
1. Review the updates on the Summary of modifications page.
1. Select Apply Immediately if you want the changes to be applied now or select Apply during the next scheduled maintenance window to apply later.
1. Click Modify DB Instance.

## Backing up and Restoring Sql Server database backups using S3

Since RDS does not allow access to the OS on the database server, you have to run pre-defined stored procedures to perform backups, restores, and other tasks. This is also called importing and exporting in the AWS documentation. Note that importing and exporting is different from the automatic backups that RDS performs.

Reference: [Using native backup and restore](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html#SQLServer.Procedural.Importing.Native.Using)

### Backing up a database to S3

Reference: [Backing up a database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html#SQLServer.Procedural.Importing.Native.Using.Backup)

Syntax:

    exec msdb.dbo.rds_backup_database
	    @source_db_name='database_name', 
	    @s3_arn_to_backup_to='arn:aws:s3:::bucket_name/file_name.extension',
	    [@kms_master_key_arn='arn:aws:kms:region:account-id:key/key-id'],	
	    [@overwrite_s3_backup_file=0|1],
	    [@type='DIFFERENTIAL|FULL'],
	    [@number_of_files=n];

Example:

    exec msdb.dbo.rds_backup_database
	    @source_db_name='Lookup', 
	    @s3_arn_to_backup_to='arn:aws:s3:::[bucket_name]/[folder_name]/Lookup.bak',
        @overwrite_s3_backup_file=1;



<!--
Probably not used

Install as standalone server<br>
Use the default instance and use a different drive if possible for user databases and backups.<br>
<ul>
    <li>Data and Transaction Log files: E:\Data</li>
    <li>Backup Files: E:\Bkup</li>
</ul>
Install the following:<br>
<ul>
    <li>Full Text</li>
    <li>Client tools connectivity</li>
    <li>Integration Services</li>
    <li>Client Tools Backwards Compatibility</li>
    <li>Documentation Components</li>
</ul>
From the installation program, install Sql Server Management Studio (SSMS). For Sql Server 2016+, SSMS is a separate install  

Restore Databases, including Lookup<br>
Create Backup Maintenance Plans and Schedules<br>
Create logins<br>
Setup Network Connectivity and update Firewall settings. If the website and server will reside on the same server,
this step won't be needed.<br>
-->

## Moving a Database to RDS

1. Backup the database on the current server, or locate an existing .BAK file.
1. Copy the backup to an S3 bucket.
    1. Connect to S3 on the current server using a web browser.
    1. Select the bucket and folder to upload to 
    <!--sqlserver-rds-bucket -> FromDarby folder -->
    1. Click the Upload button, click "Add files" and select the .bak backup file. Click Upload.<br><br>    

1. Open SQL Server Management Studio to connect to RDS from the new server.
1. Delete the current database on the new server if it exists.
In SSMS when you right click on a database, the dialog has a checkbox that you can check to close all of the connections to the database before deleting the database.
<!-- So not needed:
If database "in use" prevents Delete, Try running this SQL:  
(Try without Single_User line. That line returns an error, the rest works.)
USE master;  
ALTER DATABASE Atlanta SET SINGLE_USER WITH ROLLBACK IMMEDIATE;  
DROP DATABASE Atlanta;  
Refresh, database should now be gone.  
-->
1. [Restore the database from S3](#restoring-a-database-from-s3).
1. Reset the login and user mappings in Sql Server Studio on the new server for the restored database.
    1. Select the restored database.
    1. Open the Security > Users node and then delete the user that is used to connect to the database in Web.config. <!-- Starts with name of database --> Do the same for the Lookup database if it was restored.
    1. Open the Security node that applies to all databases.
        1. Open the Logins node.
        1. If the user login does not appear, refresh the Logins list. The login may need to be added if it does not exist. If the login needs to be added, choose Sql Server Authentication and enter the username and password so that it corresponds to the values in Web.config.
        1. Right click the user login and choose Properties. 
        1. In the "User Mappings" section, assign the user login to the restored database.
            1. Check db\_datareader, db\_datawriter<!-- wasn't checked for Lookup when setting for Atlanta --> and [database name]SP. <!-- Could be SystemSP for Atlanta db -->Leave "public" checked. Check ExecStoredProc if present. <!-- not present for Atlanta -->
        1. Click OK to save the User Mappings. If you see an error, such as for the Lookup database, be sure to delete the corresponding user in the Lookup database and try again.



### Restoring a database from S3
<a id="restoring-a-database-from-s3"></a>

Reference: [Restoring a database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html#SQLServer.Procedural.Importing.Native.Using.Restore)

**An existing database must be deleted first before restoring, otherwise the restore will fail. There is no overwrite option.**

Sample syntax, run as SQL.  
(simply grab the path from the AWS website S3 detail page):

Change "Lookup" to the name of your database instance.  
(You can avoid including with_norecovery, region, etc.)  

    use master
    go
    exec msdb.dbo.rds_restore_database    
    @restore_db_name='Lookup', 
    @s3_arn_to_restore_from='arn:aws:s3:::[bucket_name]/[folder_name]/Lookup_backup_2022_04_03_200000_8000000.bak';

Allow about 40 seconds, then hit refresh on "Databases" to see the restored database.  

You can track the status using a query:

    use master
    go
    exec msdb.dbo.rds_task_status @db_name='Lookup';

### Tracking the status of tasks

Reference: [Tracking the status of tasks](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html#SQLServer.Procedural.Importing.Native.Tracking)

Syntax:

    exec msdb.dbo.rds_task_status
	    [@db_name='database_name'],
	    [@task_id=ID_number];

If the optional arguments are not used, the status for the most recent tasks are displayed.

Example:

    use master
    go

    exec msdb.dbo.rds_task_status
                @db_name='Lookup';

## Restoring an RDS instance to a point in time

If a database has errors, is missing data, or for some other reason needs to be restored to a previous version, you can restore the RDS instance to a point in time. Note that you cannot restore an individual database to a point in time. Instead, you can only restore the entire instance with all the contained databases to a point in time. This process creates a new RDS instance and contains the data and transaction log updates up to the point in time that you specify. After the instance has been restored, you can then rename or delete the current instance and then set the id of the newly restored instance to have the same id as the current instance. This will allow the websites and other applications to access the databases as they did before. No updates to the connection strings will need to be made to access the databases as long as the RDS instance id remains the same.

Note that the Restore to point in time operation can take several hours to complete depending on the volume of transaction logs to be applied on a given database backup.

1. Click on the Automated Backups link in the navigation page in the [RDS Console](https://console.aws.amazon.com/rds/home?region=us-east-1#). From there, you can see the earliest and the latest time that you can restore the instance from.
1. Select the DB instance that you want to restore.
1. Click Actions -> Restore to a point in time.
1. Select the Latest restorable time option or the Custom option and enter a date and time to restore to. The time should be entered in as EST, which is the time zone for the instance.
1. Specify the new instance id.
1. Use the default values that are pre-populated from the current instance except for the following, which aren't pre-populated for some reason.
    1. Backup -> Check Copy tags to snapshots
    1. Log exports -> Check Agent Log
    1. Log exports -> Check Error Log
    1. Deletion protection -> Check enable deletion protection.
1. Review your settings and then Click Restore to a point in time.
1. For Scheduling of modifications, select Apply immediately or use the default to have the restore done during the next maintenance period.

Once the restoration has completed, edit the restored instance and set the following options
1. Check Enable Enhanced monitoring
    1. Granularity: 60 seconds
    1. Monitoring Role: rds-monitoring-role
1. Check Enable Performance Insights
    1. Retention Period - Default (7 days)
    1. AWS KMS Key: (default) aws/rds
1. Deletion protection -> Check enable deletion protection. This setting didn't seem to be saved during the restoration.
1. Save the settings.

To use the restored instance do the following:
1. Stop the websites and disable scheduled tasks if they aren't already stopped.
1. Edit the current RDS instance and copy the instance ID.
1. Change the instance ID to some other value.
1. Save the changes and monitor the status until it has completed and the new instance ID has been displayed.
1. Edit the restored RDS instance and set the ID equal to the old instance.
1. Save the changes and monitor the status until it has completed and the new instance ID has been displayed.
1. Re-enable the websites and re-enable scheduled tasks.
1. If desired, delete the old instance including any history and logs.

Reference: [Restoring a DB instance to a specified time](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIT.html)

## Restoring a single database to a point in time

Only an entire RDS instance can be restored from a point in time. However, if only one database is corrupted within the RDS instance, the other databases will be restored to that previous time as well, which will probably cause a loss of data in the other databases even though they weren't corrupted. Inserts, updates, and deletes that occurred after the restore time will be lost on those databases. If possible, it may be better to restore a single database to a point in time by doing the following:
1. Restore the RDS instance as described in the section above but don't change the ID of the current instance after restoring.
1. Export the restored database to S3. See the "Backing up a database to S3" section above for more information.
1. Delete the corrupted database in the current RDS instance.
1. Restore the database from S3. See the [Restore a database from S3](#restoring-a-database-from-s3) section for more information.

This will prevent the other databases that weren't corrupted from losing any of their updates.


## Set the EC2 Web Server time zone

From [How to List and View Timezones](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-set-time.html) - so the timezone doesn't default back to UTC.  

    tzutil /l
    tzutil /s "Eastern Standard Time"

The time zone for the RDS instance defaults to UTC. Evidently the only way to fix this is to create a new RDS instance and select the time zone you want. The alternative, is to handle the time zone offset in the application. So, any date time results from the database plus queries, updates, and inserts would need to handle the offset from the application layer.

## Managing Sql Server Agent Jobs on RDS

Reference: [Using Sql Server Agent](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.CommonDBATasks.Agent.html)

Since RDS is running on a managed host in a DB instance, some actions in Sql Server Agent are not supported. The following is a brief list. See the reference link for more information:
1. Email notifications from a Sql Server Agent Job are not supported.
1. Sql Server Agent Job Operators are not supported.

### Migrating a Sql Server Agent Job from a Windows Database Server to AWS RDS 
To migrate a Sql Server Agent job from a Windows database server to AWS RDS, do the following:
1. On the database server open Sql Server Management Studio and connect to the database server.
1. On the EC2 instance, open Sql Server Management Studio and connect to the RDS instance. Open a new Query Editor window by clicking New Query.
1. On the database server, under Sql Server Agent, click on the Jobs folder to list the current jobs.
1. Right click the job that you want to migrate.
1. Select Script Job as -> CREATE to -> New Query Editor Window (or to Clipboard)
1. Copy the script to the clipboard. 
1. On the EC2 instance, paste the script to the Query Editor window.
1. Update the sp\_add\_job arguments:
    1. Change the @notify\_level\_email argument value to 0 if it is not already set to that value.
    1. Update the @owner\_login\_name argument value to the RDS master user, which is the administrator account you specified when the RDS instance was created.
    1. Remove the @notify\_email\_operator\_name argument and value if found. Be sure to remove the trailing comma if it is not the last argument.
    1. Ensure that the @database\_name argument values match the database names in the RDS instance. Each step should have a value.
    1. Update the name of each step, as needed, to match the database name that it will be run against.
1. Execute the script and check for errors.

### Deleting a Job
If a job needs to be deleted do not use the UI in Sql Server Management Studio since you will see the following error:

    The EXECUTE permission was denied on the object 'xp_regread', database 'mssqlsystemresource', schema 'sys'.

Instead, run the following script:

    EXEC msdb.dbo.sp_delete_job @job_name = 'job_name';


## Move Files

If you are transfering an instance of the ComponentCore Item database, you'll need to move the following folders:

Feed  
Files  
Info  
Removed  
Source  

Don't move the Site file. The content of the Site file is auto-generated.  

If you are doing a second file move, you can filter by the date changed and only zip recently added files.   


## Add Web.Config file

See notes in core-admin or contact a system administrator. 
Update the MailReturnPath email address in web.config and in the ManagementSuiteMail config file.


<h2>Add MIME types</h2>

<!--
The following bindings are installed in the default IIS site on Windows Server 2008.<br><br>
net.tcp, net.pipe, net.msmq, msmq.formatname  
-->

**For trail vector maps (optional):**  

	Extension: .kml  
	MIME type: application/vnd.google-earth.kml+xml  

	Extension: .kmz  
	MIME type: application/vnd.google-earth.kmz  

Avoid adding a MIME type for .aspx  
  
<a href="http://www.iis.net/configreference/system.webserver/staticcontent/clientcache#004">Set browser caching</a> - You may want to set the time to cache in the browser to one day.<br>
This may not be necessary now that most browsers and CDNs cache files.  

<!--<div class="projecttime" style="display:none"><strong>Time:</strong>&nbsp;2-4 hrs</div>-->





<a name="email"></a>

<h2>Setup Email</h2>
Setup email to allow website to send emails.<br>

1. Turn on the SMTP Service and set to Automatic Startup.
1. In C:\inetpub\mailroot, give the Network Service account full permissions. This should be the same user account that the IIS Application Pools runs under.
1. Set the domain in the IIS 6 SMTP server properties to be the same name as what is used to connect to the server computer using Remote Desktop. 
    1. Expand the server node.
    1. Right click on the SMTP Virtual Server node.
    1. Select the Properties menu item.
    1. Click the Delivery tab
    1. Click the Advanced button.
    1. Enter the server name in the "Fully-qualified domain name" field. If using AWS, do not use the generic domain given by AWS for the web server. Since it is generic and just has an ip address in it as an identifier, receiving email servers will reject it. Instead, create a DNS A record such as myserver.yourdomain.com that points to the web server.
    1. Save the settings.
1. Ensure that a Reverse DNS (PTR) record is created to point back to the server domain. If using AWS, refer to the next step for more details.
1. AWS blocks the SMTP port 25 by default, so complete the [Request to remove email sending limitations](https://console.aws.amazon.com/support/contacts?#/rdns-limits) form to get AWS to remove the block. Ask AWS to remove the port block and also update the Reverse DNS PTR record so that the domain matches the SMTP server name, i.e. myserver.yourdomain.com.  

### Setup TLS

TLS 1.0 and TLS 1.1 should be disabled on the server since they are not secure. As of April 2022, the server (Windows Server 2019 Datacenter) uses TLS 1.2 for making https requests such as for static page generation and credit card authorizations. Eventually, TLS 1.3 will become the standard and a software download and recommended registry keys should be available to install it on the server. Newer servers should, by default, support the latest version of TLS.

#### Registry Updates for SSL/TLS
The following registry files can be used to disable TLS 1.0, TLS 1.1, and SSL. The TLS file also enables TLS 1.2 which may already be enabled. Be sure to make a backup of the SCHANNEL\Protocols tree before making any updates. To load these files, either right-click on the file and select Open with -> Registry Editor, or search for Registry Editor in the Windows search box and then use File -> Import. These registry files are currently located in the Tools\EnableDisableSSLRegistry folder.

<ul>
<li>DisableSSL2&3.reg</li>
<li>EnableTLS1.2_DisableOlderTLS.reg</li>
</ul>

#### Using TLS in the Server code

The Global.asax file in each website is used to configure the TLS version that is used. Microsoft recommends that the TLS version not be hardcoded which will allow the operating system to determine which version to use including newer versions that may be installed. This may be valid for newer servers such as Windows Server 2019 and beyond, but perhaps not for Windows Server 2008 and older.

In the Application_Start function, add the following code:

        // Set TLS 1.2 as the default HTTPS Protocol. The other TLS protocols are not secure anymore.
        // http://stackoverflow.com/questions/28286086/default-securityprotocol-in-net-4-5
        // Ensure TLS 1.2 is enabled in the registry. Older protocols should be disabled. 
        System.Net.ServicePointManager.SecurityProtocol = (System.Net.SecurityProtocolType)3072;

This statement should also be added to any utilities that make HTTPS requests, such as static page generation, before the requests are made. Note that perhaps these statements are not needed anymore in the code. An easy test could be made by removing it from the static generation program and seeing if it can generate a static file or not. If you see the following error: The request was aborted: Could not create SSL/TLS secure channel", then you know that it still must be included. Be sure that the minimum TLS version for domains hosted on CloudFlare is 1.2 (as of April 2022).

References: 
[Default SecurityProtocol in .NET 4.5](https://stackoverflow.com/questions/28286086/default-securityprotocol-in-net-4-5)
[Transport Layer Security (TLS) best practices with the .NET Framework](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls) 

### Setup TLS for secure email transmission

1. Create a certificate using CertifyTheWeb or some other tool. Set the domain name to match the SMTP server name, i.e. myserver.yourdomain.com. Deploy the certificate to the certificate store only. Try to use DNS authorization so that a website won't have to be set up for the server domain.
1. Restart the server or IIS or the SMTP service.
1. Open IIS 6.
1. Expand the server node.
1. Right click on the SMTP Virtual Server node.
1. Select the Properties menu item.
1. Click the Access tab. The Require TLS encryption checkbox should now be enabled. If it isn't, you may need to restart the server if you tried some of the other reset options after creating the certificate. You can also confirm that the certificate is in the Certificate store:
    1. Open MMC console. (mmc.exe in the 'Run' box).
    1. Under 'File', select 'Add/Remove Snap-in'.
    1. Under the snap-in list, select 'Certificates'
    1. Click Add and a configuration dialog will appear.
    1. Select the Computer account option.
    1. Click the Next button.
    1. Choose Local Computer (it should be selected by default).
    1. Click the Finish button.
    1. Click OK on the Add or Remove Snap-ins dialog.
    1. Once back at the MMC listing, expand the Certificates -> Personal -> Certificates node.
    1. You should then see the certificate that you added. This is where IIS 6 looks for the certificate in order to enable the Require TLS encryption checkbox.
1. Check the Require TLS encryption checkbox.
1. Click the Delivery tab.
1. Click the Outbound Security button and ensure that the TLS encryption checkbox is checked or check it if not checked.
1. Save the settings.


The following tools can be helpful in looking at DNS and Reverse DNS records and testing the SMTP server:
- [DigWebInterface](https://www.digwebinterface.com/) to view DNS and Reverse DNS records
- [MxToolbox SuperTool](https://mxtoolbox.com/SuperTool.aspx) to view DNS and Reverse DNS info as well as other info and for testing the SMTP server.
- [Microsoft SMTPDiag Tool](https://download.cnet.com/Microsoft-Exchange-Server-SMTPDiag-Tool/3000-2070_4-10731690.html) to test SMTP on the local server. Note that this utility is no longer available for download on the Microsoft website.


<!--<div class="projecttime" style="display:none"><strong>Time:</strong>&nbsp; 1-2 hrs</div>-->

    
<h2>Directory Permission Setting</h2>
Apply the following permission change to the following folders:<br>
<ul>
    <li>app_data</li>
    <li>Files</li>
    <li>Source - Larger original versions</li>
    <li>Removed - Source images stored for resizing, but expendable if storage reaches max.</li>
    <li>Page</li>
    <li>Site</li>
</ul>

<!--
Permission change probably not needed here:
<ul>
    <li>Content - 2004 to 2013. Prior to "go" folder and other repos. Included FTP uploads.</li>
</ul>
-->

Right click on the Files directory and select Properties. Click on the Security tab. 

<!--
	XP
Right click on the Files directory and select Sharing and Security. 
-->
Click Add to enter the Network Service username if not already present.  

	[Machine Name]\NETWORK SERVICE  

Give it Full Control. Click Advanced. Keep the first box checked and also check
the second "Replace permission entries on all child objects..."  

## Device detection using 51Degrees.mobi

We are using the 51Degrees mobile device detection for some pages to control the rendered output. The version we are currently using is 3.2.22.2.

To upgrade or install:

1. Make a copy of the current FiftyOne.Foundation.dll in the build\dlls folder.
1. Upload the latest release version of the FiftyOne.Foundation.dll, in the Detector Web Site\bin folder in the release zip file, to the build\dlls folder.

1. For each review or live folder:
    1. Copy the local data file, 51Degrees-LiteV3.2.dat, in the zip file's data folder, to each website's app_data folder (live and review).
    1. Build and Deploy to a review or live folder, or copy the FiftyOne.Foundation.dll from the build\dlls folder to the website's bin folder.
    1. Rename 51Degrees.mobi.config to 51Degrees.mobi.config.old if it exists in the web root, i.e. D:\Live\Georgia, folder.
    1. Copy the local 51Degrees.config from the latest release version, in the Detector Web Site folder, to the corresponding web root, i.e. D:\Live\Georgia, folder.
    1. Ensure that the Network Service account has full privileges for the app_data folder (this folder only).
    1. Restart the site.
    1. Verify that the new data file is being loaded: http://[domain]/core/site/variables.aspx


## Build and Deploy

Type D: to change drive, then cd to change directory. Replace core-site with your sites folder name.

    D:
    cd \core-deploy  
    build  
    deploy \review\core-sites

Use the following to pull a repo you've already cloned. You can also use the following to pull one file or subfolder.

    update \review\core-sites\localsite

Running the update batch file is easy, but you do have to type in the path. If you have multiple repos to pull, you can use the command history (up/down arrow) and then edit previous commands without having to type from scratch every time. This is handy for deploying to review and then live.


## TortoiseGit

In addition to the deploy and update commands above, you can pull from Github using the following (but only if you are logged in as an Administrator due to permission restrictions): 

TortoiseGit is used for the build process on Windows.

Right click a folder and choose TortoiseGit > Settings > Git

On the Git nav item, select Global, to edit the name and email.  Use the noreply email for privacy.  You'll find it in the "[Primary email address](https://github.com/settings/emails)" paragraph in GitHub.

If you get an error, you may need to run this once initially:  

    git config --global --add safe.directory *

You'll see the following if you right click on a TortoiseGit folder -> Settings -> Git -> Edit global .gitconfig [safe]

    directory = *

<!--
Might also need credential and lfs, pasted these from D.A.

[user]

                name = [your account]

                email = [your noreply email from GitHub.com site]

[credential]

                helper = manager-core

[filter "lfs"]

                clean = git-lfs clean -- %f

                smudge = git-lfs smudge -- %f

                process = git-lfs filter-process

                required = true

[safe]

                directory = *

-->

You may need to log on as Administor to Pull into folders due to Windows permission restrictions. You'll get the error: Cannot open '.git/FETCH_HEAD': Permission denied. To resolve, add full access permissions for the user on the Review and Live folders ([Stackoverflow](https://stackoverflow.com/questions/251109/tortoisesvn-and-windows-server-2008-user-account-control)). Being a member of the Administrators group is not enough.

### Check and change GitHub accounts

Check if you are logged in to git, and optionally change your account and email:


    git config -l

    git config --global user.name "[Your Github account username]"

    git config --global user.email [ID]+[username]@users.noreply.github.com



## NeatUpload

Clone the [NeatUpload](https://github.com/joeaudette/neatupload) folder into the site root.
<!-- private form also in modelearth-->  

## Create a Backup Snapshot

To make a backup, switch from using static IP addresses back to using DHCP, create a snapshot, then re-add the secondary IP addresses after making the backup.  As a result, your websites will be down about 10-15 minutes while you are taking the snapshot.  

If you don't switch to static IPs, your install from the snapshot will return a 1/2 complete error.  


## To Reboot

[Use the AWS Console](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-reboot.html) - better than rebooting from the machine.  


## Validation Tools

To validate [accessibility](https://wave.webaim.org/report#/), css, html and links, we recommend the [Web Developer extension](https://chrome.google.com/webstore/detail/web-developer/bfbameneiokkgbdmiekhjnmfkcnldhhm) by chrispederick.com. The Web Developer extension can be activated as a settings icon in the upper right for testing page HTML.

<a name="api"></a>

## Web API

Access to the Web API is currently limited to partner site admins.