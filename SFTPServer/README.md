SFTPServer
-------


In this project, I intend to spin up a SFTPServer using Ansible playbook. This server would allow uploading files using public key access. The SFTPServer will be setup on AWS EC2 instance. Current configuration will spawn t2.micro instances using 8 GB of general purpose disks with Ubuntu trusty image in us-east-1 region.

Installation
============

Please ensure the following things are installed or in place:
- [Ansible](http://docs.ansible.com/ansible/intro_installation.html)
- Install Boto module using `sudo pip install boto`
- [Boto AWS Configuration](http://docs.pythonboto.org/en/latest/boto_config_tut.html)
- Update the ansible.cfg file to point to your AWS `key_name.pem`
- Make sure to check the permissions on your AWS `key_name.pem` file (Run `sudo chmod 400 key_name.pem`)

Configuration changes
=====================
There are three different roles defined:
- create-user
- provision-ec2
- vsftpd

#### create-user ####
=========================
This role takes care of creating a public key repository on local system and generates public key file for each user. The users are defined in create-user/vars/main.yml as part of `vsftpd_users` variable. This variable is later referenced in create-user/tasks/main.yml. Please update the list of vsftpd_users in create-user/vars/main.yml as per requirements.

#### provision-ec2 ####
========================
This role takes care of provisioning ec2 instances on AWS. It then waits for the instances to boot up by checking the ssh port. The variables used for provisioning ec2 instances are defined in provision-ec2/vars/main.yml. Please update the provision-ec2/vars/main.yml file to match the requirements of your SFTP server infrastructure.

#### vsftpd ####
========================
This role checks if the latest version of vsftpd is installed on the ec2 instance. It then proceeds to add each user defined in vsftpd/vars/main.yml to the ec2 instances. It then adds public key files of the users to the ec2 instances. At the end of this role, the vsftpd service is restarted by the handler. Please update the list of vsftpd_users in vsftpd/vars/main.yml as per requirements.

How to run
==========
Use the following command to launch the playbook from command line:
`ansible-playbook -vvvv site.yml`
You can get rid of `-vvvv` to reduce the verbosity of the output.

Testing
=======
We can then check the SFTP connection for each and every user by using the following command:
sftp -i "~/path/to/private_key" ftpuser@ec2_ip

For example:
sftp -i "~/ansible/files/authorized_keys.ftpuser" ftpuser@52.201.227.191
