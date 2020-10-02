[Server Setup](../)
# Install IIS

From Control Panel, under Programs, click on Turn Windows Features on or off.  

Select the role-based or feature-based installation option (the default)  

Select the current server

### Under Server Roles

Click on Web Server (IIS) – Accept the default options

### Under Features

Click on .Net Framework 3.5 Features

- Ensure the .Net Framework (includes .Net 2.0 and 3.0) option is checked

Open the on .Net Framework 4.7 Features (some options will already be checked)

- Check the ASP.NET 4.7 option

### Under Web Service Role (IIS) > Features > Web Server

The following options should be checked except where noted.  

- Common HTTP Features

    Default Document    
    Directory Browsing (not needed, uncheck)  
    HTTP Errors  
    Static Content  

- Under Health and Diagnostics

    HTTP Logging (not needed, uncheck)

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

<br>
Additional AWS EC2 notes reside in the core-admin aws subfolder.  
