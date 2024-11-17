## Self-Study Documentation: Ansible Refactoring and Static Assignments with Jenkins Integration


### **1. Setting Up Jenkins to Save Artifacts**

#### **Step 1.1: Connect to Jenkins-Ansible Server**
- Use SSH to connect to your Jenkins-Ansible server.
```bash
ssh <your-username>@<server-ip>
```
Screenshot: [SSH to Jenkins Server](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/ssh%20into%20you%20jenkins%20server.png)

#### **Step 1.2: Create Directory for Artifacts**
- Create a directory to store Jenkins artifacts.
```bash
sudo mkdir /home/ubuntu/ansible-config-artifact
sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact
```
Screenshot: [Create and Change Permissions](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20director%20and%20change%20permission.png)

#### **Step 1.3: Install Plugins in Jenkins**
1. Open Jenkins on your browser: `http://<server-ip>:8080`.
2. Navigate to **Manage Jenkins > Plugins > Available Plugins**.
3. Search for and install the **Copy Artifact** plugin without restarting.
Screenshot: [Install Copy Artifact Plugin](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/install%20Copy%20Artifact%20without%20restarting.png)

#### **Step 1.4: Create a Freestyle Project**
1. In Jenkins, create a new **Freestyle Project** called `save_artifacts`.
2. Under **Build Triggers**, enable "Build after other projects are built" and set the triggering project to `ansible`.
3. In **Build Discarder**, keep only the last 2–5 builds to save space.
Screenshot: [Freestyle Project Setup](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20a%20new%20freestyle%20project%20in%20jenkins.png)

#### **Step 1.5: Configure Artifact Copying**
- In the **Build** section, add a **Copy artifacts from another project** step:
  - Source Project: `ansible`.
  - Target Directory: `/home/ubuntu/ansible-config-artifact`.
Screenshot: [Build Steps](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/buid%20steps%20configurations.png)

#### **Step 1.6: Test the Setup**
- Make a small change in your Ansible repository (e.g., edit `README.md`) and commit it.
- Verify both `ansible` and `save_artifacts` jobs run in sequence.
Screenshot: [Test Setup](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/jenkins%20and%20ansible%20run%20in%20sequence.png)

---

### **2. Refactoring Ansible Code**

#### **Step 2.1: Set Up a New Branch**
- Pull the latest changes and create a new branch:
```bash
git checkout -b refactor
```
Screenshot: [Create a New Branch](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/creating%20a%20new%20branch.PNG)

#### **Step 2.2: Organize Playbooks**
1. Create a `site.yml` file in the `playbooks` directory.
2. Create a `static-assignments` folder at the root of your repository and move `common.yml` into it.
3. Update `site.yml` to import `common.yml`:
```yaml
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```
Screenshot: [Update site.yml](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20the%20site%20yml.PNG)

#### **Step 2.3: Verify Folder Structure**
Ensure your repository is organized as follows:
```
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
```

#### **Step 2.4: Run the Refactored Playbook**
- Run the playbook for the `dev` environment to confirm it works:
```bash
ansible-playbook -i inventory/dev.ini playbooks/site.yml
```
Screenshot: [Run Playbook](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/run%20your%20playbook.PNG)

---

### **3. Add a New Playbook to Delete Wireshark**

#### **Step 3.1: Create `common-del.yml`**
- Add tasks to delete Wireshark for different server types in `static-assignments/common-del.yml`:
```yaml
---
- name: Remove Wireshark from web, nfs, and db servers
  hosts: webservers, nfs, db
  tasks:
    - name: Delete Wireshark on RHEL/CentOS servers
      yum:
        name: wireshark
        state: removed
      when: ansible_facts['pkg_mgr'] == "yum"

- name: Remove Wireshark from LB server
  hosts: lb
  tasks:
    - name: Delete Wireshark on Ubuntu/Debian servers
      apt:
        name: wireshark
        state: absent
        purge: yes
      when: ansible_facts['pkg_mgr'] == "apt"
```
Screenshot: [Create common-del.yml](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/create%20common%20del%20yml.PNG)

#### **Step 3.2: Update `site.yml`**
Replace `common.yml` with `common-del.yml` in `site.yml`:
```yaml
---
- hosts: all
- import_playbook: ../static-assignments/common-del.yml
```

#### **Step 3.3: Test Wireshark Removal**
1. Run the updated playbook:
```bash
ansible-playbook -i inventory/dev.ini playbooks/site.yml
```
2. Verify Wireshark removal on each server:
```bash
wireshark --version
```
Screenshot: [Verify Wireshark Removal](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/verify%20that%20wireshark%20is%20successfully%20deleted.PNG)

---

### **4. Troubleshooting**

#### **Permission Denied Error**
- If you encounter a "Permission denied (publickey)" error:
1. Start the SSH agent:
```bash
eval $(ssh-agent -s)
```
2. Add your `.pem` key:
```bash
ssh-add <path-to-pem-file>
```
Screenshot: [Set Up SSH Agent](https://github.com/Prince-Tee/Ansible_Refactoring/blob/main/screenshot%20from%20my%20env/setting%20up%20ssh%20agent.PNG)

---
#  Setting up UAT Web Servers with Ansible

---

## 1. Launch Two EC2 Instances
### Steps:
1. **Log in to AWS Management Console**:
   - Navigate to the **EC2 Dashboard**.
   - Click **Launch Instance**.

2. **Select AMI**:
   - Choose **RHEL 8** as the operating system.

3. **Configure Instances**:
   - Set **Instance Count** to 2.
   - Assign instance names:
     - **Web1-UAT**
     - **Web2-UAT**

4. **Security Group**:
   - Allow the following ports:
     - **SSH (22)**
     - **HTTP (80)**

5. **Launch Instances**:
   - Complete the setup.
   - Note the **private IPs** for inventory configuration.

---

## 2. Connect to the Jenkins-Ansible Server
### Steps:
1. **Start SSH Agent**:
   ```bash
   eval $(ssh-agent)
   ssh-add <path-to-your-private-key>
   ```

2. **Connect to Jenkins-Ansible Server**:
   ```bash
   ssh -i <path-to-your-private-key> ubuntu@<jenkins-ansible-server-ip>
   ```

---

## 3. Create a Role for Web Servers
### Option 1: Using `ansible-galaxy` (Recommended)
1. **Navigate to the Project Directory**:
   ```bash
   cd ~/ansible-config-mgt
   mkdir -p roles
   cd roles
   ```

2. **Initialize Role**:
   ```bash
   ansible-galaxy init webserver
   ```

3. **Clean Up Unnecessary Files**:
   ```bash
   cd webserver
   rm -rf files vars templates tests
   ```

---

## 4. Configure the Inventory File
### Steps:
1. **Open UAT Inventory File**:
   ```bash
   nano inventory/uat.ini
   ```

2. **Add UAT Server Details**:
   ```yaml
   [uat-webservers]
   <Web1-UAT-Private-IP> ansible_ssh_user=ec2-user
   <Web2-UAT-Private-IP> ansible_ssh_user=ec2-user
   ```

---

## 5. Update Ansible Configuration
### Steps:
1. **Create Global Config Directory (If Missing)**:
   ```bash
   sudo mkdir -p /etc/ansible
   ```

2. **Create/Edit `ansible.cfg`**:
   ```bash
   sudo nano /etc/ansible/ansible.cfg
   ```

3. **Set Roles Path**:
   ```ini
   roles_path = /home/ubuntu/ansible-config-mgt/roles
   ```

---

## 6. Write the `webserver` Role Logic
### Steps:
1. **Edit Tasks File**:
   ```bash
   nano roles/webserver/tasks/main.yml
   ```

2. **Add the Following Tasks**:
   ```yaml
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

   - name: start service httpd
     become: true
     ansible.builtin.service:
       name: httpd
       state: started

   - name: remove /var/www/html/html directory
     become: true
     ansible.builtin.file:
       path: /var/www/html/html
       state: absent
   ```

---

## 7. Reference the `webserver` Role
### Steps:
1. **Navigate to `static-assignments` Folder**:
   ```bash
   cd ~/ansible-config-mgt/static-assignments
   ```

2. **Create `uat-webservers.yml`**:
   ```bash
   nano uat-webservers.yml
   ```

3. **Add the Role**:
   ```yaml
   ---
   - name: Configure UAT Webservers
     hosts: uat-webservers
     roles:
       - webserver
   ```

---

## 8. Reference in `site.yml`
### Steps:
1. **Navigate to Playbooks Directory**:
   ```bash
   cd ~/ansible-config-mgt/playbooks
   ```

2. **Edit `site.yml`**:
   ```bash
   nano site.yml
   ```

3. **Include Reference**:
   ```yaml
   ---
   - hosts: all
     import_playbook: ../static-assignments/common.yml

   - hosts: uat-webservers
     import_playbook: ../static-assignments/uat-webservers.yml
   ```

---

## 9. Commit and Push Changes
### Steps:
1. **Navigate to Project Root**:
   ```bash
   cd ~/ansible-config-mgt
   ```

2. **Commit Changes**:
   ```bash
   git add .
   git commit -m "Added uat-webservers role and updated site.yml"
   git push origin refactor
   ```

3. **Create a Pull Request**:
   - Merge changes to the `main` branch.

---

## 10. Run the Playbook
### Steps:
1. **Execute Playbook**:
   ```bash
   ansible-playbook -i inventory/uat.ini playbooks/site.yml
   ```

---

## 11. Verify Configuration
### Steps:
1. **SSH into UAT Server**:
   ```bash
   ssh -i <path-to-your-private-key> ec2-user@<Web1-UAT-Private-IP>
   ```

2. **Check Apache Status**:
   ```bash
   sudo systemctl status httpd
   ```

3. **Verify Website Files**:
   ```bash
   ls -l /var/www/html
   ```

---


### **Conclusion**
This guide walks you through setting up Jenkins for artifact management, refactoring Ansible configurations, and adding tasks to manage server configurations effectively. This documentation also ensures you can replicate the UAT web server setup independently, troubleshoot potential issues, and verify configurations effectively.Test each step thoroughly to ensure successful implementation. For more information, refer to the screenshots linked for each step.