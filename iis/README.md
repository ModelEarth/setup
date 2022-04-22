[Setup](../)
# Install IIS

From Control Panel, under Programs, click on Turn Windows Features on or off.  

Select the role-based or feature-based installation option (the default)  

Select the current server

### Under Server Roles

Click on Web Server (IIS) – Accept the default options

### Under Web Service Role (IIS) > Features > Web Server

The following options should be checked except where noted.  

- Common HTTP Features

    Default Document    
    Directory Browsing (not needed, uncheck)  
    HTTP Errors  
    Static Content  

- Under Health and Diagnostics

    HTTP Logging 
    Logging Tools  
    ODBC Logging (checked by default)
    Request Monitor  

- Under Performance

    Static Content Compression

- Under Security

    Request Filtering

- Application Development

    .NET Extensibility 4.7

    Application Initialization

    ASP.NET 4.7 (Accept defaults in required features dialog)

    ISAPI Extensions

    ISAPI Filters

- Management Tools

    IIS Management Console             

- IIS 6 Management Compatibility (Used for SMTP Server)

    IIS 6 Metabase Compatibility

    IIS 6 Management Console

### Under Features

Click on .Net Framework 3.5 Features

- Ensure the .Net Framework (includes .Net 2.0 and 3.0) option is checked

Open the .Net Framework 4.7 Features (some options will already be checked)

- Check the ASP.NET 4.7 option

Open the Remote Server Administration Tools -> Feature Administration Tools

- Click on SMTP Server Tools (this may be checked by default when enabling SMTP Server)

Check the SMTP Server option and accept recommended dependent features be installed.

## HTTP Logging

In IIS, optionally activate daily logging per site to the D:\IISLogs folder.

Use the default W3C format and click Select Fields... to specify the fields to include in the log files.

Delete periodically or disable after a period of time so that the log files don't fill up the server storage.  


## Transferring IIS Settings From One Server to Another

In order to transfer settings from one IIS server to another, do the following:
<ol>
<li>Ensure that both servers are running the same version of IIS. A minor version difference such as 11.3 versus 11.5 may be OK. If you try to restore the settings from a server with a major version difference, such as version 8 to version 11, the restore may fail. Trying to restore from a backup of settings made on the new server prior to the restore from the old server may fail as well. You will then have to uninstall IIS, physically delete it from the file system, and then re-install it again on the new server. Reference: <a href='https://docs.microsoft.com/en-us/archive/blogs/friis/how-to-perform-a-clean-reinstallation-of-iis' target='_blank'>How to perform a clean reinstallation of iis</a></li>
<li><a href='https://stackoverflow.com/questions/58953676/how-can-i-copy-all-iis-setting-configurations-application-pools-from-one-iis-b' target='_blank'>Backup the settings on the old server and the new server</a>.</li>
<li><a href='https://www.digicert.com/kb/ssl-support/certificate-pfx-file-export-import-iis-10.htm' target='_blank'>Export the SSL certificates from the old server and then import them to the new server</a>.</li>
<li><a href='https://stackoverflow.com/questions/58953676/how-can-i-copy-all-iis-setting-configurations-application-pools-from-one-iis-b' target='_blank'>Restore the settings from the old server to the new one</a>. You still may see an error code indicating failure, but hopefully you'll be able to see the restored settings in IIS. Perhaps the error code resulted from the SSL certificates.</li>
</ol>

<br />
Additional AWS EC2 notes reside in the core-admin aws subfolder.  
