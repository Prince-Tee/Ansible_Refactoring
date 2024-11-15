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
![(screenshot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/run%20your%20playbook.PNG))
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

Verify Wireshark Removal:

Check each server to confirm Wireshark has been removed by running:
bash
Copy code
wireshark --version
![screenshot](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/verify%20that%20wireshark%20is%20successfully%20deleted.PNG)
if you encounter any issue that the ec2-user server still have wireshark installed Update the when Condition if Necessary: If your RHEL servers are using dnf (common in newer CentOS/RHEL versions), the when condition should be updated:

yaml
Copy code
when: ansible_facts['pkg_mgr'] in ["yum", "dnf"]
This ensures the task runs for both yum and dnf.