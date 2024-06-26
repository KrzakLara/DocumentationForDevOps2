# DocumentationForDevOps2 (MI2)

redhat: https://rha.ole.redhat.com/ 
ili
https://vcsa7.vua.cloud


Exam details
#Site
http://vcsa7.vua.cloud/
username: infoeduka_name
password: Pa$$w0rd
#VM
username: student
password student
root user: root
password: centos
#Mysql root user
username: root
password: secret
#Which ports are exposed to host machine?
Nginx is running on port 80 & 81
Mysql is running on port 3306
#Which versions of services does docker compose use?
Mysql version is 5.7.42
Nginx version is 1.25
PHP version is 7.4
#Can I add my own initialization script for mysql?
Yes, you can change existing one - ./algebra.sql:/docker-entrypoint-initdb.d/1.sql or add another one - ./new.sql:/docker-entrypoint-initdb.d/2.sql

Notice the number 1, this means this script or dump will execute first
All dumps and scripts in docker-entrypoint-initdb.d will run only once, on container creation





*Exercises:*

e4-lo3:
| Step | Command | Description |
|------|---------|-------------|
| 1    | `ssh username@vm-address` | Use SSH to log into the workstation VM provided by the RH Academy. Replace `username` and `vm-address` with actual values. |
| 2    | `podman network ls` | List all existing Podman networks to ensure only the default network is present. |
| 3    | `podman network inspect podman` | Inspect the default network configuration and check the `dns_enabled` setting. |
| 4    | `podman network create --subnet 10.88.2.0/24 --gateway 10.88.2.1 --dns-enable=true labnet` | Create a custom network named `labnet` with DNS enabled. Adjust subnet and gateway if necessary. |
| 5    | `podman run -d --network labnet --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes -e MYSQL_DATABASE=wordpress -e MYSQL_USER=student -e MYSQL_PASSWORD=DB15secure! mysql` | Create and start a MySQL container in the background on the `labnet` network, with a randomized root password, a database named `wordpress`, and a user named `student` with a specified password. |
| 6    | `podman run -d --network labnet --name wordpress -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_USER=student -e WORDPRESS_DB_PASSWORD=DB15secure! -e WORDPRESS_DB_NAME=wordpress -p 8080:80 wordpress` | Start a WordPress container in the background on the `labnet` network, connected to the MySQL database, and publish port 80 of the container to port 8080 on the host. |
| 7    | N/A | Yes, using the default network would work similarly, though specific network configurations like DNS might differ. |
| 8    | Navigate to `http://localhost:8080` in a web browser | After starting the WordPress container, use a web browser to navigate to the host's IP on port 8080 to configure and create a default web page. |
| 9    | `podman rm -f mysql; podman run -d --network labnet --name mysql -v /home/student/mysql:/var/lib/mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes -e MYSQL_DATABASE=wordpress -e MYSQL_USER=student -e MYSQL_PASSWORD=DB15secure! mysql` | Delete the old MySQL container and recreate it, mounting the host directory `/home/student/mysql` to the container's `/var/lib/mysql`. |
| 10   | `podman volume create mysql_data; podman run -d --network labnet --name mysql -v mysql_data:/var/lib/mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes -e MYSQL_DATABASE=wordpress -e MYSQL_USER=student -e MYSQL_PASSWORD=DB15secure! mysql` | After removing the previous MySQL container, create a new one using a volume `mysql_data` for data persistence instead of a bind mount. |
| 11   | `sudo yum install -y podman-compose` | Install the `podman-compose` package using Yum package manager. |
| 12   | N/A | Review sample Podman Compose files at the provided GitHub repositories to understand how to structure your own Compose files. |
| 13   | Create a file `docker-compose.yml` | Create a Docker Compose file to define services for WordPress and MySQL using the configuration details from steps 5 and 6. |
| 14   | `podman-compose up -d` | Deploy the WordPress and MySQL services using the created Docker Compose file in detached mode. |



6. 

Write a Containerfile/Dockerfile which takes the official Ubuntu base image, installs apache2, and sets the default command to start apache2. It should set the environment variable STUDENT to your username and surname. Port 80 should be exposed. Bear in mind that Ubuntu uses apt and apt-get to manage packages. Save it as a file called LO3_D.



| Task                      | Command                                             | Description                                                                                       |
|---------------------------|-----------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Base Image                | `FROM ubuntu:latest`                                | Specifies the base image to use, which is the latest version of Ubuntu.                           |
| Set Environment Variable  | `ENV STUDENT="your_username your_surname"`          | Sets the environment variable `STUDENT` to your username and surname.                             |
| Install Apache2           | `RUN apt-get update && apt-get install -y apache2`  | Updates the package lists and installs `apache2` using `apt-get`.                                 |
| Expose Port 80            | `EXPOSE 80`                                         | Indicates that the container will listen on port 80 at runtime.                                   |
| Default Command           | `CMD ["apache2ctl", "-D", "FOREGROUND"]`            | Runs `apache2` in the foreground, which is required for Docker to keep the container running.     |

____________________________________________________________________________________________________________________________________________________________________________


e5:
| Task | Command | Description |
|------|---------|-------------|
| Identify main services | `kubectl get services --all-namespaces` | List all services in all namespaces to identify main Kubernetes services. For OpenShift specific services, use the OpenShift CLI (`oc`) equivalent. |
| Monitor services from console | N/A | Use the Kubernetes Dashboard or OpenShift web console to monitor services. Access these through their respective URLs provided by your cluster setup. |
| Access cluster from CLI | `oc login --token=your-token --server=your-server` | Log into an OpenShift cluster using the command line. Replace `your-token` and `your-server` with actual values provided by your cluster administrator. |
| Query Kubernetes API | `kubectl get --raw "/apis"` | Query the Kubernetes API to assess the health of the cluster. This command lists all available API resources. |
| Run containers as pods | `kubectl run nginx --image=nginx` | Run a containerized application (nginx in this case) as an unmanaged pod in Kubernetes. |
| Troubleshoot containerized applications | `kubectl logs [POD_NAME]` | Retrieve logs from a specified pod to troubleshoot issues. Replace `[POD_NAME]` with the name of your pod. |
| Deploy applications on Kubernetes | `kubectl create deployment my-app --image=my-image` | Deploy an application on Kubernetes. Replace `my-app` and `my-image` with your application and container image names. |
| Expose applications to network | `kubectl expose deployment my-app --port=8080 --type=LoadBalancer` | Expose your application to network access using a LoadBalancer service. Adjust the port and type as needed. |
| Manage storage for applications | `kubectl apply -f my-configmap.yaml` | Apply a ConfigMap or Secret from a file to externalize application configurations. For persistent storage, use: `kubectl apply -f my-persistentvolumeclaim.yaml` |
| Configure applications for reliability | `kubectl scale deployment my-app --replicas=3` | Scale an application to increase availability and resilience. Adjust the number of replicas based on your requirements. |
| Manage application updates | `kubectl set image deployment/my-app my-app=my-image:v2` | Update the application to a new version of the container image and manage rollbacks using deployment strategies. |

____________________________________________________________________________________________________________________________________________________________________________

e6:
| Task | Command | Description |
|------|---------|-------------|
| Identify main services | `kubectl get services --all-namespaces` | List all services in all namespaces to identify main Kubernetes services. For OpenShift specific services, use the OpenShift CLI (`oc`) equivalent. |
| Monitor services from console | N/A | Use the Kubernetes Dashboard or OpenShift web console to monitor services. Access these through their respective URLs provided by your cluster setup. |
| Access cluster from CLI | `oc login --token=your-token --server=your-server` | Log into an OpenShift cluster using the command line. Replace `your-token` and `your-server` with actual values provided by your cluster administrator. |
| Query Kubernetes API | `kubectl get --raw "/apis"` | Query the Kubernetes API to assess the health of the cluster. This command lists all available API resources. |
| Run containers as pods | `kubectl run nginx --image=nginx` | Run a containerized application (nginx in this case) as an unmanaged pod in Kubernetes. |
| Troubleshoot containerized applications | `kubectl logs [POD_NAME]` | Retrieve logs from a specified pod to troubleshoot issues. Replace `[POD_NAME]` with the name of your pod. |
| Deploy applications on Kubernetes | `kubectl create deployment my-app --image=my-image` | Deploy an application on Kubernetes. Replace `my-app` and `my-image` with your application and container image names. |
| Expose applications to network | `kubectl expose deployment my-app --port=8080 --type=LoadBalancer` | Expose your application to network access using a LoadBalancer service. Adjust the port and type as needed. |
| Manage storage for applications | `kubectl apply -f my-configmap.yaml` | Apply a ConfigMap or Secret from a file to externalize application configurations. For persistent storage, use: `kubectl apply -f my-persistentvolumeclaim.yaml` |
| Configure applications for reliability | `kubectl scale deployment my-app --replicas=3` | Scale an application to increase availability and resilience. Adjust the number of replicas based on your requirements. |
| Manage application updates | `kubectl set image deployment/my-app my-app=my-image:v2` | Update the application to a new version of the container image and manage rollbacks using deployment strategies. |
____________________________________________________________________________________________________________________________________________________________________________


e7:
| Task | Command | Description |
|------|---------|-------------|
| Identify main services | `kubectl get services --all-namespaces` | List all services in all namespaces to identify main Kubernetes services. For OpenShift specific services, use the OpenShift CLI (`oc`) equivalent. |
| Monitor services from console | N/A | Use the Kubernetes Dashboard or OpenShift web console to monitor services. Access these through their respective URLs provided by your cluster setup. |
| Access cluster from CLI | `oc login --token=your-token --server=your-server` | Log into an OpenShift cluster using the command line. Replace `your-token` and `your-server` with actual values provided by your cluster administrator. |
| Query Kubernetes API | `kubectl get --raw "/apis"` | Query the Kubernetes API to assess the health of the cluster. This command lists all available API resources. |
| Run containers as pods | `kubectl run nginx --image=nginx` | Run a containerized application (nginx in this case) as an unmanaged pod in Kubernetes. |
| Troubleshoot containerized applications | `kubectl logs [POD_NAME]` | Retrieve logs from a specified pod to troubleshoot issues. Replace `[POD_NAME]` with the name of your pod. |
| Deploy applications on Kubernetes | `kubectl create deployment my-app --image=my-image` | Deploy an application on Kubernetes. Replace `my-app` and `my-image` with your application and container image names. |
| Expose applications to network | `kubectl expose deployment my-app --port=8080 --type=LoadBalancer` | Expose your application to network access using a LoadBalancer service. Adjust the port and type as needed. |
| Manage storage for applications | `kubectl apply -f my-configmap.yaml` | Apply a ConfigMap or Secret from a file to externalize application configurations. For persistent storage, use: `kubectl apply -f my-persistentvolumeclaim.yaml` |
| Configure applications for reliability | `kubectl scale deployment my-app --replicas=3` | Scale an application to increase availability and resilience. Adjust the number of replicas based on your requirements. |
| Manage application updates | `kubectl set image deployment/my-app my-app=my-image:v2` | Update the application to a new version of the container image and manage rollbacks using deployment strategies. |
____________________________________________________________________________________________________________________________________________________________________________

e8:
| Task | Command | Description |
|------|---------|-------------|
| Identify main services | `kubectl get services --all-namespaces` | List all services in all namespaces to identify main Kubernetes services. For OpenShift specific services, use the OpenShift CLI (`oc`) equivalent. |
| Monitor services from console | N/A | Use the Kubernetes Dashboard or OpenShift web console to monitor services. Access these through their respective URLs provided by your cluster setup. |
| Access cluster from CLI | `oc login --token=your-token --server=your-server` | Log into an OpenShift cluster using the command line. Replace `your-token` and `your-server` with actual values provided by your cluster administrator. |
| Query Kubernetes API | `kubectl get --raw "/apis"` | Query the Kubernetes API to assess the health of the cluster. This command lists all available API resources. |
| Run containers as pods | `kubectl run nginx --image=nginx` | Run a containerized application (nginx in this case) as an unmanaged pod in Kubernetes. |
| Troubleshoot containerized applications | `kubectl logs [POD_NAME]` | Retrieve logs from a specified pod to troubleshoot issues. Replace `[POD_NAME]` with the name of your pod. |
| Deploy applications on Kubernetes | `kubectl create deployment my-app --image=my-image` | Deploy an application on Kubernetes. Replace `my-app` and `my-image` with your application and container image names. |
| Expose applications to network | `kubectl expose deployment my-app --port=8080 --type=LoadBalancer` | Expose your application to network access using a LoadBalancer service. Adjust the port and type as needed. |
| Manage storage for applications | `kubectl apply -f my-configmap.yaml` | Apply a ConfigMap or Secret from a file to externalize application configurations. For persistent storage, use: `kubectl apply -f my-persistentvolumeclaim.yaml` |
| Configure applications for reliability | `kubectl scale deployment my-app --replicas=3` | Scale an application to increase availability and resilience. Adjust the number of replicas based on your requirements. |
| Manage application updates | `kubectl set image deployment/my-app my-app=my-image:v2` | Update the application to a new version of the container image and manage rollbacks using deployment strategies. |

____________________________________________________________________________________________________________________________________________________________________________

e9:
| Task | Command | Description |
|------|---------|-------------|
| Manage storage for application configuration | `kubectl apply -f my-configmap.yaml` | Apply a ConfigMap or Secret from a file to externalize application configurations. |
| Provision persistent storage | `kubectl apply -f my-persistentvolumeclaim.yaml` | Provision storage volumes for persistent data files using a PersistentVolumeClaim. |
| Access persistent storage | `kubectl get pvc` | Check the status of persistent volume claims in your cluster. |
| Mount storage in pods | `kubectl apply -f my-pod.yaml` | Mount the provisioned storage in your pods by applying a Pod configuration file. |
| Backup data | `kubectl cp [POD_NAME]:/path/to/data /local/path` | Backup data from a pod to your local machine. Replace `[POD_NAME]`, `/path/to/data`, and `/local/path` with appropriate values. |
| Restore data | `kubectl cp /local/path [POD_NAME]:/path/to/data` | Restore data to a pod from your local machine. Replace `/local/path`, `[POD_NAME]`, and `/path/to/data` with appropriate values. |
| Troubleshoot storage issues | `kubectl describe pvc [PVC_NAME]` | Describe a persistent volume claim to troubleshoot issues. Replace `[PVC_NAME]` with the name of your PVC. |

____________________________________________________________________________________________________________________________________________________________________________

e10:
| Task | Command | Description |
|------|---------|-------------|
| Configure application reliability | `kubectl scale deployment my-app --replicas=3` | Scale an application to increase availability and resilience. Adjust the number of replicas based on your requirements. |
| Update application with a new version | `kubectl set image deployment/my-app my-app=my-image:v2` | Update the application to a new version of the container image and manage rollbacks using deployment strategies. |
| Monitor application health | `kubectl get pods --all-namespaces` | Monitor the health and status of pods across all namespaces. |
| Implement readiness probe | `kubectl apply -f readiness-probe.yaml` | Apply a readiness probe configuration to ensure the application is ready to receive traffic. |
| Implement liveness probe | `kubectl apply -f liveness-probe.yaml` | Apply a liveness probe configuration to automatically restart unhealthy containers. |
| Configure horizontal pod autoscaler | `kubectl autoscale deployment my-app --cpu-percent=50 --min=1 --max=10` | Automatically scale the number of pod replicas based on CPU utilization. |
| Roll back application update | `kubectl rollout undo deployment/my-app` | Roll back the application to a previous version in case of issues with the latest update. |
| Check rollout status | `kubectl rollout status deployment/my-app` | Check the status of a deployment rollout to ensure it has completed successfully. |
| Pause and resume a deployment | `kubectl rollout pause deployment/my-app` | Pause a deployment to stop further changes while troubleshooting. |
| Resume a deployment | `kubectl rollout resume deployment/my-app` | Resume a paused deployment to continue the rollout process. |


e11:
| Task | Command | Description |
|------|---------|-------------|
| Manage application updates | `kubectl set image deployment/my-app my-app=my-image:v2` | Update the application to a new version of the container image and manage rollbacks using deployment strategies. |
| Roll back application update | `kubectl rollout undo deployment/my-app` | Roll back the application to a previous version in case of issues with the latest update. |
| Check rollout status | `kubectl rollout status deployment/my-app` | Check the status of a deployment rollout to ensure it has completed successfully. |
| Pause a deployment | `kubectl rollout pause deployment/my-app` | Pause a deployment to stop further changes while troubleshooting. |
| Resume a deployment | `kubectl rollout resume deployment/my-app` | Resume a paused deployment to continue the rollout process. |
| Monitor application updates | `kubectl get deployments` | Monitor the status and progress of application updates. |
| Recreate deployment strategy | `kubectl apply -f my-deployment.yaml` | Apply a deployment configuration that uses the Recreate strategy to update the application by shutting down existing pods before creating new ones. |
| Rolling update deployment strategy | `kubectl apply -f my-rollingupdate-deployment.yaml` | Apply a deployment configuration that uses the RollingUpdate strategy to update the application by gradually replacing old pods with new ones. |
| Canary deployment strategy | `kubectl apply -f my-canary-deployment.yaml` | Apply a deployment configuration that uses the Canary strategy to test a new version of the application with a subset of users before full deployment. |
| Blue-Green deployment strategy | `kubectl apply -f my-bluegreen-deployment.yaml` | Apply a deployment configuration that uses the Blue-Green strategy to run two separate environments (blue and green) and switch traffic between them. |
____________________________________________________________________________________________________________________________________________________________________________

E12:
| Task | Command | Description |
|------|---------|-------------|
| Deploy web applications | `kubectl create deployment web-app --image=my-web-app` | Deploy a web application using a specified container image. Replace `my-web-app` with your application image name. |
| Expose web applications | `kubectl expose deployment web-app --port=80 --type=LoadBalancer` | Expose your web application to network access using a LoadBalancer service. Adjust the port as needed. |
| Scale applications | `kubectl scale deployment web-app --replicas=3` | Scale the web application to ensure availability and load balancing. Adjust the number of replicas based on your requirements. |
| Troubleshoot applications | `kubectl logs [POD_NAME]` | Retrieve logs from a specified pod to troubleshoot issues. Replace `[POD_NAME]` with the name of your pod. |
| Monitor application status | `kubectl get pods --all-namespaces` | Monitor the health and status of pods across all namespaces. |
| Update web applications | `kubectl set image deployment/web-app web-app=my-web-app:v2` | Update the web application to a new version of the container image. Replace `my-web-app:v2` with the new image version. |
| Roll back application updates | `kubectl rollout undo deployment/web-app` | Roll back the web application to a previous version in case of issues with the latest update. |
| Implement readiness probe | `kubectl apply -f readiness-probe.yaml` | Apply a readiness probe configuration to ensure the application is ready to receive traffic. |
| Implement liveness probe | `kubectl apply -f liveness-probe.yaml` | Apply a liveness probe configuration to automatically restart unhealthy containers. |
| Manage storage for applications | `kubectl apply -f my-persistentvolumeclaim.yaml` | Provision storage volumes for persistent data files using a PersistentVolumeClaim. |
| Mount storage in pods | `kubectl apply -f my-pod.yaml` | Mount the provisioned storage in your pods by applying a Pod configuration file. |
| Backup data | `kubectl cp [POD_NAME]:/path/to/data /local/path` | Backup data from a pod to your local machine. Replace `[POD_NAME]`, `/path/to/data`, and `/local/path` with appropriate values. |
| Restore data | `kubectl cp /local/path [POD_NAME]:/path/to/data` | Restore data to a pod from your local machine. Replace `/local/path`, `[POD_NAME]`, and `/path/to/data` with appropriate values. |
| Troubleshoot storage issues | `kubectl describe pvc [PVC_NAME]` | Describe a persistent volume claim to troubleshoot issues. Replace `[PVC_NAME]` with the name of your PVC. |



____________________________________________________________________________________________________________________________________________________________________________


