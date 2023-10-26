## SQL Server in a Docker Container Quick Start

Having a containerized SQL Server available locally is a bit like having a drive through fast food database available that you can throw away and get a new one any time you need.

For a quick personal use testing setup, everything that follows is free and can run on your home PC desktop:

[Docker Desktop](https://www.docker.com/products/docker-desktop/)

[WSL - Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/install)

[SSMS](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16)

Once you have the WSL and Docker installed, you essentially have a Linux Virtual Machine living inside of your Windows Operating System, and it is now prepared to spin up containers inside of that VM.

Side note: If all this containerization talk is a bit beyond where you're at right now, skip it and just go for [the basic quick start.](https://macfergusson.github.io/2021/11/30/getting-started.html)

### The Basic Setup

Once you've installed the necessary components listed above, the following Powershell commands will create a brand new instance of SQL Server inside of a container, so if you muck it up you can just delete the container, and run the command again to start fresh.

```ps1
# build container with SQL Server
docker run -e ACCEPT_EULA=Y -e MSSQL_SA_PASSWORD='nL5G$6375feDTq' --name 'mssql_docker_1' -p 14331:1433 -v mssqldata:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2022-latest
```

This is a slightly customized template that you can update to your preferences, for example setting your own SA password, or altering the ports. 
If you have a local SQL Server instance running on the default port of 1433, and you want to run a containerized SQL Server, they can't both be running on the same port, so this template maps the port 1433 inside of the container to port 14331 on your desktop.
Note that this also uses the volume -v option, to define where we put the SQL Server data files in a persistent location. This means that even if we delete this container, the data volume will still be there if we recreate the container.

With that all finished, open SSMS, and use a server name of `localhost,14331` and SQL Server Authentication with the SA credentials you chose.

Any time you restart your PC or have any trouble connecting, ensure Docker Desktop is running and check that the SQL Server container in Docker is running.

You are now free to roam about the database!

### Restoring a Backup

Say you have a bak file that you want to restore in to your fresh containerized SQL Server instance. We can use the persistent volume we talked about earlier, and copy backups in to it, so the containerized SQL Server instance can see it.

```ps1
# create backup folder inside container
docker exec -it mssql_docker_1 mkdir /var/opt/mssql/backup

# fetch sample database bak file
cd ~
curl -L -o wwi.bak 'https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Full.bak'

# copy bak file to backup folder in container
docker cp wwi.bak mssql_docker_1:/var/opt/mssql/backup
```

Now that we have a bak file in our persisted volume, we can do a good old fashioned t-sql restore command:

```sql
RESTORE FILELISTONLY FROM DISK = '/var/opt/mssql/backup/wwi.bak'

RESTORE DATABASE WideWorldImporters FROM DISK = '/var/opt/mssql/backup/wwi.bak' 
WITH MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf'
, MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_userdata.ndf'
, MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf'
, MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1'
```

### Additional Reading

[Docker - Microsoft SQL Server - Ubuntu based images](https://hub.docker.com/_/microsoft-mssql-server)

[Quickstart: Run SQL Server Linux container images with Docker](https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&pivots=cs1-powershell)

[Configure and customize SQL Server Docker containers](https://learn.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-configure?view=sql-server-ver16&pivots=cs1-powershell)

[MS SQL Server in Docker](https://medium.com/@zzpzaf.se/ms-sql-server-in-docker-b0397a55859c)

[Restore a SQL Server database in a Linux container](https://learn.microsoft.com/en-us/sql/linux/tutorial-restore-backup-in-sql-server-container?view=sql-server-ver16)

[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)

[Install Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/)

[Setting Up SQL Server Databases In Docker - Rich in SQL](https://richinsql.com/2022/01/setting-up-sql-server-databases-in-docker/)