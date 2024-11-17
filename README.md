Ansible Refactoring & Static Assignments (Imports and Roles)

Connect to your Jenkins-Ansible server terminal by ssh on your terminal
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/ssh%20into%20you%20jenkins%20server.png)

reate a directory to store Jenkins artifacts:
bash
Copy code
sudo mkdir /home/ubuntu/ansible-config-artifact
![(Screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20director%20and%20change%20permission.png)

Change the permissions to ensure Jenkins can write to it:
bash
Copy code
sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact
![(Screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20director%20and%20change%20permission.png)

Go to the Jenkins dashboard.login on port 8080 in our case we will login with
http://51.21.47.126:8080/

![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/go%20to%20your%20jenkins%20server.png)

now go navigate to manage jenkins-->plugins-->available plugins
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/available%20plugins.png)
Search for the Copy Artifact plugin, then install it without restarting Jenkins.
![(Screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/install%20Copy%20Artifact%20without%20restarting.png)
Create a New Freestyle Project:

In Jenkins, create a new Freestyle Project and name it save_artifacts.
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20a%20new%20freestyle%20project%20in%20jenkins.png)
Set it to be triggered by the completion of your existing Ansible job (configure this under Build Triggers and choose "Build after other projects are built" and type in "Ansible" as the project to watch).
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/buid%20triggers%20configurations.png)
In the project settings, set Discard Old Builds to keep only the last 2-5 builds, to save space
![(Screenshots)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/set%20Discard%20Old%20Builds%20to%20keep%20only%20the%20last%202%205%20builds.png)
Configure Artifact Copying:

In the Build step section of save_artifacts, add a Copy artifacts from another project step.
Set Source Project to your Ansible job(named "ansible").
Set the Target Directory to /home/ubuntu/ansible-config-artifact
![(Screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/buid%20steps%20configurations.png)
Test Your Setup:

Make a small change (like editing README.MD) in your Ansible repository’s master branch and commit it.
Check if both Jenkins jobs (ansible and save_artifacts) run in sequence.
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/jenkins%20and%20ansible%20run%20in%20sequence.png)
Verify that files are saved in /home/ubuntu/ansible-config-artifact
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/verify%20that%20artifacts%20are%20save%20on%20your%20jenkins%20server.png)

If you encounter a permission error in the build on the save_artifacts project like the screenshot below
![(screesnhot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/error%20encountered%20in%20save%20atifacts.png)
Go to your jenkin-ansible server and run sudo chmod 755 /home/ubuntu
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/run%20chmod%20755%20home%20ubutu%20to%20solve%20error.png)
make changes on your github repository and your save atifact should run successfully
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/save%20artifact%20successfull.png)
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/save%20artifact%20successfull2.png)
Verify that files are saved in /home/ubuntu/ansible-config-artifact.
![(screesnhot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/verify%20that%20artifacts%20are%20save%20on%20your%20jenkins%20server.png)

Refactor Ansible Code with Imports and Roles
Set Up a New Branch for Refactoring:

Pull the latest changes from the main branch of your Ansible repository.
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/Pull%20the%20latest%20changes%20from%20the%20main%20branch%20of%20your%20Ansible%20repository.PNG)
Create a new branch for refactoring:
bash
Copy code
git checkout -b refactor
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/creating%20a%20new%20branch.PNG)
Create site.yml for Managing Imports:

In your playbooks directory, create a new file called site.yml.
This file will serve as the main entry point for all your playbooks, so you can centralize your configurations.
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20the%20site%20yml.PNG)
Organize Playbooks with static-assignments:

Create a folder named static-assignments at the root of your Ansible repository.
bash
Move the existing common.yml file into static-assignments.
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20the%20site%20yml.PNG)
Set Up Imports in site.yml:

Open site.yml and add the following content to import common.yml:
yaml
Copy code
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

Verify Folder Structure:

Ensure your folder structure looks like this:
arduino
Copy code
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
Run the Playbook:

Run the playbook for your dev environment to confirm it works:
bash
Copy code
ansible-playbook -i inventory/dev.ini playbooks/site.yml
  
Add a New Playbook to Delete Wireshark
Create common-del.yml:

In the static-assignments folder, create a new playbook named common-del.yml.
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20common%20del%20yml.PNG)
Add tasks to delete Wireshark from different server types
```
---
- name: Remove Wireshark from web, nfs, and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: Delete Wireshark on RHEL/CentOS servers
      yum:
        name: wireshark
        state: removed
      when: ansible_facts['pkg_mgr'] == "yum"

- name: Remove Wireshark from LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Delete Wireshark on Ubuntu/Debian servers
      apt:
        name: wireshark
        state: absent
        purge: yes
      when: ansible_facts['pkg_mgr'] == "apt"
```
Update site.yml to Include common-del.yml:

Replace the import_playbook line in site.yml with:
yaml
Copy code
---
- hosts: all
- import_playbook: ../static-assignments/common-del.yml
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/update%20site%20yml%20file.PNG)
Run the Updated Playbook:

Run the playbook again for your dev environment:
bash
Copy code
ansible-playbook -i inventory/dev.ini playbooks/site.yml
![(screenshot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/run%20your%20playbook.PNG)
![screenshot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/run%20the%20playbook.png)
if you encounter the below error in this screenhot,  "Permission denied (publickey)" errors, this means that Ansible could not authenticate to your servers using the SSH key configured for these connections
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/permission%20error%20encountered.PNG)

Set Up SSH Agent and Import pem Key
follow these steps to use your .pem key instead:

Start the SSH Agent

eval $(ssh-agent -s)
Output example:

Agent pid 599
Add the .pem Key
Replace <path-to-pem-file> with the location of your .pem file 

ssh-add "/home/ubuntu/lamproject.pem"
Verify the Key Added
List the keys added to the SSH agent to ensure your .pem file is included:

ssh-add -l
![(screenshot)](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/setting%20up%20ssh%20agent.PNG)
Run the playbook again for your dev environment:
bash
Copy code
ansible-playbook -i inventory/dev.ini playbooks/site.yml

![screenshot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/run%20the%20playbook.png)

Verify Wireshark Removal:

Check each server to confirm Wireshark has been removed by running:
bash
Copy code
wireshark --version
![screenshot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/verify%20that%20wireshark%20is%20successfully%20deleted.PNG)
![screenshot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/check%20the%20servers%20if%20wireshark%20is%20removed.png)

if you encounter any issue that the ec2-user server still have wireshark installed Update the when Condition if Necessary: If your RHEL servers are using dnf (common in newer CentOS/RHEL versions), the when condition should be updated:

yaml
Copy code
when: ansible_facts['pkg_mgr'] in ["yum", "dnf"]
This ensures the task runs for both yum and dnf.


setting up the UAT web servers using an Ansible role called webserversetting up the UAT web servers using an Ansible role called webserver

Launch Two EC2 Instances
Log in to the AWS Management Console:

Navigate to the EC2 Dashboard.
Click Launch Instance.
Select an AMI:

Choose RHEL 8 as the image for your instances.
Configure Instance Details:

Set the instance count to 2.
Assign appropriate names to the instances:
Web1-UAT
Web2-UAT

(screenshot)
Choose an existing security group or create a new one allowing:
SSH (port 22)
HTTP (port 80)
(screenshot)
Complete the Setup:

Launch the instances and note their private IP addresses for inventory configuration later.

Connect to Your Jenkins-Ansible Server
Open a terminal on your local machine.
Start the SSH agent:
bash
Copy code
eval $(ssh-agent)
ssh-add <path-to-your-private-key>
Connect to your Jenkins-Ansible server:
bash
Copy code
ssh -i <path-to-your-private-key> ubuntu@<jenkins-ansible-server-ip>
(screenshot)

Create a Role for Web Servers
Option 1: Use ansible-galaxy (Recommended)
Navigate to your project directory:
bash
Copy code
cd ~/ansible-config-mgt
mkdir -p roles
cd roles
Initialize the webserver role:
bash
Copy code
ansible-galaxy init webserver
Remove unnecessary directories and files:
bash
Copy code
cd webserver
rm -rf files vars templates tests
(screenshot)

Configure the Inventory File
Open the UAT inventory file:
bash
Copy code
nano inventory/uat.yml
(screenshot)
Add the private IPs of your UAT servers:
yaml
Copy code
[uat-webservers]
<Web1-UAT-Private-IP> ansible_ssh_user='ec2-user'
<Web2-UAT-Private-IP> ansible_ssh_user='ec2-user'
(screenshot)

Update ansible.cfg
Open the Ansible configuration file:
bash
Copy code
nano /etc/ansible/ansible.cfg
(screenshot)

if you encounter an error that the file does not exist

Create a Global Configuration File
Navigate to the Default Location:
bash
Copy code
sudo mkdir -p /etc/ansible
(screenshot)
Create and Edit ansible.cfg:
bash
Copy code
sudo nano /etc/ansible/ansible.cfg

update the roles_path with the code below:
ini
Copy code
roles_path = /home/ubuntu/ansible-config-mgt/roles
(screenshot)


Write the webserver Role Logic
Open the tasks/main.yml file:
bash
Copy code
nano roles/webserver/tasks/main.yml
(screenshot)
Add the following tasks:
yaml
Copy code
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
    (screenshot)

Referencing the 'Webserver' Role
Let’s carefully go through each step to achieve the goal without mistakes.

Step 1: Navigate to the Correct Directory
Ensure you’re in the ansible-config-mgt directory where all your configuration files and roles are stored. Use this command to confirm your location:

bash
Copy code
cd ~/ansible-config-mgt
Run the ls command to verify that the directory contains folders like inventory, roles, and playbooks.

Create uat-webservers.yml
Navigate to the static-assignments folder:

bash
Copy code
cd ~/ansible-config-mgt/static-assignments
Create a new file named uat-webservers.yml:

bash
Copy code
nano uat-webservers.yml
Add the following content:

yaml
Copy code
---
- name: Configure UAT Webservers
  hosts: uat-webservers
  roles:
    - webserver
(screenshot)    

Reference uat-webservers.yml in site.yml
Navigate to the playbooks directory:

bash
Copy code
cd ~/ansible-config-mgt/playbooks
Open the site.yml file:

bash
Copy code
nano site.yml

(screenshot)

Modify it to include a reference to uat-webservers.yml. Ensure your file looks like this:

yaml
Copy code
---
- hosts: all
  import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
  import_playbook: ../static-assignments/uat-webservers.yml
Save and exit the file.

Commit Your Changes
Now, let’s commit the changes to Git and push them to the repository.

Navigate to the root of your project:

bash
Copy code
cd ~/ansible-config-mgt

Stage the changes:

bash
Copy code
git add .

Commit the changes with a message:

bash
Copy code
git commit -m "Added uat-webservers role and updated site.yml"
Push the changes to your repository:

(screenshot)

bash
Copy code
git push origin <your-branch-name> (git push origin refactor)
(screenshot)

Create a Pull Request (PR) on your GitHub repository hosting platform  to merge these changes into the main branch.
(screenshot)
(screenshot)
(screenshot)
(screenshot)

Verify Jenkins Webhook
Once the PR is merged, ensure that the webhook triggers the Jenkins pipeline jobs:

Check Jenkins to see if two jobs ran:

The first job: Builds the configuration.
The second job: Deploys the files to /home/ubuntu/ansible-config-mgt on the Jenkins-Ansible server.

(screenshot)
(screenshot)

Confirm the files are updated on the Jenkins-Ansible server by SSHing into it:
then change the branch to main by running git checkout main and after that run git pull origin main to update
(screenshot)

then 
cd /home/ubuntu/ansible-config-mgt
ls
Verify the updated uat-webservers.yml and site.yml files are present
(screenshot)

Run the playbook with the UAT inventory file:

bash
Copy code
ansible-playbook -i /inventory/uat.ini playbooks/site.yml

(Screenshot)

Verify the Configuration
SSH into one of the UAT servers:
bash
Copy code
ssh -i <path-to-your-private-key> ec2-user@<Web1-UAT-Private-IP>
Check if Apache is running:
bash
Copy code
sudo systemctl status httpd

(Screenshot)

Verify the website files:
bash
Copy code
ls -l /var/www/html

(Screenshot)



Verify Web Servers
After the playbook runs successfully, note the public IP or DNS of your UAT web servers.

Access the servers from your browser to verify:

plaintext
Copy code
http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php
or

plaintext
Copy code
http://<Web2-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php
(Screenshot)
(screenshot)
