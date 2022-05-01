This note will serve as notes for container management in RHEL-like systems. This notes will see how to check container tools, inspect images, download images and run them.
This note will will also see how to remove containers and images as well as how to generate root and rootless container controlled via systemd. This notes are based on Ashgar Ghori's updated book on preparing for RHEL RHCSA.

Before you begin, install necessary tools by executing this command ```sudo dnf module install container-tools```. Also make sure you have active subscription with Redhat. A developer account can be obtained for free which gives you perks such as running up to 16 RHEL machines for free. Also access to it's container registry.

#### Image Management
1. Login to ```registry.redhat.io```
```podman login registry.redhat.io```

2. Search for images
``` podman search registry.redhat.io/rhel8/mysql```
This command will search for MySQL images in ```registry.redhat.io```

3. Inspect online image
```skopeo inspect registry.redhat.io/rhel8/mysql-80```
This command will inspect the image MySQL 8 in online registry. It will provide versions and other relevant information pertaining to the image.

4. Pull image from public repository
```podman pull docker://registry.redhat.io/rhel8/mysql-80```
This will pull MySQL 8 image from ```registry.redhat.io```

5. List downloaded image in localhost
```podman images```

6. Inspect downloaded image
```podman inspect mysql-80```
This will inspect the image locally, providing information on the image.

7. Remove image from local registry
```podman rmi mysql-80```
This will remove the image from local registry. You can verify the removal by executing ```podman images```

#### Run, Interact & Renove a Named Container
1. ```podman -ti --name rhel8-base-os ubi8``` This command will do the following: download ```ubi8```, run interactive shell for the container. The ```-ti``` command achieves it whereas ```--name``` will add a name tag for the container. To exit the container, simply type ```exit```. You will fall back to localhost.
2. ```podman exec rhel8-base-os cat /etc/redhat-release``` This command will execute a command inside the container without attaching to it. The ```exec``` achieves it.
3. ```podman attach rhel8-base-os``` This command will attach to the container we have downloaded and named earlier.
4. ```podman rm rhel8-base-os``` This command will remove the container. You can verify by executing ```podman ps``` or ```podman ps -a```.
5. ```podman run --rm ubi8 ls``` This command will download the ```ubi8``` image, spawn a container, run the ```ls``` command and then removes the container.


#### Advanced Container Management
1. ```podman run -dp 8080:80 --name httpd httpd-24``` This command will run ```httpd-24``` on detached mode with container's internal port mapping to localhost's port.
2. ```podman port httpd``` This command will show which internal port mapped to localhost's port for ```httpd``` container.
3. ```podman run -it -e HISTSIZE -e SECRET="tryhackme" --name rhel8 ubi8``` This command will run ```ubi8``` image with environment variables set (```-e HISTSIZE```). Once you are inside the container, you can verify the environment variables set.
4. ```podman run --name rhel8 -v /host_data/:container_data:Z -it ubi8``` Before running this command, create and give appropriate permissions for ```/host_data/``` in localhost. The ```-v``` will attach a directory or mount point to the container. The ```:Z``` will ensure that proper SELinux context is applied to the mount point. Create a file with content you wish and place it in ```/container_data```. Exit the container and check ```/host_data/```. You will find the file you created inside the container in the aforementioned directory.
5. ```podman run --name rhel8 -v /host_data/:/container_data2/:Z -it ubi8``` This is to show the contents of ```/host_data/``` is persistent across containers.

##### Configure a Root Container as systemd Service
1. ```podman run -dt --name root-container ubi8``` This will run the container in detached mode.
2. ```podman generate systemd --new -- root-container > /etc/systemd/system/root-container.service``` This will generate systemd unit file and store it in ```/etc/systemd/system/```. Now go ahead and stop the container we created in step.1 and run ```systemctl daemon-reload```. Now start your container using ```systemctl enable --now root-container```. Check if the container is running by issuing ```systemctl status root-container```. You should see the container running, if not start the systemd service by issuing ```systemctl start root-container```.

##### Configure Rootless Container as systemd Service
**This section assumes that you have created rootless container using podman command, and rest of the section assumes you are running the command as normal user**
1. ```mkdir -p ~/.config/systemd/user``` This command is to create a dir to store the service unit file.
2. ```podman generate systemd --name rootless-container > ~/.config/systemd/user/rootless-container.service``` This command will generate user systemd files in mentioned directory. Now running ```systemctl --user daemon-reload``` will load all user related systemd files. Now remove the rootless-container and run ```systemd --user enable --now rootless-container``` will enable the rootless container. ```systemctl --user start rootless-container``` will start the container. The issue with rootless container is that as the user logs out, the container gets killed. In order to make it persistent as the system boots, run following command ```loginctl enable-linger```. This will ensure the container is started even if the user did not login or logs out.


Reference: RHCSA Red Hat Enterprise Linux 8 Training and Exam Preparation Guide Second Edition by Ashgar Ghori


### Sample Questions
Task 1: Launch a simple container as normal user using the latest version of ubi7 image. Configure the container to auto-start at system reboots without the need for normal user to login.
Task 2: Launch another container as normal user using the latest version of ubi8 image with to environment variables SHELL and HOSTNAME. Configure the container to auto-start via systemd without the need for normal user to log in. Connect to the container and verify variable settings. 
Task 3: Launch a root container and configure it to auto-start via systemd.
Task 4: Launch a container as normal user with /data01 mapped to /data01 using the latest version of the ubi8 image. Configure a systemd service to auto-start the container on system reboots without the need for normal user to log in. Create files under the shared mount point and validate data persistence.
Task 5: Launch a named container as normal user with host port 10000 mapped to container port 80. Employ the latest version of the ubi7 image. Configure a systemd service to auto-start the container without the need for normal user to login. Validate port mapping using an appropriate podman subcommand.
Task 6: Launch another named container as normal user with /host_data01 mapped to /container_data01, one variable ENVIRON=Exam , and host port 1050 mapped to container port 1050. Use the latest version of the ubi8 image. Configure a systemd service to auto-start the container without the need for normal user to login. Create a file under the shared directory and validate data persistence. Verify port mapping and variable settings using appropriate podman subcommands.
Task 7: Launch a named root container with host port 443 mapped to container port 443. Employ the latest version of the ubi7 image. Configure a systemd service to auto-start the container at system reboots. Validate port mapping using appropriate podman subcommand.
Task 8: Launch a named container as normal user with /data01 mapped to /data01 and two variables KERN=$(uname -r) and SHELL defined. Use the latest version of the ubi8 image. Configure a systemd service to auto-start the container at system reboots without the need for normal user to login. Create a file under the shared mount point and validate data persistence. Verify port mapping using an appropriate podman subcommand. 

