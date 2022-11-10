## Jenkins

Jenkins is a free and open source automation server. It helps automate the parts of software development related to building, testing, and deploying, facilitating continuous integration and continuous delivery.

--- 

## How to create a new job on jenkins

- Step 1: Create new item (On the left you should see New item, select that.)
  
- Step 2: Create job. (Under Enter an item name write the appropriate name for your job)

--- 

## How to SSH connect Between Github and Jenkins then deploy of an EC2 instance.

---
#### Step 1: Generate a new key


- Generate a new ssh key in your localhost and name format eng130-abdellah-jenkins. Steps to gerating a key can be found here in my How to create SSH key README.

---
#### Step 2: Copy Key into Github


    - Open up the correct repo select Settings on that repo
    - Select `Deploy Keys` and select `Add deploy key`
    - Copy the public ssh key into key and name it
    - Now select `Add key`

  **Create a new Jenkins item for CI, Merge and Deploy into EC2 Instance  select Freestyle Project**

---
#### Step 3: CI job on jenkins (test)


- Set up the following configurations:
  
**General**

    - Click Discard old builds and keep the max number of build to 2
    - Click GitHub project and add the HTTP URL of the repository
**Office 365 Connector**

    - Click Restrict where this project can be run, then set it as sparta-ubuntu-node

**Source Code Management**

- Select Git
- In Repositories:
    - Repository URL: insert the SSH URL from GitHub
    - Credentials:
        - Next to Credentials, click Add > Jenkins
        - Select Kind as SSH Username with private key
        - Set a suitable description and enter the private key directly. The private key is in your ~/.ssh directory. Ensure that the begin and end text of the key is included.
        - With the credential added, select the one you created
    - Branches to build: set to */dev (dev branch)

- Build Triggers

    - Click GitHub hook trigger for GITScm polling
- Build Environment

    - Click Provide Node & npm bin/ folder to PATH
  
- Build

    - Click Add build step > Execute Shell
        - In command, enter the following code:
            ```
            cd app
            npm install
            npm test
            ```

- Post-build Actions
    - Select Add post-build action > Build other projects
    - Insert the project name for the merge job
    - Ensure Trigger only if build is stable is selected

---
#### Step 4: Create Merge Job on jenkins


- General

    - Click `Discard old builds` and keep the max number of build to 3
    - Click `GitHub project` and add the HTTP URL of the repository
  
- Office 365 Connector

Click Restrict where this project can be run, then set it as sparta-ubuntu-node

- Source Code Management

    - Select Git
      - In Repositories:
      - Repository URL: insert the SSH URL
      - Credentials: select the credential you created earlier
      - Branches to build: set to */dev (dev branch)
  
- Build Environment

    - Select Provide Node & npm bin/ folder to PATH

 Build

   -  Click Add build step > Execute Shell
      -  In command, enter the following code:
            ```
            git checkout main
            git merge origin/dev
            ```

- Post-build Actions

    - First, select Add post-build action > Git Publisher
    - Click Push Only If Build Succeeds
    - In Branches:
        - Branch to push: main
        - Target remote name: origin
    - Next, select Add post-build action > Build other projects
    - Insert the project name for the deploy job
    - Ensure Trigger only if build is stable is selected
    - Ensure the Build other projects block is below the Git Publisher block



---
#### Step 5: On AWS create an EC2 Instance for Deployment


- We will deploy our application on an EC2 instance.

- Create a new EC2 instance
- Choose an AMI or correct ubuntu 
- Choose t2.micro as the instance type (the default)
- On Configure Instance Details:
- Change the VPC to your VPC
- Change subnet to your public subnet
- Enable Auto-assign Public IP
- Add a tag with the Key as Name and enter an appropriate name as the value
- Enter a suitable name and description for the Security Group with the following rules:
  - SSH (22) with source My IP - allows you to SSH
  - SSH (22) with source jenkins_server_ip/32 - allows Jenkins server to SSH
  - HTTP (80) with source Anywhere - allow access to the app
  - Custom TCP (3000) with source 0.0.0.0/0 - allow access to port 3000
  
Review and Launch




or 



#### Step 6: Create a CD job to launch ec2 insatnce 
---

- General

    - Click `Discard old builds` and keep the max number of build to 2
    - Click `GitHub project` and add the HTTP URL of the repository

- Office 365 Connector

Click `Restrict where this project can be run`, then set it as `sparta-ubuntu-node`

- Source Code Management

    - Git add SSH repo link and Credentials
    - Branch Specifier `main`
  
- Build Triggers
    - Projects to watch `abdellah-dev-merge-main,`
    - Trigger only if build is stable
  
- Build Environment

    - Select Provide Node & npm bin/ folder to PATH
    - Select SSH Agent:
      - Select Specific credentials
      - Select the SSH key for the EC2 instance (DevOpsStudent in this case)

- Build

Select Add build step > Execute Shell

In command, insert the following code:
```
# `ubuntu@ec2-54-220-129-240.eu-west-1.compute.amazonaws.com` the ip is taken from the ec2 insatnce 
rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@ec2-54-220-129-240.eu-west-1.compute.amazonaws.com:/home/ubuntu/
ssh -A -o "StrictHostKeyChecking=no" ubuntu@ec2-54-220-129-240.eu-west-1.compute.amazonaws.com << EOF


cd app

sudo killall -9 node

npm install


nohup node app.js > /dev/null 2>&1 &

EOF
```


### Seed the Database to aws

1. Select New Item from the Jenkins Dashboard
2. Create a new Jenkins Job to seed the database, called name_seed_db
3. Select `Freestyle project` and click OK
4. Select `Discard old builds` and set the number of builds to keep to 3
5. Select Restrict where this project can be run and select `sparta-ubuntu-node`
6. Select `SSH Agent` and select Add and enter the private key for the eng130_jenkins_name key pair
7. Select Add build step and select `Execute shell`
8. Enter the following commands:

```
ssh -A -o "StrictHostKeyChecking=no" ubuntu@ << EOF
sudo killall -9 node

if ["$DB_HOST" = ""]
then
  export DB_HOST=mongodb://:27017/posts >> .bashrc
  sudo source .bashrc
  echo 'IN THE IF STATMENT'
  
  cd eng130-aws-node-app/
  cd app/
  npm install
  nohup node app.js > /dev/null 2>&1 &
else
	echo 'IN THE ELSE STATMENT'
	cd eng130-aws-node-app/
  	cd app/
  	npm install
  	nohup node app.js > /dev/null 2>&1 &
fi
EOF
```