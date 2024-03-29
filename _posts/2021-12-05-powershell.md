## Powershell Related Notes and Resources

[Intro to Powershell](http://www.powershellpro.com/powershell-tutorial-introduction/)

[Official PS Docs](https://technet.microsoft.com/en-us/scriptcenter/powershell.aspx)

[dbatools is a free PowerShell module with over 500 SQL Server best practice, administration, development and migration commands included](https://dbatools.io/commands/)

[PowerShell Universal - Ironman Software](https://ironmansoftware.com/powershell-universal)

[SQL Server Backups](https://www.mssqltips.com/sqlservertip/1862/backup-sql-server-databases-with-a-windows-powershell-script/)

[Check Latest SQL Server Backup Date](https://www.mssqltips.com/sqlservertip/1784/check-the-last-sql-server-backup-date-using-windows-powershell/)

[List Local Admins](https://4sysops.com/archives/create-a-list-of-local-administrators-with-powershell/)

[Excel Import](https://github.com/dfinke/ImportExcel)

[SQL Server Tips in PS](https://www.mssqltips.com/sql-server-tip-category/81/powershell/)


### SSRS things!

[SSRS Data in PS](https://www.mssqltips.com/sqlservertip/4294/accessing-sql-server-reporting-services-data-using-powershell/)

[Automate Data Source Deployment](https://www.mssqltips.com/sqlservertip/4429/sql-server-reporting-services-data-source-deployment-automation-with-powershell/)


```
[string] $url = "https://XXXXXXX.XXXX.XXXX.XXXX/Reports";

[System.Net.WebClient] $wc = New-Object System.Net.WebClient;
$wc.UseDefaultCredentials = $true;
$result = $wc.DownloadString($url);
$wc.Dispose();
```

copy  system level security(site settings) and folder level  roles from old server to new  SSRS server. 

```
$SSRSReportServer = '#{SSRS_Report Execution Url}'
Get-RsCatalogItemRole -ReportServerUri $SSRSReportServer  -Recurse | select Identity,TypeName,Roles, ParentSecurity,Path | ft -AutoSize | out-string -Width 2000 > "D:\XXXXX\XXXX\$env:COMPUTERNAME - permissions.txt"
```

then loop through those results and do something like this...

```
$TheSSRSServer           = "https://xxxxxxx.xxxxxx.xxxx.xxxx/ReportServer"
Grant-RsCatalogItemRole  -Identity $XXX     -RoleName "Publisher"          -Path $Folder -ReportServerUri $TheSSRSServer 

```

```
    Grant-RsSystemRole
    
SYNOPSIS
    This script grants access to SQL Server Reporting Services Instance to users/groups.
    
    
    -------------------------- EXAMPLE 1 --------------------------
    
    PS C:\>Grant-RsSystemRole -Identity 'johnd' -RoleName 'System User'
    
    Description
    -----------
    This command will establish a connection to the Report Server located at http://localhost/reportserver using current user's credentials and then grant 'System User' access to user 'johnd'.
    
    
    
    
    -------------------------- EXAMPLE 2 --------------------------
    
    PS C:\>Grant-RsSystemRole -ReportServerUri 'http://localhost/reportserver_sql2012' -Identity 'johnd' -RoleName 'System User'
    
    Description
    -----------
    This command will establish a connection to the Report Server located at http://localhost/reportserver_2012 using current user's credentials and then grant 'System User' access to user 'johnd'.
    
    
    
    
    -------------------------- EXAMPLE 3 --------------------------
    
    PS C:\>Grant-RsSystemRole -ReportServerCredentials 'CaptainAwesome' -Identity 'johnd' -RoleName 'System User'
    
    Description
    -----------
    This command will establish a connection to the Report Server located at http://localhost/reportserver using CaptainAwesome's credentials and then grant 'System User' access to user 'johnd'.
```
