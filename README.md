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

### Get the Public IP

In the EC2 list, slide to the right and get the IPv4 DNS.  
Click the row to see the public IPv4.  

Later you'll set a permanent IP since it will otherwise change when the instance is restarted.


### Elastic IP Address Setup and Review

Login to <a href="https://console.aws.amazon.com/">AWS Console</a> and select <a href="https://console.aws.amazon.com/ec2/v2/home?#Addresses:">EC2 Elastic IPs</a> to view the public DNS IPv4 addresses that are mapped to the private IPs you use in IIS.  

If Elastic IPs aren’t used, recycling or stopping/starting the instance will cause new IPs to be assigned to the server. This will not only cause websites to become inaccessible but also our ability to connect to the server using Remote Desktop. We’d have to use a different ip address each time the instance is restarted and the dns for each website would have to be updated.  


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

Associate a domain to an IP in IIS. You can use the same IP with multiple IIS sites.  
Note that the generated cert will have no IP in IIS.  

Add IP in IIS and optionally remove domain name from the new 433 binding<!-- parks site returned Not Found until this was done. -->  
Unchecked "Require Server Name Indication".  (Important, otherwise only the one domain will work)

You can point additional domains at the IP using a proxy in Cloudflare.  
With AWS EC2, the external IP differs from the IP on the machine.  

CloudFlare reuses one cert for all domains pointed at the IIS site. [Cloudflare setup](https://neighborhood.org/localsite/start/cloudflare)  
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


<a name="database"></a>

## Setup RDS for SQL Databases

<!--about/setup/Database.aspx'>Database Setup-->


**The cost is about over 60% more if SQL Server reside on the EC2 Windows server due to the large size required.**  

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
1. Microsoft SQL Server Windows Authentication: Unchecked (the default option). Estimated cost to enable is $288/month as of 4/2021.  
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

Add the option group to create the S3 bucket etc.<!-- Don did this-->  

[Connecting to a DB Instance running Microsoft SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToMicrosoftSQLServerInstance.html)  
After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the DB instance.  

## Allow the EC2 instance to connect to the RDS instance

Reference: [Scenarios for accessing a DB instance in a VPC](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.Scenarios.html#USER_VPC.Scenario1)

The goal is to only allow the EC2 instance to access the RDS instance.  
This allows public access to the web server on the EC2 instance while keeping the RDS instance private.

See "Connect to RDS instance" notes in core-admin

## Setup to allow importing/exporting SQL Database backups

Since RDS does not allow access to the underlying operating system that hosts Sql Server, you need to use an S3 bucket to import or export database backups to and from RDS. After setting up components to allow RDS to access the S3 bucket, you will use stored procedures to import or export database backups to and from RDS.

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

Since RDS does not allow access to the OS on the database server, you have to run pre-defined stored procedures to perform backups, restores, and other tasks. This is also called importing and exporting in the AWS documentation.

Reference: [Using native backup and restore](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html#SQLServer.Procedural.Importing.Native.Using)

### Backing up a database

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



### Restoring a database

Reference: [Restoring a database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html#SQLServer.Procedural.Importing.Native.Using.Restore)

Syntax:

    exec msdb.dbo.rds_restore_database
        @restore_db_name='database_name',
        @s3_arn_to_restore_from='arn:aws:s3:::bucket_name/file_name.extension',
        @with_norecovery=0|1,
        [@kms_master_key_arn='arn:aws:kms:region:account-id:key/key-id'],
        [@type='DIFFERENTIAL|FULL'];

Example:

    use master
    go

    exec msdb.dbo.rds_restore_database    
    @restore_db_name='Lookup', 
    @s3_arn_to_restore_from='arn:aws:s3:::[bucket_name]/[folder_name]/Lookup.bak';

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


## Set the EC2 Web Server time zone

From [How to List and View Timezones](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-set-time.html) - so the timezone doesn't default back to UTC.  

    tzutil /l
    tzutil /s "Eastern Standard Time"

The time zone for the RDS instance defaults to UTC. Evidently the only way to fix this is to create a new RDS instance and select the time zone you want. The alternative, is to handle the time zone offset in the application. So, any date time results from the database plus queries, updates, and inserts would need to handle the offset from the application layer. 


## Core Data Schema  

Each entity receives an ItemID.  Items include users, events, locations, organizations, group lists.  

<a href="img/database/ItemDatabase.gif"><img src="img/database/ItemDatabase.gif" style="width:100%;max-width:828px"></a><br>

## Add Web.Config file

See notes in core-admin  


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
Turn on the SMTP Service and set to Automatic Startup.<br>
In C:\inetpub\maillroot, give the Network Service account full permissions.  
This should be the same user account that the IIS Application Pools runs under.  

<!--<div class="projecttime" style="display:none"><strong>Time:</strong>&nbsp; 1-2 hrs</div>-->

    
<h2>Directory Permission Setting</strong></h2>
Apply the following permission change to the following folders:<br>
<ul>
    <li>Files</li>
    <li>Source - Larger original versions</li>
    <li>Removed - Source images stored for resizing, but expendable if storage reaches max.</li>
    <li>Page</li>
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


## Create a Backup Snapshot

To make a backup, switch from using static IP addresses back to using DHCP, create a snapshot, then re-add the secondary IP addresses after making the backup.  As a result, your websites will be down about 10-15 minutes while you are taking the snapshot.  

If you don't switch to static IPs, your install from the snapshot will return a 1/2 complete error.  


## Updates

#### Deployment Updates

2021 - EC2/RDS setup notes  
2020 - Streamlining server migration.  
2019 - Updates for deployment from Github. 

#### Cloudflare HTTPS Certs

2018 - Switch to free CloudFlare certs for https security.

#### Email Summary Updates

2017 - Email summaries of exceptions are now sent to admins once every 30 minutes containing the remote IPs and the counts per IP address.  Added to adjust for bursts of emails during high traffic.  

For PageName, we've added the highlighted line. If the script_name ends with a trailing slash, as in “/events/”, we return an empty string rather than “events”.  

For IContains, we now check for an empty string as well as null. If the string had been empty, IContains would have returned 0 as the index, and therefore true, rather than false as would be expected.  


#### Custom Page URLs

2016 - Added support for custom page URLs for improved search indexing and short URLs.  

#### Static Page Generation Updates

2015 - Pages generated from multiple queries are now stored in static folders for fast loading.  

<!--
    Fork the Core repo, copy in recent changes from NAAEE version. Changes are primarily removal of remaining IsSite settings. These can be replaced with database settings in the "site" table.
-->
<br>






