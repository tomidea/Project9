## INSTALL AND CONFIGURE JENKINS SERVER
#### Step 1 – Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"
2. Install JDK (since Jenkins is a Java-based application)
- sudo apt update
- sudo apt install default-jdk-headless
3. Install Jenkins
- wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
- sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
- sudo apt update
- sudo apt-get install jenkins

4. Make sure Jenkins is up and running

- sudo systemctl status jenkins
img

5. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

6. Perform initial Jenkins setup.
From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080
img

You will be prompted to provide a default admin password
Retrieve it from your server:

- sudo cat /var/lib/jenkins/secrets/initialAdminPassword

 Then you will be asked which plugings to install – choose suggested plugins.
Once plugins installation is done – create an admin user and you will get your Jenkins server address.

The installation is completed!

img
    
    
   ### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
    
    1. Enable webhooks in your GitHub repository settings
    2. Go to Jenkins web console, click "New Item" and create a "Freestyle project"
     - To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself
     - In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.
    - You can open the build and check in "Console Output" if it has run successfully.
    img
    
    3. Click "Configure" your job/project and add these two configurations
      - Configure triggering the job from GitHub webhook:
      - Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts"
      - Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.
        img
    
   ## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH
   ##### Step 3 – Configure Jenkins to copy files to NFS server via SSH
    1. Install "Publish Over SSH" plugin.
       - On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.
       - On "Available" tab search for "Publish Over SSH" plugin and install it
    2. Configure the job/project to copy artifacts over to NFS server.
        - On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.
        - Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

                Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
                Arbitrary name
                Hostname – can be private IP address of your NFS server
                Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
                Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
       Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.
