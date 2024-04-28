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
