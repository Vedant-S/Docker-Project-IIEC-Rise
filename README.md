# Docker-Project-IIEC-Rise
_________________________________________________________________________________________________________

DevOps is a set of practices that combines software development (Dev) and information-technology operations (Ops) which aims to shorten the systems development life cycle and provide continuous delivery with high software quality. Demand for the development of dependable, functional apps has soared in recent years. In a volatile and highly competitive business environment, the systems created to support, and drive operations are crucial. Naturally, organizations will turn to their in-house development teams to deliver the programs, apps, and utilities on which the business counts to remain relevant."
_______________________________________________________________________________________________________________
## Docker:

Docker is a set of platform as a service (PaaS) products that uses OS-level virtualization to deliver software in packages called containers. It is a category of cloud computing services that provides a platform allowing customers to develop, run, and manage applications without the complexity of building and maintaining the infrastructure typically associated with developing and launching an app. Docker is one of the tools that used the idea of the isolated resources to create a set of tools that allows applications to be packaged with all the dependencies installed and ran wherever wanted. Docker has two concepts that is almost the same with its VM containers as the idea, an image, and a container. An image is the definition of what is going to be executed, just like an operating system image, and a container is the running instance of a given image.

**Differences between Docker and VM:**

    Docker containers share the same system resources, they don’t have separate, dedicated hardware-level resources for them to behave like completely independent machines.
    They don’t need to have a full-blown OS inside.
    They allow running multiple workloads on the same OS, which allows efficient use of resources.
    Since they mostly include application-level dependencies, they are pretty lightweight and efficient. A machine where you can run 2 VMs, you can run tens of Docker containers without any trouble, which means fewer resources = less cost = less maintenance = happy people.

## Jenkins:

**_'Jenkins to the rescue!'_**

As a Continuous Integration tool, Jenkins allows seamless, ongoing development, testing, and deployment of newly created code. Continuous Integration is a process wherein developers commit changes to source code from a shared repository, and all the changes to the source code are built continuously. This can occur multiple times daily. Each commit is continuously monitored by the CI Server, increasing the efficiency of code builds and verification. This removes the testers' burdens, permitting quicker integration and fewer wasted resources.

**Jenkins Features**

####    Easy Installation:
    Jenkins is a platform-agnostic, self-contained Java-based program, ready to run with packages for Windows, Mac OS, and Unix-like operating systems.
-----------------------------------------------------------------------------------------------------------
####    Easy Configuration:
    Jenkins is easily set up and configured using its web interface, featuring error checks and a built-in help function.
-----------------------------------------------------------------------------------------------------------
####    Available Plugins:
    There are hundreds of plugins available in the Update Center, integrating with every tool in the CI and CD toolchain.
-----------------------------------------------------------------------------------------------------------
####    Extensible:
    Jenkins can be extended by means of its plugin architecture, providing nearly endless possibilities for what it can do.
-----------------------------------------------------------------------------------------------------------
####    Easy Distribution:
    Jenkins can easily distribute work across multiple machines for faster builds, tests, and deployments across multiple platforms.
-----------------------------------------------------------------------------------------------------------
####    Free Open Source:
    Jenkins is an open source resource backed by heavy community support.
-----------------------------------------------------------------------------------------------------------

____________________________________________________________________________________________________________________________________
# Implementation and Understanding:

* Explanation and use case of Docker as a container based software to host programs such as any OS. 

* Introduction to concept of an environment which consists of WebServer and Distros.

* Launching of OS in isolation (locally) from docker.

* Defining OS Image as a bootable i/o system or device. 

* Also setup local webserver httpd from docker.
```diff
# docker pull httpd
# docker run -d -t -i --name Redhat_WebServer httpd
# docker cp new.html Redhat_WebServer
```

* Concept of mounting docker to remote developer's system explained.
```diff
# docker run -d -t -i /lwweb:/user/local/apache2/docs/ --name MyWebPage httpd
```

* Explanation of PAT networking to make local WebServer available to clients globally.
```diff
# docker run -d -t -i /lwweb:/user/local/apache2/docs/ -p 8001:80 --name ClientSideOpen httpd
```

* Integrating both Jenkins and Docker technologies along with the VCS such as Github with update data obtained from developers system.

* Explaining the necessity of two independent job creation for (1)obtaining code pushed to github by pull request by Jenkins, and (2)Deploying code to the environment.

* Explanation of the method of job chaining as build trigger , where one job depends on the others success, instead of simply being queued with it.

* Providing the root access to Jenkins using sudo command, after editing /etc/sudoers file, instead of the setfacl Access Control Listing command. 

# The Architecture:

1. The DEVELOPER & Local Workspace:

Each developer maintains a local workspace in personal system, and commits the changes/patches to Github through Git as and when necessary.

2. Github:

Once the changes are pushed to Github, the challenge is to have an automated architecture or system in order to deploy these changes to the webserver (after testing) in order for the clients to avail the benefits.

3. Jenkins:

This challenge is solved by Jenkins which serves as a middlemen to automate the deployment process.

# The Project:

## JOB#1 

If Developer push to dev branch then Jenkins will fetch from dev and deploy on dev-docker environment.

## JOB#2 

If Developer push to master branch then Jenkins will fetch from master and deploy on master-docke environment. both dev-docker and master-docker environment are on different docker containers.

## JOB#3 

Jenkins will check (test) for the website running in dev-docker environment. If it is running fine then Jenkins will merge the dev branch to master branch and trigger #job 2

This is the repository where the Project has been uploaded.

**1. Setting up the Development/Host System:**

This is the system in which the developer works. We are considering it to be Windows. Here, Git is installed and authenticated. To make this project more realistic, we are going to work on 2 GitHub branches:

```diff
master
dev1
First, we create a new repository in GitHub. We copy the clone url of the repository and clone the it in the Desktop using git clone <repo url>.
```

We move inside the folder just created and create an index.html file. Inside the file, lets type
This is from master branch
Then we add the file in the Staging Area using git add .
Then we commit the changes using the command: git commit . -m "first commit from master"
Finally we push to GitHub using: git push

We can also cross-check the contents from the GitHub website to confirm.

Now, we want to work with the dev1 branch. We want to pull the contents from the master branch first.

To create and change the branch, we type: git checkout -b dev1 Now, all the codes from master branch are present in the dev1 branch. We want to add an extra line in this branch:
cat >> This line is from Dev1 branch

To stage, commit and push, we use the following commands:

```diff
git add . 
git commit . -m "Second commit from DEV1"
git push -u origin dev1
```

Now, we have setup 2 branches in Github, having some differential codes.


**2. Setting up the Server System:**

This is the system where the Web Server will be running. We are going to use Docker to run 2 httpd servers, one for deploying the master branch and the other for the dev1 branch. We are going to use RHEL 8 as the Operating System to host the Docker containers.

As we are going to automate the process, we are going to configure Jenkins for creating the CI/CD pipeline.

Jenkins would do 2 important tasks for each of the branches:

Dowloading any code changes from GitHub to the server system.
Starting the Webserver from Docker containers, to view the final website.
Jenkins would do another important task from the Quality Assurance Team. If the dev1 branch is working fine, then Jenkins would merge it with the master branch, on being triggered to do so.

We create 2 directories on our Desktop directory from the terminal.

```diff
lwtest
lwtestdev
The codes downloaded from GitHub by Jenkins would be placed in these folders according to their branches.
```

Now, lets configure Jenkins to do all those tasks.

**3. Configuring Jenkins:**

Lets start the Jenkins process from RHEL 8 by the command: systemctl start jenkins
Now, on visiting the port 8080, we can see the Jenkins Control Panel running.

But, we are going to configure it from Host / Developer system by getting the IP address of RHEL.
For that we use the command ifconfig enp0s3

After getting the IP Address, from any browser of the Host System, we can direct to the IP address.
for e.g. http://192.123.32.2932:8080
After logging in with both the username and password as admin, we come to the Jobs List page. Here we can see the Jobs we have submitted to be performed.

**3.1. Task-1 : Automatic Code Download:**

For now, we have to create new Jobs for downloading the latest codes from both the branches of Github separately, to the Server system, for being deployed on the Web-server.
i.e. The master branch should be downloaded in the lwtest folder and the dev1 branch would be downloaded in the lwtestdev folder.

For creating the Job for downloading codes from the master branch:

Select new item option from the Jenkins menu.
Assign a name to the Job ( eg. lwtest-1 )and select it to be a Freestyle project.
From the Configure Job option, we set the configurations.
From Source Code Management section, we select Git and mention the URL of our GitHub Repository and select the branch as master.
In the Build Triggers section, we select Poll SCM and set the value to * * * * *.
This means that the Job would check any code change from GitHub every minute.
In the Build Section, we type the following script: sudo cp -v -r -f * /root/Desktop/lwtest This command would copy all the content downloaded from the GitHub master branch to the specified folder for deployment.
On clicking the Save option, we add the Job to our Job List.
On coming back to the Job List page, we can see the Job is being built. If the color of the ball turns blue, it means the Job has been successfully excecuted. If the color changes to red, it means there has been some error in between. We can see the console output to check the error.

***The same process can be performed to create another Job lwtest-2 for the dev1 branch. Only the branch name needs to be changed to dev1 and the script:***
```diff
sudo cp -v -r -f * /root/Desktop/lwtestdev
```

Till now, we have successfully downloaded the codes from both the branches of GitHub to our Server System automatically.

**3.2. Task-2 : Automatically Starting the Docker containers:**

Next, we create another Job for starting the docker containers once the codes have been copied into the lwtest and lwtestdev folders.

For starting the docker container once the lwtest folder updates from the master branch:

Select new item option from the Jenkins menu.
Assign a name to the Job ( eg. lwtest-1-docker )and select it to be a Freestyle project.
From the Configure Job option, we set the configurations.
From Build Triggers section, we select Build after other projects are built and mention lwtest as the project to watch. This is called DownStreaming.
In the Build Section, we type the following script:
```diff
if sudo docker ps | grep docker_master
then
echo "Already Running"
else
sudo docker run -d  -t -i -p 8085:80 -v /root/Desktop/lwtest:/usr/local/apache2/htdocs/ --name docker_master httpd
fi
```
This script first checks if any Docker container has been already created, in such case it exits.
Else, it starts a new Docker container of the httpd webserver, with some parameters:

Runs the container in background : -d
Exposes the port so that the container can be operated from outside the Server system (RHEL)
Mounts the lwtest folder on the htdocs folder, from where the webserver renders the pages.
Sets the name of the container as docker_master
On clicking the Save option, we add the Job to our Job List.
The same process can be performed to create another Job lwtest-2-docker for starting the docker containers once the lwtestdev folder updates from the dev1 branch.
Only the build trigger needs to be set to watch lwtestdev and the corresponding folder name should be changed in the script.

These 2 Jobs would automatically run just after the codes are updated in the respective folders, by the previous jobs.

Thus we have successfully set up a CI/CD pipeline where once the developer pushes the code on GitHub, Jenkins would automatically download the codes and start the Docker containers to run the Web-Server.

**3.3. Task-3 : Merging the dev1 branch with master by QAT:**

Finally, this is the task of the Quality Assurance Team. When the team certifies that the dev1 branch is working fine, they can merge it with the master branch using Remote Build Triggers.

To setup the Trigger we are going to use Jenkins to do the automation:

Select new item option from the Jenkins menu.
Assign a name to the Job ( eg. Merge-test )and select it to be a Freestyle project.
From the Configure Job option, we set the configurations.
From Source Code Management section, we select Git and mention the URL of our GitHub Repository and select the branch to build as dev1.
From the Additional Behavior Dropdown list, we select Merge Before Build
There, we set the Name of the Repository as origin
We set the Branch to Merge to as master
From the Build Triggers section, we select Trigger builds remotely option.
Provide an Authentication Token
From the Post Build Actions dropdown, we select Git Publisher.
We check Push only if build succeeds and Merge Results options.
On clicking the Save option, we add the Job to our Job List.
Now from Build Triggers section of the lwtest job, we select Build after other projects are built and mention Merge-test as the project to watch.
Thus, the Job has been setup. To Trigger the Build, the QAT would run the following command:
```diff
curl --user "<username>:<password>" JENKINS_URL/job/Mergetest/build?token=TOKEN_NAME
```

e.g. 
```diff
curl --user "admin:admin" http://192.123.32.2932:8080/job/Merge-test/build?token=redhat
```

So, after merging, the lwtest Job is again fired, so that the updated code can be downloaded in the Server system to run in the web-server.
