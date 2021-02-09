# Chocolatey

[Chocolatey][1] è un package manager per Windows.

Per installare il pacchetto eseguire *Powershell come amministratore* e poi

```text
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Una volta installato il package manager, per installare i singoli software standard, chiudere e aprire un *Prompt dei comandi come amministratore*

```{warning}
**Copiare il blocco di testo sotto e valutare cosa serve installare / scaricare (sicuramente non servono TUTTE le versioni di SQL Server...)**
```

TBC: completare/sistemare/organizzare...

```text
choco install googlechrome -y
choco install 7zip -y
choco install notepadplusplus.install -y
choco install agentransack -y
choco install filezilla -y
choco install tortoisesvn -y
choco install winmerge -y
choco install sysinternals -y
choco install putty -y
choco install stickies -y

REM choco install ultravnc -y -params="-password vnc" 

choco install sql-server-management-studio -y

REM DOWNLOADER COMMAND LINE
choco install aria2 -y 

REM FGST.exe
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/FGST.exe -V

REM CARTELLA BASE CON JDK E ECLIPSE
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/ejlog2.4-folder-base.7z -V

REM PACCHETTI SINGOLI
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/jdk1.8.0_202_64.7z -V
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/eclipse-2020-06.7z -V

aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/SQL_SERVER_2016_EXPR_64.7z -V
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/SQL_SERVER_2017_EXPR_64.7z -V
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/SQL_SERVER_2019_EXPR_64.7z -V
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/SQL_SERVER_Mangement_Studio.7z -V

REM PER VEDERE PACKAGE INSTALLATI
choco list --local-only

REM 32 bit se serve
aria2c --ftp-user=guestftp --ftp-passwd=guestftp ftp://ftp.ferrettogroup.com/packages/jdk1.8.0_202_32.7z -V
```

[1]: https://chocolatey.org/