# Wordpress_ansible_ibmcloud

Demo ansible package to install Wordpress in a highly available 3-tier configuration on IBM Cloud
 - IBM Cloud Load Balancer
 - httpd app server
 - mariadb 

This is written as a capability demostration of building high availability web sites using IBM Cloud IaaS and secure networking. 



This pacakge supports two deployment options: 
- Single site deployment of multiple httpd webservers with a single Mariadb database host with an IBM Cloud Load Balancer (CLB) as a local LB. 
- Dual site high availability configuration, with webservers and DB's deployed in two data centers, each with CLBs, fronted by IBM Cloud Internet Services (CloudFlare) as a global load balancer. The Wordpress database is replicated master-master over the IBM Cloud private network. 

Deployment architecture is determined dynamically based on the Ansible inventory file specifying two Mariadb servers in different data centers. The inventory file can be statically specified with manual deployment of hosts on IBM Cloud, or used with Ansible dynamic inventory with Terraform automated deployment of servers and LBs.  


<p style="text-align: center;">
  <img src="images/WordpressCLB.png" alt="CLB single site" width="500"/>
</p>

<p style="text-align: center;">
  <img src="images/WordpressGLB.png" alt="GLB dual site" width="600"/>
</p>



This package uses three excellent Ansible roles maintained by Bert Van Vreckem and team. 

https://github.com/bertvv/ansible-role-mariadb
https://github.com/bertvv/ansible-role-wordpress
https://github.com/bertvv/ansible-role-httpd

DB replication setup is based on the work of Vitalii Michailovich
https://github.com/VitaliiMichailovich/Ansible-MySQL-Master-Master


All submodules/roles are force included in this package as a small number of changes where made to support usage in this configuration. Its not ideal, but simplifies installation for users who are new to IBM Cloud, Ansible and Terraform. 

## Local setup
This package was developed on OSX and as such requires sudo rights to execute some of the updates performed to the host file on the OSX control workstation and install modules for monitoring the state of the application. The OSX user password is saved as the encrypted variable 'su_password' in an Ansible Vault file in group_vars/control. The vault password is expected to be stored in the users home directory  ~/vault_pass.txt. ansible.cfg in the root of the package defines the location of the vault password. 

## Inventory
Ansible inventory is defined in /inventory/IBMCloud_inv.

When used with a manually deployed environment, host details take the form:
`app101  ansible_host=10.72.58.78 ansible_user=root`

Alternatively an Ansible dynamic inventory script can be used. 

## IBM Cloud deployment
The target deployment environment is specified in the IBM Cloud Solution Tutorial - [Web application serving from a secure private network](https://console.bluemix.net/docs/tutorials/web-app-private-network.html#web-application-serving-from-a-secure-private-network). The infrastructure deployment from this tutorial can be manually completed, with host names and IBM addresses populated into the Ansible inventory file. 

## Execution
 
```
ansible=playbook -i inventory site.yml
```

