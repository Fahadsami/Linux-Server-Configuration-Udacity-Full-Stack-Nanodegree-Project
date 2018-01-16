# Linux-Server-Configuration-Udacity-Full-Stack-Nanodegree-Project
This is the last project toward Udacity Full Stack Web Developer Nanodegree. In this project, the [Catalog Web Application](https://github.com/yiyupan/Catalog-App-Udacity-Full-Stack-Nanodegree) from project 3 will be hosted by a Ubuntu Linux server on an Amazon Lightsail instance. A series of instructions will be presented below. You can visit http://18.218.99.181/ for the website deployed.

* Public IP address: 18.218.99.181
* SSH port: 2200
## Start a new Ubuntu Linux Server instance on Amazon Lightsail
1. Create an AWS account
2. Click **Create instance** button on the home page
3. Select **Linux/Unix** platform
4. Select **OS Only** and **Ubuntu** as blueprint
5. Select an instance plan
6. Name your instance
7. Click **Create** button

## SSH into your Server
1. Download private key from the **SSH keys** section in the **Account** section on Amazon Lightsail. The file name should be like _LightsailDefaultPrivateKey-us-east-2.pem_
2. Create a new file named **lightsail_key.rsa** under ~/.ssh folder on your local machine
3. Copy and paste content from downloaded private key file to **lightsail_key.rsa**
4. Set file permission as owner only : `$ chmod 600 ~/.ssh/lightsail_key.rsa`
5. SSH into the instance: `$ ssh -i ~/.ssh/lightsail_key.rsa ubuntu@18.218.99.181`

## Update all currently installed packages
1. Run `sudo apt-get update` to update packages
2. Run `sudo apt-get upgrade` to install newest versions of packages

##  Change the SSH port from 22 to 2200
1. Run `$ sudo nano /etc/ssh/sshd_config` to open up the configuration file
2. Change the port number from **22** to **2200** in this file
3. Save and exit the file
4. Restart SSH: `$ sudo service ssh restart`

## Configure the firewall
1. Check firewall status: `$ sudo ufw status`
2. Set default firewall to deny all incomings: `$ sudo ufw default deny incoming`
3. Set default firewall to allow all outgoings: `$ sudo ufw default allow outgoing`
4. Allow incoming TCP packets on port 2200 to allow SSH: `$ sudo ufw allow 2200/tcp`
5. Allow incoming TCP packets on port 80 to allow www: `$ sudo ufw allow www`
6. Allow incoming UDP packets on port 123 to allow NTP: `$ sudo ufw allow 123/udp`
7. Enable firewall: `$ sudo ufw enable`
8. Check out current firewall status: `$ sudo ufw status`
9. Update the firewall configuration on Amazon Lightsail website under **Networking**. Delete default SSH port 22 and add **port 80, 123, 2200**
10. Open up a new terminal and you can now ssh in via the new port 2200: `$ ssh -i ~/.ssh/lightsail_key.rsa ubuntu@18.218.99.181 -p 2200`

## Create a new user account **grader** and give **grader** sudo access
1. Create a new user account **grader**:`$ sudo adduser grader`
2. `$ sudo nano /etc/sudoers`
3. Create a file named grader under this path: `$ sudo touch /etc/sudoers.d/grader`
4. Edit this file: `$ sudo nano /etc/sudoers.d/grader`, add code `grader ALL=(ALL:ALL) ALL`. Save and exit

## Set SSH login using keys
1. Create an SSH key pair for **grader** using the `ssh-keygen` tool on your local machine. Save it in `~/.ssh` path
2. Deploy public key on development environment
    * On your local machine, read the generated public key
     `cat ~/.ssh/FILE-NAME.pub`
    * On your virtual machine
     `$ su -grader
      $ mkdir .ssh
      $ touch .ssh/authorized_keys
      $ nano .ssh/authorized_keys`
    * Copy the public key to this _authorized_keys_ file on the virtual machine and save
3. Run `chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys` on your virtual machine to change file permission
4. Restart SSH: `$ sudo service ssh restart`
5. Now you are able to login in as grader: `$ ssh -i ~/.ssh/grader_key -p 2200 grader@18.218.99.181`
6. You will be asked for grader's password. To unable it, open configuration file again: `$ sudo nano /etc/ssh/sshd_config`
7. Change `PasswordAuthentication yes` to **no**
8. Restart SSH: `$ sudo service ssh restart`

## Configure the local timezone to UTC

