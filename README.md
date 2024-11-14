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
