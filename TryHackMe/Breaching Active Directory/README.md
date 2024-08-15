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
