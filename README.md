# AltSchool-Second-Semester-Project

Master Slave VM deployment, LAMP + Laravel installation and Ansible configurations

## Parent Folder Tree

Ansible:

    - files:  
        - LAMP+Laravel.sh
    - ansible.cfg  
    - inventory.ini  
    - playbook.yml  

LAMP+Laravel.sh  
MS-Deploy.sh  
README.md

The Ansible folder contains the documents required to run the Ansible configuration on the slave machine:

* Files folder contains the file that will be copied using the copy module in the playbook to the slave machine to execute.  
   * LAMP+Laravel.sh: a shell script to install LAMP stack and Laravel; it contains variables that should be defined before running the Ansible playbook.

* ansible.cfg: Ansible configuration file.

* inventory.ini: contains the details of the host the playbook will run on; in this case, only the IP of the slave machine should be inside, which is sufficient to execute the playbook.

* playbook.yml: the Ansible playbook; it contains the actions Ansible will carry out upon execution.

LAMP+Laravel.sh: a shell script to install LAMP stack and Laravel; it contains variables that should be defined before running the script.

MS-Deploy.sh: contains configuration to automate the deployment of two machines.

README.md: that's me! 🙂

### Variables to declare before running script
File name: MS-Deploy.sh

- MVM_IP="" - master IP address  
- SVM_IP="" - slave IP address  
- DB_USER='' - new username  
- USER_PASS='' - new user password  

File name: LAMP+Laravel.sh

- DB_NAME='' - MySQL database name  
- DB_USER='' - username, the user will be granted permission on database  
- USER_PASS='' - user password  
- ADMIN_EMAIL='' - serveradmin email
- SERVER_IP='' - server IP either master or slave depending on which machine you are running the script

## How to run script

1. open terminal and go to path directory

        cd /path-to-file/

2. Run MS-Deploy.sh using the below to spin your master and slave machine.  
   
        bash MS-Deploy.sh

3. ssh into the master machine.

        vagrant ssh master

4. switch to your root user: ```sudo su```
5. Switch to parent directory, and the vagrant folder then Run the LAMP+Laravel.sh file.

    	cd /
	    cd vagrant
	    bash LAMP+Laravel.sh
   * if you encounter window line ending errors ('$'\r': command not found), do the below:

        	sudo apt install dos2unix
	        dos2unix LAMP+Laravel.sh
   * then run the LAMP+Laravel script.

6. Type your master machine IP in your browser, Laravel application will be dsplayed.  
	![My Image](/assets/master.png)
7. Prerequisite to run ansible configuration:
	Establish ssh connection with slave using the below:  
    * on the master vm, go to the user you want to establish a connection with and generate an ssh key.

			su $PrefferedUser
			ssh-keygen -t rsa -b 2048 -N '' -f /home/$PrefferedUser/.ssh/id_rsa

	* copy the id to the slave and input credentials
	
    		ssh-copy-id $PrefferedUser@$SlaveIP
			yes option and input the preffered user on the slave password

	* on the slave machine, go to /etc/ssh/sshd_config file

			cd /etc/ssh/
			vi sshd_config and change PasswordAuthentication yes to PasswordAuthentication no
			save and exit file
	ps: by default, the sshd_config file has an input of PasswordAuthentication no but this was changed during the execution of the MS-Deploy.sh script so we can log into the preffered user and register our generated ssh key. This file can only be edited by the root user, you can modify file permission - not recommended.

8. Run your ansible playbook - this playbook copies the LAMP+Laravel script in the files directory to the slave machine and executes it; it also add a cron job to confirm uptime. Use the below command:
	
        ansible-playbook -i inventory.ini playbook.yml

    Expected output  
	![My Image](/assets/ansible.png)

9.  Type your slave machine IP in your browser, Laravel application will be dsplayed.  
	![My Image](/assets/slave.png)


### Cheers ✌🐱‍👤