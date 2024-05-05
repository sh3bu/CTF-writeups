Link - https://underthewire.tech/cyborg

## Cyborg 0 -> 1 :

The goal of this level is to log into the game. Do the following in order to achieve this goal.

1. Obtain the initial credentials via the #StartHere channel on our Slack (link). Once you are in the channel, scroll to the top to see the credentials.

2. After obtaining the credentials, connect to the server via SSH. You will need an SSH client such as Putty. The host that you will be connecting to is cyborg.underthewire.tech, on port 22.

3. When prompted, use the credentials for the applicable game found in the #StartHere Slack channel.

4. You have successfully connected to the game server when your path changes to “PS C:\Users\Cyborg1\desktop>”.

> Creds - `cyborg1:cyborg1`

## Cyborg 1 -> 2 :

The password for cyborg2 is the state that the user Chris Rogers is from as stated within Active Directory.

NOTE:
– The password will be lowercase no matter how it appears on the screen.
– “State” refers to the location within the country and NOT the “state” of the account (enabled/ disabled).

IMPORTANT:
Once you feel you have completed the Cyborg1 challenge, start a new connection to the server, and log in with the username of Cyborg2 and this password will be the answer from Cyborg1. If successful, close out the Cyborg1 connection and begin to solve the Cyborg2 challenge. This concept is repeated over and over until you reach the end of the game.


▼ HINT:
List the available modules, there may be a useful one available…

```powershell
PS C:\users\cyborg1\desktop> Get-ADUser -Filter 'Name -like "*rogers*"'

DistinguishedName : CN=Rogers\, Chris\ ,OU=T-65,OU=X-Wing,DC=underthewire,DC=tech
Enabled           : False
GivenName         : Chris
Name              : Rogers, Chris
ObjectClass       : user
ObjectGUID        : ee6450f8-cf70-4b1d-b902-a837839632bd
SamAccountName    : chris.rogers
SID               : S-1-5-21-758131494-606461608-3556270690-2177
Surname           : Rogers
UserPrincipalName : chris.rogers
```

By default, the `Get-ADUser` cmdlet doesn't show all the properties. As per [Microsoft Documentation](https://learn.microsoft.com/en-us/windows/win32/adschema/a-st?redirectedfrom=MSDN) , the state info is in the property named `st`.

```powershell
PS C:\users\cyborg1\desktop> Get-ADUser -Filter 'Name -like "*rogers*"' -Property st


DistinguishedName : CN=Rogers\, Chris\ ,OU=T-65,OU=X-Wing,DC=underthewire,DC=tech
Enabled           : False
GivenName         : Chris
Name              : Rogers, Chris
ObjectClass       : user
ObjectGUID        : ee6450f8-cf70-4b1d-b902-a837839632bd
SamAccountName    : chris.rogers
SID               : S-1-5-21-758131494-606461608-3556270690-2177
st                : kansas
Surname           : Rogers
UserPrincipalName : chris.rogers
```
> Creds - `cyborg2:kansas`

## Cyborg 2 -> 3 :

The password for cyborg3 is the host A record IP address for CYBORG718W100N PLUS the name of the file on the desktop.

NOTE:
– If the IP is “10.10.1.5” and the file on the desktop is called “_address”, then the password is “10.10.1.5_address”.
– The password will be lowercase no matter how it appears on the screen.

▼ HINT:
WMI or cmdlets… choices, choices.


▼ HINT:
Each domain client has its own specific Zone Name.

