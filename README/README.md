## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![](Images/Azure_Virtual_Network.drawio)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  -[Filebeat-Playbook](https://github.com/itasyst/CyberSecurity/blob/main/Ansible/filebeat-playbook.yml)
  -[Install-Elk](https://github.com/itasyst/CyberSecurity/blob/main/Ansible/install-elk.yml)
  -[Metricbeat-Playbook](https://github.com/itasyst/CyberSecurity/blob/main/Ansible/metricbeat-playbook.yml)
  -[Pentest](https://github.com/itasyst/CyberSecurity/blob/main/Ansible/pentest.yml)  

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting traffic to the network.

- What aspect of security do load balancers protect? 
__Availability, Web Traffic, Web Security__
  
- What is the advantage of a jump box?
__Automation, Security, Network Segmentation, Access Control__


Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.

- What does Filebeat watch for?
__Filebeat helps generate and organize log files to send to Logstash and Elasticsearch. Specifically, it logs information about the file system, including which files have changed and when.__

- What does Metricbeat record?
__Metricbeat records system-level CPU usage, memory, file system, disk IO, and network IO statistics, as well as top-like statistics for every process running on your systems. You can ship the data to the output that you specify, such as Elasticsearch, Logstash, or Kibana.__

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name          | Function       | IP Address                        | Operating System |
|---------------|----------------|-----------------------------------|------------------|
| Jump Box      | Gateway        | 10.0.0.4/20.85.230.55             | Linux            |
| Web-1         | Web Server     | 10.0.0.5/40.117.60.30             | Linux            |
| Web-2         | Web Server     | 10.0.0.6/40.117.60.30             | Linux            |
| Elk           | Elk Server     | 10.1.0.4/40.113.222.208           | Linux            |
| Load Balancer | Load Balancer  | Static External IP (40.117.60.30) | Linux            |
| Workstation   | Access Control | External IP/Public IP             | Linux            |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Elk Server machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Workstation Public IP through TCP port 5601

Machines within the network can only be accessed by the Jump-Box-Provisioner. Which machine did you allow to access your ELK VM? What was its IP address?
- Jump-Box-Provisioner IP: 10.0.0.4 via SSH port 22
- Workstation Public IP via port TCP 5601

A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible | Allowed IP Addresses                 |
|---------------|---------------------|--------------------------------------|
| Jump Box      | No                  | Workstation Public IP on SSH 22      |
| Web-1         | No                  | 10.0.0.4 on SSH 22                   |
| Web-2         | No                  | 10.0.0.4 on SSH 22                   |
| Elk           | No                  | Workstation Public IP using TCP 5601 |
| Load Balancer | No                  | Workstation Public IP on  HTTP 80    |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _Ansible allows you to automate the complex tasks you would have otherwise had to configure manually, the automation allows admins and developers to focus their time and attention on other tasks 

The playbook implements the following tasks:
- Configures ELK VM and specifies the host and remote user to run the set of tasks against
    ```name: Configure Elk VM with Docker
    hosts: elk
    remote_user: sysadmin
    become: true
    tasks:
    ```
- Installs the following packages:
   `docker.io`: The Docker engine, user for running containers.
   `python3-pip`: Package used to install Python software
   `docker`: Python client for Docker. Required by Ansible to control the state of Docker containers.
   
   ```- name: Install docker.io
        apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present
        
     - name: Install python3-pip
       apt:
       force_apt_get: yes
       name: python3-pip
       state: present
        
     - name: Install Docker module
       pip:
       name: docker
       state: present
   ```
- Configures VM to use more memory:
  ```- name: Increase virtual memory
       command: sysctl -w vm.max_map_count=262144
       
       # Use sysctl module
       name: Use more memory
       sysctl:
       name: vm.max_map_count
       value: 262144
       state: present
       reload: yes
  ```
- Download and Launch a Docker Container
  ```- name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
  ```      
- Configures Container to launch using these ports:
  ```published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044
     ```

- Enable Docker on boot
```    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes
```
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web 1: 10.0.0.5
- Web 2: 10.0.0.6

We have installed the following Beats on these machines:
- Filebeat, Metricbeat
- ELK Server, Web 1, Web 2

These Beats allow us to collect the following information from each machine:
- Filebeat: used to collect log files from very specific files, such as logs generated by Apache, Microsoft Azure tools, the Nginx web server, or MySQL database
- Metricbeat: takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the `filebeat-config.yml` file to `/etc/filebeat/filebeat-playbook.yml`.
- Update the `filebeat-config.yml` file to include the following:
  -
  
- Run the playbook, and navigate to the Filebeat installation page on the ELK server GUI to check that the installation worked as expected.
  ..*Navigate back to the Filebeat installation page on the ELK server GUI.
  ..*On the same page, scroll to Step 5: Module Status and click Check Data.
  ..*Scroll to the bottom of the page and click Verify Incoming Data.

__FAQs__
- _Which file is the playbook? Where do you copy it?__The filebeat playbook is located in /etc/ansible/files/filebeat-config.yml__
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?__http://[your.ELK-VM.External.IP]:5601/app/kibana__

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

__Commands to Start Up VMs and Containers__

```ssh [username]@[IP Address]
Jump Box Internal IP: 10.0.0.4
Jump Box ssh sysadmin@20.85.230.55
Web - 1  ssh sysadmin@10.0.0.5
Web - 2 ssh sysadmin@10.0.0.6
Web - 3 ssh sysadmin@10.0.0.7
Elk ssh sysadmin@10.1.0.4
Load Balancer: 40.117.60.30

sudo docker container list -a 

sudo docker start zealous_diffie
sudo docker attach zealous_diffie

sudo docker start elk
sudo docker attach elk

sudo docker start dvwa
sudo docker attach dvwa
```
__Commands to Edit and Run Playbooks__

Create or Edit Filebeat Playbook: `nano /etc/ansible/roles/filebeat-playbook.yml`

Get Filebeat Config file: `curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/filebeat-config.yml`

Edit Filebeat Config file: `nano /etc/ansible/filebeat-config.yml`

Get Metricbeat Config file: `curl https://gist.githubusercontent.com/slape/58541585cc1886d2e26cd8be557ce04c/raw/0ce2c7e744c54513616966affb5e9d96f5e12f73/metricbeat > /etc/ansible/metricbeat-config.yml`

Edit Metricbeat Config file: `nano /etc/ansible/metricbeat-config.yml`

Create or Edit Metricbeat Playbook: `nano /etc/ansible/roles/metricbeat-playbook.yml`

Run Metricbeat Playbook: `ansible-playbook /etc/ansible/roles/metricbeat-playbook.yml`

