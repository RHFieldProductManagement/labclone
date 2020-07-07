## Using this lab on Packet.Net

Deploying this lab is easy on packet.net using their **3.xlarge.x86** instance size and your Red Hat subs. 

This size is available in all of their core DC's and contains the following specs:
<center>
    <img src="images/packet-instance-size.png"/>
</center>

## Deployment of server

Follow this guide to deploy your server:

<center>
    <img src="images/packet-deploy-1.png"/>
</center>

* "Select a Datacenter" - choose something close, but it must have the **3.xlarge.x86** on offer)
* "Select Your Server" - as mentioned **3.xlarge.x86** is the prefered size. You'll need plenty of disk, RAM, and CPU and this sizing works nicely for the price.

---

<center>
    <img src="images/packet-deploy-2.png"/>
</center>

* "Select an Operating system" - Choose "Licensed" then "RHEL" and set the "OS Version" to RedHat Enterprise Linux 8"
* "Select Number and Name Your Server(s)" - Choose one server and name it something useful (or fun!)

---

<center>
    <img src="images/packet-user-data.png"/>
</center>

* "Optional Settings" - You need to toggle **"Add User Data"** to enable nested virtualisation on the host. Add the following User Data Script to the box:

~~~bash
#!/bin/bash
rmmod kvm_intel
cat << EOF > /etc/modprobe.d/kvm_intel.conf
 options kvm-intel nested=1
 options kvm-intel enable_shadow_vmcs=1
 options kvm-intel enable_apicv=1
 options kvm-intel ept=1
EOF
modprobe kvm_intel
~~~

* "Customize SSh Key Access" - You may choose to customise your ssh key depending on how you set up Packet. **The server will be deployed with root ssh access enabled for 24 hours and a password provided.**

---

<center>
    <img src="images/packet-deploy-3.png"/>
</center>

* Finally, review your settings and "**Deploy Now**".

---

<center>
    <img src="images/packet-deploy-4.png"/>
</center>

Full details of the server, including login credentials, are then provided

## Logging in and setting up the server

Packet servers are setup with RHUI, which won't have the packages you need. So register the server with your own (employee) sub:

~~~bash
$ ssh root@139.178.86.53
Last login: Mon Jul  6 21:31:09 2020 from 192.168.0.25

# subscription-manager register --username=xxxxx --password=yyyyy
# subscription-manager attach --auto
~~~

Install tmux

~~~bash
# dnf install tmux -y
~~~

Install git

~~~bash
# dnf install git -y
~~~

Check out this repo

~~~bash
# git clone https://github.com/RHFieldProductManagement/openshift-virt-labs.git
~~~

Prepare the intall script

~~~bash
# cd openshift-virt-labs
# vim install.sh
~~~

You need to set a few variables before deployment:

**SSH\_PUB\_BASTION** should be set to a local public key to allow passwordless logins. You can always just run `ssh-keygen` and set **SSH\_PUB\_BASTION=~/.ssh/id_rsa.pub**

**PULL_SECRET** needs to be set to your pull secret value as obtained from [https://cloud.redhat.com/openshift/install/metal/user-provisioned](https://cloud.redhat.com/openshift/install/metal/user-provisioned). Simply put the value between the single quote marks such that it looks like

~~~bash
PULL_SECRET='{"auths":{"cloud.openshift.com":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2 ... XbFdWdw==","email":"user@redhat.com"}}}'
~~~

# Remember to Delete your Packet instance when done!!!

<center>
    <img src="images/packet-delete.png"/>
</center>


Back to [Lab Guide](https://github.com/RHFieldProductManagement/openshift-virt-labs)



