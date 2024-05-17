

## Task 1 : Introduction


Learn about CI/CD and build principles to safeguard your pipelines.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/32852a82-be3f-4ab3-b1c2-55370cc64204)

> No answer needed.

### Network diagram :

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/1b59ce2d-6bc1-4aee-8728-e1d16776009b)

So what does a typical CI/CD-enabled pipeline look like? The network diagram of this room helps a bit to explain this. Let's work through the different components that can be found in this pipeline:

- **Developer workstations** - Where the coding magic happens, developers craft and build code. In this network, this is simulated through your `AttackBox`.
- **Source code storage solution** - This is a central placeholder to store and track different code versions. This is the `Gitlab server` found in our network.
- **Build orchestrator** - Coordinates and manages the automation of the build and deployment environments. Both `Gitlab and Jenkins` are used as build servers in this network.
- **Build agents** - These machines build, test and package the code. We are using `GitLab runners and Jenkins agents` for our build agents.
- **Environments** - Briefly mentioned above, there are typically environments for development, testing (staging) and production (live code). The code is built and validated through the stages. In our network, we have both a `DEV and PROD` environment.

## Task 2 : Setting up

#### Configuring DNS -

Add the **Gitlab instance IP** & **Jenkins instance IP** to **/etc/hosts** file.

```bash
sudo echo <Gitlab IP> gitlab.tryhackme.loc >> /etc/hosts && sudo echo <Jenkins IP> jenkins.tryhackme.loc >> /etc/hosts
```

#### Contacting MU-TH-UR 6000 -

As you progress through this network, you must report your work to the **MU-TH-UR 6000 mainframe**, better known as **Mother**. You will have to register with Mother before you begin this perilous journey. SSH is being used for communication as detailed below:

| SSH Username | mother          |
|--------------|-----------------|
| SSH Password | motherknowsbest |
| SSH IP       | X.X.X.250       | 

```bash
┌──(kali㉿kali)-[~]
└─$ ssh mother@10.200.94.250           
The authenticity of host '10.200.94.250 (10.200.94.250)' can't be established.
ED25519 key fingerprint is SHA256:ZX6N5LWRYGSxe+QjwYod/inoO9SH9uMPZ33gTBY7c8s.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.200.94.250' (ED25519) to the list of known hosts.
mother@10.200.94.250's password: 

A... warm.... welcome.... from...

 __  __ _   _     _____ _   _       _   _ ____     __    ___   ___   ___  _ 
|  \/  | | | |   |_   _| | | |     | | | |  _ \   / /_  / _ \ / _ \ / _ \| |
| |\/| | | | |_____| | | |_| |_____| | | | |_) | | '_ \| | | | | | | | | | |
| |  | | |_| |_____| | |  _  |_____| |_| |  _ <  | (_) | |_| | |_| | |_| |_|
|_|  |_|\___/      |_| |_| |_|      \___/|_| \_\  \___/ \___/ \___/ \___/(_)
                                                                            

Please make a selection:
[1] Register
[2] Authenticate
[3] Exit
Selection:1
Please provide your THM username: Shebu                

=======================================
Thank you for checking-in with Mother for the CI/CD Network. Be careful, the are hostiles and androids about!
Please take note of the following details and please make sure to save them, as they will not be displayed again.
=======================================
Username: Shebu
Password: iwNOKQSF1xWTZDxP
MailAddr: Shebu@tryhackme.loc
IP Range: 10.200.94.0/24
=======================================

These details are now active.
Once you have performed the actions to take control of the Android's pipeline, please reestablish a connection to mother.
If your access is deemed worthy, mother will award you with a flag for your progress.

=======================================
Please note once again that the VPN server and Mother, 10.200.94.250, are not in-scope for this investigation.
Any attempts made against Mother will result in immediate termination of your contract.
Best of luck on your journey!

Thank you for talking to Mother, goodbye!
Connection to 10.200.94.250 closed.
```

## Task 3 : What is CI/CD and Build Security?

#### Fundamentals of CI/CD -

According to Gitlab, there are eight fundamentals for CI/CD:

- A single source repository - Source code management should be used to store all the necessary files and scripts required to build the application.
- Frequent check-ins to the main branch - Code updates should be kept smaller and performed more frequently to ensure integrations occur as efficiently as possible.
- Automated builds - Build should be automated and executed as updates are being pushed to the branches of the source code storage solution.
- Self-testing builds - As builds are automated, there should be steps introduced where the outcome of the build is automatically tested for integrity, quality, and security compliance.
- Frequent iterations - By making frequent commits, conflicts occur less frequently. Hence, commits should be kept smaller and made regularly.
- Stable testing environments - Code should be tested in an environment that mimics production as closely as possible.
- Maximum visibility - Each developer should have access to the latest builds and code to understand and see the changes that have been made.
- Predictable deployments anytime - The pipeline should be streamlined to ensure that deployments can be made at any time with almost no risk to production stability.

> 1. What element of a CI/CD pipeline coordinates and manages the automation of build and deployment environments? - `build orchestrators`
> 2. What element of a CI/CD pipeline builds, tests, and packages code? - `build agents`
> 3. What fundamental of CI/CD promotes developers in having access to the latest builds and code in order to understand and see the changes that have been made? - `maximum visibility`

## Task 4 : Creating your own Pipeline

#### Gitlab Registration -

We will start the process by creating an account on the GitLab instance. Navigate to http://gitlab.tryhackme.loc and select the Register Now option on the page. Fill in the required details to create your account and register.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/4581753e-222d-4252-9f59-692477112379)

Once done, we can log in to gitlab server.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/629b6b20-26ce-427e-8987-c3686c1ff9a0)

#### Project Creation -

Now that you have an account, the next step is to create a new project. Feel free to create some of your projects directly to play around with the feature. However, since we want to play around with pipelines, you can make a fork of an existing project that has been created for this purpose.

Click on the Your work tab, then the Explore option, and search for BasicBuild. You will see a project like this:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/a4657ffd-41a8-4c38-bf05-41d3718d54e5)

Click on the project and then on Fork. For the project options, make sure to specify your username as the namespace and to set the project to Private.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/5701b5e8-ee40-43bf-9653-96a6c38c147b)

#### Understanding CI/CD Configuration -

In Gitlab, project automation is defined in the **.gitlab-ci.yml** file. This file contains the steps that will be performed automatically when a new commit is made to the repo. Understanding what the commands in this file do will be important on your journey of learning about build security. Let's take a look at the contents of the file.

**Stages**

GitLab CI files allow you to define various jobs. Each job has different steps that must be executed, as found in the script section of the job. Usually, there are three stages to any CI pipeline, namely the `build, test, and deploy stage`, but there can be more. Let's take a look at the **build job** in **.gitlab-ci.yml** :

```yml
build-job:
  stage: build
  script:
    - echo "Hello, $GITLAB_USER_LOGIN!"
```

- Only an **echo** command will execute for this build job.
- However, developers normally complete all the build activities in the build stage, such as loading dependencies and compiling the code.
- Since we are deploying a simple PHP website, there is no reason for code compiling

**Tests**

- Tests jobs are meant to perform tests on the build to ensure everything works as expected.
- Usually, devs would execute more than one test job to ensure that they can individually test portions of the application.
- If one test job fails, the other test jobs will still continue, allowing devs to determine all the issues with the current build, rather than having to do multiple builds.

Let's take a look at the two test cases:

```yml
test-job1:
  stage: test
  script:
    - echo "This job tests something"

test-job2:
  stage: test
  script:
    - echo "This job tests something, but takes more time than test-job1."
    - echo "After the echo commands complete, it runs the sleep command for 20 seconds"
    - echo "which simulates a test that runs 20 seconds longer than test-job1"
    - sleep 20
```

As you can see, we don't have anything to test with our simple PHP web application. However, we can simulate that one test case will take longer than the other.

**Deployment**

In the deployment stage, we want to deploy our application to the relevant environment if both the build and test stages succeed. Usually, branches are used in source code repos, with the main branch being the only one that can deploy to production. Other branches will deploy to environments such as **_DEV or UAT_**. Let's take a look at what we are doing in the deployment job:

```yml
deploy-prod:
  stage: deploy
  script:
- echo "This job deploys something from the $CI_COMMIT_BRANCH branch."
    - echo "Making the website directory"
    - mkdir -p /tmp/time/cicd
    - echo "Copying the website files"
    - cp website_src/* /tmp/time/cicd/
    - echo "Hosting website using a screen"
    - screen -d -m php -S 127.0.0.1:8081 -t /tmp/time/cicd/ &    
    - echo "Deployment complete! Navigate to http://localhost:8081/ to test!"
  environment: production
```

- The first step of the deploy job is to create a new directory under **/tmp/** where we can place our web application.
- We then copy the web application files to the directory and alter the permissions of the files.
- Once done, host the application, making use of PHP. Now, we are ready to launch our application!
- CI files can become a lot more complex as there are a lot more sections and keywords that you could use.

 Now that we better understand the embedded automation, let's look at actually using it. To have the build execute, we need to register a **runner**.

**Runner Registration**

In Gitlab, we use **runners to execute the tasks configured in the project**. 

In our project, click on Settings and then CI/CD:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/a47289ae-7595-4c45-85e0-8e3156a53562)

Expand the Runners section, 

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/40a591ee-5bc9-4ef2-be1e-6d7d7f0c23a1)

Here, we will be able to _configure a new runner_ for your project. 

### Download & install binary :

```bash
┌──(kali㉿kali)-[~]
└─$ sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 62.2M  100 62.2M    0     0  58167      0  0:18:41  0:18:41 --:--:-- 52452
                                                                                                                                                                              
┌──(kali㉿kali)-[~]
└─$ sudo chmod +x /usr/local/bin/gitlab-runner

[sudo] password for kali: 
                                                                                                                                                                              
┌──(kali㉿kali)-[~]
└─$ sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
                                                                                                                                                                              
┌──(kali㉿kali)-[~]
└─$ sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
Runtime platform                                    arch=amd64 os=linux pid=5103 revision=535ced5f version=16.11.1
                                                                                                                                                                              
┌──(kali㉿kali)-[~]
└─$ sudo gitlab-runner start
Runtime platform                                    arch=amd64 os=linux pid=5337 revision=535ced5f version=16.11.1
```

### Register a runner -

```bash                                                                                                                                                                 
┌──(kali㉿kali)-[~]
└─$ sudo gitlab-runner register --url http://gitlab.tryhackme.loc/ --registration-token GR1348941cKb13-SjqLAV7c6NpGqC
Runtime platform                                    arch=amd64 os=linux pid=5523 revision=535ced5f version=16.11.1
Running in system-mode.                            
                                                   
Enter the GitLab instance URL (for example, https://gitlab.com/):
[http://gitlab.tryhackme.loc/]: 
Enter the registration token:
[GR1348941cKb13-SjqLAV7c6NpGqC]: 
Enter a description for the runner:
[kali]: runner-kali
Enter tags for the runner (comma-separated):
production
Enter optional maintenance note for the runner:

WARNING: Support for registration tokens and runner parameters in the 'register' command has been deprecated in GitLab Runner 15.6 and will be replaced with support for authentication tokens. For more information, see https://docs.gitlab.com/ee/ci/runners/new_creation_workflow 
Registering runner... succeeded                     runner=GR1348941cKb13-Sj
Enter an executor: custom, shell, virtualbox, docker-windows, instance, docker-autoscaler, ssh, parallels, docker, docker+machine, kubernetes:
shell

Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
 
Configuration (with the authentication token) was saved in "/etc/gitlab-runner/config.toml"
```
Now when we refresh the page, we can see the runner.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/b6a8f4c9-68f7-4d0e-bc79-0e15b59d7139)

Our runnner will only run jobs without any tags. It is made so since our lab environment doesnt have any job with tags.

Since we have a simple job, we can just tell our runner to execute all jobs. Click the little pencil icon and click Run untagged jobs & Save:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/d85a1926-8e6e-42b7-b4c9-1524370773a1)

**Build Automation**

Now that the runner is registered, we can test the build process by making a new commit. The easiest change to make that would kick off a build is to update the _README.md_ file.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/76ce209f-f341-4b98-8548-a4b39d896b0e)

Once done, our build process will have started! We can follow the process by clicking on `Build` and then `Pipelines`.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/769d5131-31fe-4187-9823-c9473f022ff1)

Once completed, our application will have been deployed! We can verify this by navigating to http://127.0.0.1:8081/, and we should be met by the web application homepage.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/b8c90968-a624-4b04-938a-4ce939fd4cd0)

_We have now created our very own CI/CD pipeline and build process!_****

> 1. What is the name of the build agent that can be used with Gitlab? - `Gitlab runner`
> 2. What is the value of the flag you receive once authenticated to Timekeep? - `THM{W*********ines}`

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/48333c21-c24a-4072-a243-f2ea8ad508f4)

## Task 5 : Securing the Build Source 

### Source Code Security -

The **first step to securing the pipeline and the build is to secure the source**. In the event that a threat actor can compromise the source of our build, they are in a position to compromise the build itself. We want to protect our source code from the following two main issues:

- _**Unauthorised Tampering**_ - This is the simplest issue of the two. Only authorised users should be able to make changes to the source code. This means that we want to control who has the ability to push new code to our repo.
- _**Unauthorised Disclosure**_ - This is a bit more tricky. Depending on the application, the source code itself might be considered sensitive. For example, Microsoft would not like to disclose the source code of Word since that is their intellectual property. In cases where the source code is sensitive, we must ensure we do not intentionally disclose it. This issue is a lot more common to find.

### Confusion of responsibilities -

**First misconfiguration**

_One such mistake is that organisations can leave registration for their Gitlab instance open. Not open to the internet (although this has also happened before), but open to any user on their internal network to register a profile. This was simulated in the previous task by allowing you to register your own Gitlab profile._

An organisation might have 10,000 employees, of which 2000 may be working as developers and need access to Gitlab. In essence, our attack surface has grown by 500%! **_If a single employee of our bank is compromised, an attacker would have the ability to register a profile and exfiltrate any publicly shared repos._**

**Second misconfiguration**

Developers of our bank may believe that because the Gitlab instance is only accessible internally, it is okay to configure repos to be shared publicly. This means that any user who has a valid Gitlab account will be able to view the repo. While they may perhaps not be able to alter the code, remember, in this example, the code itself is seen as the IP of the bank. This confusion between who is responsible for securing the source code can lead to sensitive information being disclosed to the threat actor. Let's take a look at how this can be exploited!

### Exploiting a vulnerable build source -

To efficiently enumerate publicly visible repos, we will make use of the Gitlab API and a Python script as follows:

```python
import gitlab
import uuid

# Create a Gitlab connection
gl = gitlab.Gitlab("http://gitlab.tryhackme.loc/", private_token='glpat-kRQ4GbynTxySZRJLLx7w')
gl.auth()

# Get all Gitlab projects
projects = gl.projects.list(all=True)

# Enumerate through all projects and try to download a copy
for project in projects:
    print ("Downloading project: " + str(project.name))
    #Generate a UID to attach to the project, to allow us to download all versions of projects with the same name
    UID = str(uuid.uuid4())
    print (UID)
    try:
        repo_download = project.repository_archive(format='zip')
        with open (str(project.name) + "_" + str(UID) +  ".zip", 'wb') as output_file:
            output_file.write(repo_download)
    except Exception as e:
        # Based on permissions, we may not be able to download the project
        print ("Error with this download")
        print (e)
        pass
```

**Gitlab does not allow for its API to be interfaced with using credentials, as this is deemed insecure**. Therefore, to use the script, we will first have to generate an API token for our account.

> We can get the access token by going to _Profile -> Preferences -> Access Token_

Now we can use the script to successfully clone all the repos,

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/213953d9-af23-4d5e-a187-bce375910d8e)


```bash
┌──(root㉿kali)-[/home/kali/thm/CI_CD and Build Security]
└─# python3 enumerate.py              
Downloading project: Basic Build
c09d2b12-1a89-4d03-95d3-d432fb442df7
Downloading project: Approval Test
4a57f04f-94ba-4b9f-a025-1720e4df4056
Downloading project: Merge Test
58bb12e0-ca5a-4571-ac2f-36e83595a129
Downloading project: Basic Build
9e2c0ebb-172c-48c3-9e79-629821abbb4b
Downloading project: Mobile App
1006c61c-eeca-4b02-b8ba-1b138538eb4e

┌──(root㉿kali)-[/home/kali/thm/CI_CD and Build Security]
└─# ls
'Approval Test_4a57f04f-94ba-4b9f-a025-1720e4df4056.zip'  'Basic Build_c09d2b12-1a89-4d03-95d3-d432fb442df7.zip'  'Merge Test_58bb12e0-ca5a-4571-ac2f-36e83595a129.zip'
'Basic Build_9e2c0ebb-172c-48c3-9e79-629821abbb4b.zip'     enumerate.py                                           'Mobile App_1006c61c-eeca-4b02-b8ba-1b138538eb4e.zip'

```
Now we can unzip all the zip files & use `grep -R` to search for hardcodede API keys which is the flag.

```bash
┌──(root㉿kali)-[/home/kali/thm/CI_CD and Build Security/unzip]
└─# grep -R "API" ./   
./mobile-app-master-7a790d318f6ff34861b7903cf61d1810b67cf741/Dockerfile:ENV ANDROID_API_KEY "THM{You.Found.The.API.Key}"
./mobile-app-master-7a790d318f6ff34861b7903cf61d1810b67cf741/README.md:up your secret API key. The stub code is here for that, but please see our
./mobile-app-master-7a790d318f6ff34861b7903cf61d1810b67cf741/app/app.iml:    <orderEntry type="jdk" jdkName="Android API 28 Platform" jdkType="Android SDK" />
```

### Securing the Build Source -

Granular access control is crucial to managing repositories and the GitLab platform. It involves defining specific permissions and restrictions for different users or groups, ensuring that only authorised individuals have the appropriate level of access to sensitive resources. This helps maintain security, confidentiality, and effective collaboration within a development environment.

In GitLab, group-based access control is a powerful mechanism that simplifies permissions management across multiple repositories and projects. Here's how it works:

- **Group-Based Access Control:** GitLab allows you to organise projects into groups. Instead of managing access for each project separately, you can set permissions at the group level. This means that the same access rules apply to all projects within the group, making it easier to maintain consistent security policies. For example, you can create a group for the development team and define permissions, such as who can view, edit, or contribute to projects within that group. This approach streamlines access management and reduces the chances of errors or oversights when configuring permissions for individual repositories.
- **Access Levels :** GitLab offers different access levels, such as Guest, Reporter, Developer, Maintainer, and Owner. Each level comes with specific capabilities and permissions. Assigning the appropriate access level to each user or group ensures they have the necessary privileges without granting unnecessary permissions.
- **Sensitive Information Protection :** One critical consideration is preventing the accidental exposure of sensitive information. GitLab provides features to help with this:
1. **GitLab's .gitignore :** This file specifies which files or directories should be excluded from version control. It's crucial for preventing sensitive data like passwords, API keys, and configuration files from being committed to repositories.
2. **Environment Variables :** GitLab allows you to define and manage environment variables securely, separate from the source code. This is especially useful for storing sensitive data needed during the CI/CD process without exposing it in the repository.
3. **Branch Protection :** Branches, like master or main, can be protected to prevent direct pushes, ensuring that changes go through code review and automated testing before merging.

> 1. Which file specifies which directories and files should be excluded for version control? - `.gitignore`
> 2. What can you protect to ensure direct pushes and vulnerable code changes are avoided? - `branches`
> 3. What issue does lack of access control and unauthorised code changes lead to? - `Unauthorised Tampering` 
> 4. What is the API key stored within the Mobile application that can be accessed by any Gitlab user? - `THM{You.Found.The.API.Key}`


