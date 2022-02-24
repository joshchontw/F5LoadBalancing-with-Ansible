# F5 Load Balancing with Ansible
## Topology

#### My lab consists of an F5 BIG IP load balancer with two web servers sitting behind it. A client machine will be making HTTP requests to the virtual IP address of the load balancer, and it will distribute those requests round-robin style to the two web servers. Each of the connections (client machine - load balancer, load balancer - web servers, load balancer - Ansible agent) is on their own network. 
![image](https://user-images.githubusercontent.com/81763406/154772366-b25fd3b1-632f-42e2-894e-e72bb5923fd7.png)


### Changing name of device

![image](https://user-images.githubusercontent.com/81763406/154860917-5ef8b76a-29c7-42fd-b75a-21f548b11d8b.png)
![image](https://user-images.githubusercontent.com/81763406/154860948-45537293-ae72-42b8-8a10-0d8c923d4010.png)

> The hostname has been changed from the default 'bigip'
----------------------------------------------------
### Changing admin password

![image](https://user-images.githubusercontent.com/81763406/154860976-c8857de6-1b2f-456d-96fd-fb6511da836f.png)

----------------------------------------------------
### Creating a pool

![image](https://user-images.githubusercontent.com/81763406/154861139-4a7b4748-1834-476b-8d24-f7fe168e6fb2.png)
![image](https://user-images.githubusercontent.com/81763406/154861197-5294f368-8506-4eb4-aa81-5b23027d4f23.png)

> The status is red as we have not added any members to the pool
----------------------------------------------------
### Creating and adding members to pool

![image](https://user-images.githubusercontent.com/81763406/154861744-b1990b07-35ce-4dd3-8d76-4df562c4aff6.png)
![image](https://user-images.githubusercontent.com/81763406/154861753-9a16d98f-de3d-4f87-9288-c6d88a06755c.png)
![image](https://user-images.githubusercontent.com/81763406/154861769-862666e8-8424-42bd-84ed-aac3639b316c.png)

> Two nodes have been created and added to our pool
----------------------------------------------------
### Creating a virtual server

![image](https://user-images.githubusercontent.com/81763406/154862985-922f13f7-f56b-4491-848c-4924b4f88ad0.png)
![image](https://user-images.githubusercontent.com/81763406/154863024-9befd3a6-2671-4d26-ad8b-b109542eeecf.png)

> Virtual server has been added
----------------------------------------------------
### Getting device facts

Once our intended configuration has been put in place, a file can be generated with pertinent details that we wish to view.

![image](https://user-images.githubusercontent.com/81763406/154863229-c3b86587-8400-402c-9405-08f598a9137b.png)

> A snippet of the output file in JSON format:

![image](https://user-images.githubusercontent.com/81763406/154863258-fee65767-cbbb-4b7e-99e5-7a82b86033b4.png)

----------------------------------------------------
### Verifying that the load balancer is working as intended

On the client machine, we will direct our browser to the virtual IP address of our load balancer. From there, the load balancer should perform its function and distribute the requests round-robin style.

![image](https://user-images.githubusercontent.com/81763406/154864425-ec51df78-bb16-4499-88dd-e1f6f7f391f0.png)
----------------------------------------------------
![image](https://user-images.githubusercontent.com/81763406/154864437-22158019-4298-4ced-9c3b-52885194fa92.png)

> Things are working as expected

![image](https://user-images.githubusercontent.com/81763406/154864490-8d73e89c-cff5-4831-923c-213ffa1615ba.png)

> As we can see in our pool statistics, both web servers have received the same amount of connections


