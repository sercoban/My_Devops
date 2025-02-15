# Hands-on Jenkins-06 : Slave Node Configuration

Purpose of the this hands-on training is to learn how to configure a slave node to Jenkins Server. 

## Learning Outcomes

At the end of the this hands-on training, students will be able to;

- configure a slave node

- run jobs on slave node.

## Outline

- Part 1 - Slave Node Configuration

- Part 2 - Free Style Project on Slave Node

- Part 3 - Pipeline Project on Slave Node


## Part 1 - Slave Node Configuration

- Launch an instance on Amazon Linux 2 AMI with security group allowing SSH (port 22) as slave node.

- Connect to your instance with SSH.

- Install Java
  
```bash
sudo yum update -y
sudo amazon-linux-extras install java-openjdk11 -y
sudo yum install java-devel 
```

- Install Git
  
```bash
sudo yum install git -y
```

- **Configure ssh connection between master and slave nodes.**

- Go to the Jenkins Master server: 
  - Switch to "jenkins" user.

```bash
sudo su - jenkins
```

  - Generate a public and private key with keygen.

```bash
ssh-keygen
```
  - Press enter for every question to continue with default options. 

  - Check ".ssh" folder and see public(id_rsa.pub) and private keys(id_rsa). 

```bash
cd .ssh
ls
```
  - We need to copy public key to slave node.

```bash
cat id_rsa.pub
```
  - Select and copy all the codes in id_rsa.pub.

- Go to the /root/.ssh folder on the slave node instance.

```bash
sudo su
cd /root/.ssh
```

- Open the "authorized_keys" file with an editor and paste the code that you copied from public key(id_rsa.pub). Save "authorized_keys" file.

- Get the slave node ip:

```bash
ifconfig
```
- Copy ip number.

- Go to the Jenkins Master server and test ssh connection.

```bash
ssh root@<slave-node-ip-number>
exit
```

- **The second one is to copy agent file from Jenkins server to slave node.**

- Go to the "/root" folder on the slave node instance. Create a folder under "/root" and name it as "bin". Get agent file from Jenkins Master server.

```bash
mkdir bin
cd bin
wget http://<jenkins_master_ip>:8080/jnlpJars/slave.jar
```

- Go to Jenkins dashboard, click on "Manage Jenkins" from left hand menu.

- Select "Manage Nodes and Clouds"

- Click on "New Node" from left hand menu.

- Enter `SlaveNode-1` in "Node name" field and select `Permanent Agent`.

- Click `ok` button.

- Enter `This is a linux slave node for jenkins` in the description field.

- "Number of executors" is the maximum number of concurrent builds that Jenkins may perform on this node. Enter `2` in this field.

- An agent needs to have a directory dedicated to Jenkins. Specify the path to this directory on the agent. Enter `/usr/jenkins` in the "Remote root directory" field.

- Enter `Linux` in the "Labels" field.

- Select `Launch agent via execution of command on the master` from dropdown menu in the "Launch method" field.

- Enter `ssh root@<slave_ip> java -jar /root/bin/slave.jar` in the "Launch command" field.

- Select `Keep this agent online as much as possible` from dropdown menu in the "Availability" field.

- Click `Save`.

- Check the console logs in case of failure to launch agent node. If there is a approvement issue go to `Manage Jenkins`,  select the `In-process Script Approval`, `approve` the script.

- Go to Jenkins dashboard. Check master and slave nodes on the left hand menu. 

## Part 2 - Free Style Project on Slave Node

- Open your Jenkins dashboard and click on `New Item` to create a new job item.

- Enter `slave-job` then select `free style project` and click `OK`.

  - Enter `My first simple free style job on slave node` in the description field.

  - Find the `General` section, click "Restrict where this project can be run" and enter `Linux` for "Label Expression"

  - Go to `Build` section and choose "Execute Shell Command" step from `Add build step` dropdown menu.

  - Write down just ``echo "Hello World, This is a job for slave node"` to execute shell command, in text area shown.

  - Click `apply` and `save`  buttons.

- Go to `slave job`.

- Select `Build Now` on the left hand menu

- Check "Build Queue" section to see build process running on slave node on the left hand menu. 

## Part 3 - Pipeline Project on Slave Node

- Go to the Jenkins dashboard and click on `New Item` to create a pipeline.

- Enter `simple-pipeline-on-slave-node` then select `Pipeline` and click `OK`.

- Enter `My first simple pipeline on slave node` in the description field.

- Go to the `Pipeline` section, enter following script, then click `apply` and `save`.

```text
pipeline {
    agent { label 'Linux' }
    stages {
        stage('build') {
            steps {
                echo "Clarusway_Way to Reinvent Yourself"
                sh 'echo second step'
                sh 'echo another step'
                sh 'sleep 30'
                sh '''
                echo 'working on'
                echo 'slave node'
                '''
          
            }
        }
    }
}
```

- Go to the project page and click `Build Now`.

- Explain the built results.

- Explain the pipeline script.