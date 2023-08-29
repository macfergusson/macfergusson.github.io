## SQL Server in a Docker Container Quick Start

Having a containerized SQL Server available locally is a bit like having a drive through fast food database available that you can throw away and get a new one any time you need.

For a quick personal use testing setup, everything that follows is free and can run on your home PC desktop:

[Docker Desktop](https://www.docker.com/products/docker-desktop/)

[WSL - Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/install)

[SSMS](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16)

Once you have the WSL and Docker installed, you essentially have a Linux Virtual Machine living inside of your Windows Operating System, and it is now prepared to spin up containers inside of that VM.

Side note: If all this containerization talk is a bit beyond where you're at right now, skip it and just go for [the basic quick start.](https://macfergusson.github.io/2021/11/30/getting-started.html)

The following Powershell script will create a brand new instance of SQL Server inside of a container, so if you muck it up you can just delete the container, and run the script again to start completely fresh.

```ps1
Function Check-RunAsAdministrator()
{
  #Get current user context
  $CurrentUser = New-Object Security.Principal.WindowsPrincipal $([Security.Principal.WindowsIdentity]::GetCurrent())
  
  #Check user is running the script is member of Administrator Group
  if($CurrentUser.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator))
  {
       Write-host "Script is running with Administrator privileges!"
  }
  else
    {
       #Create a new Elevated process to Start PowerShell
       $ElevatedProcess = New-Object System.Diagnostics.ProcessStartInfo "PowerShell";
 
       # Specify the current script path and name as a parameter
       $ElevatedProcess.Arguments = "& '" + $script:MyInvocation.MyCommand.Path + "'"
 
       #Set the Process to elevated
       $ElevatedProcess.Verb = "runas"
 
       #Start the new elevated process
       [System.Diagnostics.Process]::Start($ElevatedProcess)
 
       #Exit from the current, unelevated, process
       Exit
 
    }
}
 
#Check Script is running with Elevated Privileges
Check-RunAsAdministrator

docker run -d --name mssql_docker_1 -e ACCEPT_EULA=Y -e MSSQL_SA_PASSWORD='jKXTqjbP@7KMzB' -p 14331:1433  -e MSSQL_AGENT_ENABLED=True -d mcr.microsoft.com/mssql/server:2022-latest
```

This is a slightly customized template that you can update to your preferences, for example setting  your own SA password, or altering the ports. If you have a local SQL Server instance running on the default port of 1433, and you want to run a containerized SQL Server, they can't both be running on the same port, so this template maps the port 1433 inside of the container to port 14331 on your desktop.

With that all finished, open SSMS, and use a server name of `localhost,14331` and SQL Server Authentication with the SA credentials you chose.

Any time you restart your PC or have any trouble connecting, ensure Docker Desktop is running and check that the SQL Server container in Docker is running.

You are now free to roam about the databases!

### Additional Reading

[Docker - Microsoft SQL Server - Ubuntu based images](https://hub.docker.com/_/microsoft-mssql-server)

[Quickstart: Run SQL Server Linux container images with Docker](https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&pivots=cs1-powershell)

[Configure and customize SQL Server Docker containers](https://learn.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-configure?view=sql-server-ver16&pivots=cs1-powershell)

[MS SQL Server in Docker](https://medium.com/@zzpzaf.se/ms-sql-server-in-docker-b0397a55859c)

[Restore a SQL Server database in a Linux container](https://learn.microsoft.com/en-us/sql/linux/tutorial-restore-backup-in-sql-server-container?view=sql-server-ver16)

[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)

[Install Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/)

