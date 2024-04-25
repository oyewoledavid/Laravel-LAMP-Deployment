# SECOND SEMESTER EXAM  
For this exam i am to automate the provisioning of two Ubuntu-based servers, named “Master” and “Slave”, using Vagrant.
On the Master node, i am to create a bash script to automate the deployment of a LAMP (Linux, Apache, MySQL, PHP) stack.
This script clone a PHP application from GitHub, install all necessary packages, and configure Apache web server and MySQL.  
I made use of functions in My bash script for the purpose of modularity and reusability.  
I used an Ansible playbook on the master node to execute the bash script on the Slave node and i verified that the PHP application is accessible through the VM’s IP address.  
I also Create a cron job to check the server’s uptime every 12 am.  

# SOLUTION
First of all i provisioned two ubuntu 20.04LTS focal64 server using vagrant, the vms are name "master" and "slave". here is an image of what the Vagrantfile configuration looks like.  
![Vagrantfile image](/images/Vagrantfile.jpg)  
You can download the Vagrant file here [VagrantFile](./Vagrantfile) and configure accordingly for testing purposes

after this i ran the command `vagrant up master` and `vagrant up slave` to boot up the two VMs follow by `vagrant ssh master` and `vagrant ssh slave` to ssh into both VMs respectively.  
That beign done, i went into my master node and created my bash script called `deployment.sh` which perform the following tasks.  
1. Update the target server repository  
2. Install the LAMP stack  
3. Install all neccesary dependecies, packages and extensions
4. Clone a PHP application from Github
5. Configure Apache web server and MySQL  
Here is a copy of my bash script [deployment.sh](deployment.sh)  
I used functions throughout the script for modularity and reusabilty purpose. 

## CONFIGURING ANSIBLE
Now that the bash script is ready, the next step is to configure our ansible so that we can execute our script on the slave node. In order to do this i took the following steps on the master node.
1. Generate ssh key for the master node using the command `ssh-keygen -t ed25519 -C "Ansible Master"`. The -t flag is to specify the type of ssh key we want to generate while the -C flag is to add comment. The command looks limke this  
![ssh-keygen](/images/ssh-keygen.jpg)
  
2. Copy the generated ssh key which is stored in `.ssh/id_ed25519.pub` into the slave VM `authorisedkeys` this can either be done mannually or remotely using the command `ssh-copy-id -i .ssh/id_ed25519.pub <ip address of the slave>` PS: do not include the `<>`. This command looks like this  
![ssh-copy-id](/images/ssh-copy-id.jpg)  
this key will be saved in the slave node `.ssh/authorisedkeys` file.  
  
3. Now we can go ahead and install ansible on our master node using the command `sudo apt install ansible`. When this installation is complete, we are ready to go to the next step where put our ansible to work.
  
4. Create a directory called `ansible` and create 3 files namely ansible.cgf (this will be our configuration file),inventory (this will contain list of our hosts ip addresses, you can choose to name this file hosts or any name convinient for you) and lamp_deployment.yml (this is our playbook which will contain all our tasks and how we want to execute them, you can name it anything convinient for you but it must be a .yml or .yaml file) here are mine for testing purposes.  
[ansible.cfg](ansible.cfg), [inventory](./inventory) and [lamp_deployment.yml](lamp_deployment.yml)
  
5. Now lets go and edit our /etc/hosts file, i do this so that i wouldn't have to expose my ip inside the invetory file i talked about in step 4 above. In order to do this run the command `sudo vim /etc/host` then input the ip address of the slave VM and gave it a name (this is the name i will use inside my inventory file) in this scenerio i named it slavenode, you can name your whatever is best for you, here is what the hosts file looks like  
![hosts file](/images/hosts.jpg), PS: whatever name you give here should be the same with you will specify in your inventory file
  
6. Now we can use ansible to ping our slave node from the master node, this is how we will be sure if we can run our ansible playbook on the slave node or not. we will execute the command `ansible all -m ping`. make sure you run this command from the ansible directory created earlier. PS: if we didn't create an ansible.cfg file as done earlier which points to our inventory file, we will use this command instead `ansible all -i inventory -m ping`. The following output will be gotten if everything is in the right place.  
![ping](/images/ping.jpg)  
  
7. Our result show that we can ping the slave node successfully so therefore we can go ahead and execute our ansible playbook with the following command `ansible-playbook lamp_deployment.yml`, run this command from your ansible directory. PS: if you didn't create an ansible.cfg file as done earlier which points to our inventory file, you should use this command instead `ansible all -i inventory lamp_deployment.yml`. The following output will be gotten if everything is in the right place. if the execution was succesfull you should see something like this. Here is a copy of my playbook [Playbook](lamp_deployment.yml)  
![ansible successful](/images/ansible%20sucess.jpg)
  
8. Get your slave ip by running the command `ip a`  
![IP](/images/ip.jpg)
  
9. Load your ip into your host machine browser and you should see the laravel app
![deployed page](/images/deployed%20page.jpg)
  
10. Check your Slave node to confirm if your crontab is installed succesfully, run the command `crontab -l` and you should see something like this.
![crontab -l](/images/crontab%20-l.jpg)
  
11. To confirm that my cronjob worked, i cat the /var/log/uptime.txt where i redirected the result of the command and here is the result.
![uptime](/images/uptime.jpg)