Ansible Refactoring & Static Assignments (Imports and Roles)

Connect to your Jenkins-Ansible server terminal by ssh on your terminal
(screenshot)

reate a directory to store Jenkins artifacts:
bash
Copy code
sudo mkdir /home/ubuntu/ansible-config-artifact


Change the permissions to ensure Jenkins can write to it:
bash
Copy code
sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact
(Screenshot)

Go to the Jenkins dashboard.login on port 8080 in our case we will login with
http://51.21.47.126:8080/

now go navigate to manage jenkins-->plugins-->available plugins
(screenshot)
Search for the Copy Artifact plugin, then install it without restarting Jenkins.
(Screenshot)
Create a New Freestyle Project:

In Jenkins, create a new Freestyle Project and name it save_artifacts.
(screenshot)
Set it to be triggered by the completion of your existing Ansible job (configure this under Build Triggers and choose "Build after other projects are built" and type in "Ansible" as the project to watch).
(screenshot)
In the project settings, set Discard Old Builds to keep only the last 2-5 builds, to save space
(Screenshots)
Configure Artifact Copying:

In the Build section of save_artifacts, add a Copy artifacts from another project step.
Set Source Project to your Ansible job(named "ansible").
Set the Target Directory to /home/ubuntu/ansible-config-artifact
(Screenshot)
Test Your Setup:

Make a small change (like editing README.MD) in your Ansible repository’s master branch and commit it.
Check if both Jenkins jobs (ansible and save_artifacts) run in sequence.
(screenshot)
Verify that files are saved in /home/ubuntu/ansible-config-artifact
(screenshot)

If you encounter a permission error in the build on the save_artifacts project like the screenshot below
(screesnhot)
Go to your jenkin-ansible server and run sudo chmod 755 /home/ubuntu
(screenshot)
make changes on your github repository and your save atifact should run successfully
(screenshot)
(screenshot)
Verify that files are saved in /home/ubuntu/ansible-config-artifact.
(screesnhot)
