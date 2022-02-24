## Inventory

![image](https://user-images.githubusercontent.com/81763406/155572531-acf81ec3-75f8-47a4-b08e-defea0283561.png)

> My inventory file is relatively simple, as the load balancer is the only device the Ansible agent will make changes on. I've specified the group name as 'lb', with the management IP of 172.29.224.100, which is on the same network as my WSL adapter, where my Ansible agent sits. The SSH specific variables of admin and user_pass are defined. With this, we are ready to go execute playbooks.
---
## Playbooks
##### *For all playbooks, we specify the 'hosts' key to point to our 'lb' group, which is essentially our only device for this lab. The connection type of 'local' is used, which just means that playbooks are run on the local controller, as opposed to placing files on the managed node to perform the commands/configuration.*

#### [Changing device name](https://github.com/joshchontw/Lab2-F5LoadBalancing-with-Ansible/blob/main/playbooks/change-device-name.yaml)
![image](https://user-images.githubusercontent.com/81763406/155572885-94f1128d-6775-44ca-b2a8-cdbb1fc3f60e.png)

> The module 'f5networks.f5_modules.bigip_hostname' allows us to change the device name, which is set to 'bigip1' by default. The key 'hostname' is where we provide the device name. F5 is different from Cisco where we have to define the 'provider' or 'device' credentials again in the playbook, versus taking it from the inventory file. After we run this playbook with the command 'ansible-playbook change-device-name.yaml -i inventory/hosts.ini' from our main working directory, the device name is changed.
---
#### [Changing admin password](https://github.com/joshchontw/Lab2-F5LoadBalancing-with-Ansible/blob/main/playbooks/change-admin-password.yaml)
![image](https://user-images.githubusercontent.com/81763406/155573865-424b2155-6510-48e1-80b6-15bf92e97a64.png)

> The module 'f5networks.f5_modules.bigip_user' allows us to change the admin password. We provide the user with the 'username_credential' key and define the new password with the 'password_credential' key. The command to run this playbook is the same as above, just changing the playbook name (this will be the case for all other playbooks).
---
#### [Creating pool](https://github.com/joshchontw/Lab2-F5LoadBalancing-with-Ansible/blob/main/playbooks/create-pool.yaml)
![image](https://user-images.githubusercontent.com/81763406/155574600-5a2d0752-db25-4884-9fee-24f7657f0425.png)

> The module 'f5networks.f5_modules.bigip_pool' allows us to create a pool of web servers. We must provide the name of the pool, as well as the method of load balancing. In our case, a round-robin style is sufficient. Currently, the module does not allow us to add pool members, so a separate playbook is required. 
---
#### [Adding member to pool](https://github.com/joshchontw/Lab2-F5LoadBalancing-with-Ansible/blob/main/playbooks/add-pool-member.yaml)
![image](https://user-images.githubusercontent.com/81763406/155576707-a7b43723-109a-457c-8ee1-e4a8ab5c4572.png)

> The module 'f5networks.f5_modules.bigip_pool_member' allows us to create members and add them to a pool all in one go. We must specify the pool these members will join (so the pool has to be created first). The 'host' key needs the IP address of the web server and the 'name' key is whatever we want it to be (ideally, it should be descriptive). The last thing we need to specify is the port on which the members will listen, and in our case it is the HTTP port of 80. As we have two web servers, I ensured both members were added by adding a loop with the two members defined with a dictionary.
---
#### [Creating virtual server](https://github.com/joshchontw/Lab2-F5LoadBalancing-with-Ansible/blob/main/playbooks/create-virtual-server.yaml)
![image](https://user-images.githubusercontent.com/81763406/155574215-cdd03704-5f15-4da8-9d07-68c291ae7c36.png)

> The module 'f5networks.f5_modules.bigip_virtual_server' allows us to create a virtual server. In essence, the virtual server is the virtual IP that clients connect to, and the load balancer picks it up from there to direct traffic to pool members based on a pre-defined method (eg. round robin, least connection, etc). The 'destination' key is the virtual IP that clients will connect to. 'port' is the port that clients connect to. In our case, we have HTTP web servers, so the port is 80. We have to associate our virtual IP with a pool that it will service, and we provide the pool we've created. The final value to provide is the type of network address translation we want applied to our client. 'Automap' translates the client IP to an IP of the egress VLAN heading toward the servers.
---
#### [Retrieving device facts](https://github.com/joshchontw/Lab2-F5LoadBalancing-with-Ansible/blob/main/playbooks/get-facts.yaml)
![image](https://user-images.githubusercontent.com/81763406/155582062-8ee1f608-c5a2-4b16-a962-15bc5217fcf4.png)

> The module 'f5networks.f5_modules.bigip_device_info' allows us to gather information about how the device is configured. Under the 'gather_subset' key, we can specify specific parts of the configuration that we want to see. Using the 'register' construct, we can save the output to a variable. The next task in the playbook is to transfer the output variable to a file, using the 'copy' module. We must specify the content, and we bring in the variable we saved earlier ('configuration'). The filter 'to_nice_json' makes sure the output is in a human readable format. The 'dest' key points the agent to where twe want the file saved. The facts file is called 'F5facts'.
