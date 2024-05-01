![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/ed169f4f-7d8c-4ece-96c0-deb31dffb3d8)

Link - https://underthewire.tech/century

## Century 0 -> 1 :

The goal of this level is to log into the game. Do the following in order to achieve this goal.

1. Obtain the initial credentials via the #StartHere channel on our Slack (link). Once you are in the channel, scroll to the top to see the credentials.

2. After obtaining the credentials, connect to the server via SSH. You will need an SSH client such as Putty. The host that you will be connecting to is century.underthewire.tech, on port 22.

3. When prompted, use the credentials for the applicable game found in the #StartHere Slack channel.

4. You have successfully connected to the game server when your path changes to “PS C:\Users\Century1\desktop>”.


> Creds - `century:century1`

```powershell
PS C:\Users\shebu> ssh century1@century.underthewire.tech
The authenticity of host 'century.underthewire.tech (64:ff9b::c063:a79c)' can't be established.
ECDSA key fingerprint is SHA256:AU0EtNBKGxuIk7wHdKSLcUJ8uoObOMI1uMqPa80c/Bc.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'century.underthewire.tech' (ECDSA) to the list of known hosts.
century1@century.underthewire.tech's password:
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

Under the Wire... PowerShell Training for the People!
PS C:\users\century1\desktop>
```
## Century 1 -> 2 :

The password for Century2 is the build version of the instance of PowerShell installed on this system.

NOTE:
– The format is as follows: **.*.*****.****
– Include all periods
– Be sure to look for build version and NOT PowerShell version

```powershell
PS C:\users\century1> $PSVersionTable

Name                           Value
----                           -----
PSVersion                      5.1.14393.5582
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.14393.5582
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```
> Creds - `century2:10.0.14393.5582`

## Century 2 -> 3 :

The password for Century3 is the name of the built-in cmdlet that performs the wget like function within PowerShell PLUS the name of the file on the desktop.

NOTE:
– If the name of the cmdlet is `get-web` and the file on the desktop is named `1234`, the password would be `get-web1234`.
– The password will be lowercase no matter how it appears on the screen.

```powershell
PS C:\users\century2\desktop> gal wget

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           wget -> Invoke-WebRequest


PS C:\users\century2\desktop> Get-ChildItem


    Directory: C:\users\century2\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:29 AM            693 443
```

> Creds - `century3:invoke-webrequest443`

## Century 3 -> 4 :

The password for Century4 is the number of files on the desktop.

```powershell
PS C:\users\century3\desktop> Get-ChildItem | Measure-Object


Count    : 123
Average  :
Sum      :
Maximum  :
Minimum  :
Property :

PS C:\users\century3\desktop> (Get-ChildItem | Measure-Object).Count
123
```
> Creds - `century4:123`

## Century 4 -> 5 :

The password for Century5 is the name of the file within a directory on the desktop that has spaces in its name.

NOTE:
– The password will be lowercase no matter how it appears on the screen.

```powershell
PS C:\users\century4\desktop> cd '.\Can You Open Me'
PS C:\users\century4\desktop\Can You Open Me> Get-ChildItem


    Directory: C:\users\century4\desktop\Can You Open Me


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/14/2024   2:35 PM             24 34182

PS C:\users\century4\desktop\Can You Open Me> type .\34182
Great Work!  Keep it up.
```

> Creds - `century5:34182`

## Century 5 -> 6 :

The password for Century6 is the short name of the domain in which this system resides in PLUS the name of the file on the desktop.

NOTE:
– If the short name of the domain is “blob” and the file on the desktop is named “1234”, the password would be “blob1234”.
– The password will be lowercase no matter how it appears on the screen.

```powershell
PS C:\users\century5\desktop> Get-WMIObject -Class Win32_ComputerSystem


Domain              : underthewire.tech
Manufacturer        : OpenStack Foundation
Model               : OpenStack Nova
Name                : UTW
PrimaryOwnerName    : UTW_Team
TotalPhysicalMemory : 12582359040

PS C:\users\century5\desktop> Get-ChildItem

    Directory: C:\users\century5\desktop

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:29 AM             54 3347
```
> Creds - `century6:underthewire3347`

## Century 6 -> 7 :

The password for Century7 is the number of folders on the desktop.

```powershell
PS C:\users\century6\desktop> (Get-ChildItem).count
197
```

> Creds - `century7:197`

## Century 7 -> 8 :

The password for Century8 is in a readme file somewhere within the contacts, desktop, documents, downloads, favorites, music, or videos folder in the user’s profile.

NOTE:
– The password will be lowercase no matter how it appears on the screen.

```powershell
PS C:\users\century7> Get-ChildItem -Path C:\users\century7 -File -Include *README* -Recurse -ErrorAction Silentlycontinue


    Directory: C:\users\century7\Downloads


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:29 AM              7 Readme.txt


PS C:\users\century7> Get-Content C:\users\century7\Downloads\Readme.txt
7points
```
> Creds - `century8:7points`

## Century 8 -> 9 :

The password for Century9 is the number of unique entries within the file on the desktop.

```powershell
PS C:\users\century8\desktop> Get-ChildItem


    Directory: C:\users\century8\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:33 AM          15858 unique.txt

PS C:\users\century8\desktop> (Get-Content .\unique.txt | unique).count
696
```

> Creds - `century9:696`

## Century 9 -> 10 :

The password for Century10 is the 161st word within the file on the desktop.

NOTE:
– The password will be lowercase no matter how it appears on the screen.

```powershell
PS C:\users\century9\desktop> Get-Content .\Word_File.txt -Delimiter " " | Select-Object -Index 160
pierid
```
> Creds - `century10:pierid`

## Century 10 -> 11 :

The password for Century11 is the 10th and 8th word of the Windows Update service description combined PLUS the name of the file on the desktop.

NOTE:
– The password will be lowercase no matter how it appears on the screen.
– If the 10th and 8th word of the service description is “apple” and “juice” and the name of the file on the desktop is “88”, the password would be “applejuice88”.


