# DocumentationForDevOps2 (MI2)
*Exercises:*

lo4-e4:
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



lo4-e5:
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











*Addition:*
*lo4:*

7. You need to deploy WordPress and MySQL using specific image versions and ensure that WordPress does not connect to the database using the root user:

   
| Task | Command | Description |
|------|---------|-------------|
| Deploy MySQL container | `podman run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=wpuserpw mysql:5.7` | Start a MySQL container named `mysql`. Set a root password, create a WordPress database, and establish a non-root user `wpuser` with a specified password. Uses MySQL version 5.7. |
| Deploy WordPress container | `podman run -d --name wordpress --link mysql -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_USER=wpuser -e WORDPRESS_DB_PASSWORD=wpuserpw -e WORDPRESS_DB_NAME=wordpress -p 8080:80 wordpress:php8.2` | Deploy a WordPress container named `wordpress`. It links to the MySQL container and configures to connect to the MySQL database using the non-root user credentials. Maps port 80 inside the container to port 8080 on the host. Uses the WordPress image with PHP 8.2. |
| Document commands | `echo "podman run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=wpuserpw mysql:5.7" > LO4_M`<br>`echo "podman run -d --name wordpress --link mysql -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_USER=wpuser -e WORDPRESS_DB_PASSWORD=wpuserpw -e WORDPRESS_DB_NAME=wordpress -p 8080:80 wordpress:php8.2" >> LO4_M` | Record the commands used in previous steps into a file named `LO4_M`. The `>` operator is used to create or overwrite the file with the first command, while `>>` is used to append the second command to the same file. |


8. Suitable methodologies for running a simple Python application requiring a database for persistent data storage across two different scenarios: development/testing on   a developer laptop and production deployment with scalability and automated image updates:


 | Scenario | Methodology | Command | Justification |
|----------|-------------|---------|---------------|
| Development and Testing on a Developer Laptop | Use Docker Compose with a Dockerfile for the Python app and `docker-compose.yml` for orchestrating the app and a PostgreSQL database. | `docker-compose up` | Docker Compose manages both the application and its database dependencies in isolated containers, simplifying development and testing. This setup can easily be mirrored on any developer's laptop, ensuring consistency across environments. PostgreSQL is chosen for its closer alignment with production-like settings compared to SQLite. |
| Production Deployment with At Least 10 Instances and Persistent Storage | Deploy on Kubernetes using deployments for the app and StatefulSets for PostgreSQL. Use Helm for package management and Jenkins for CI/CD to handle deployments and updates. | `helm install my-python-app ./my-helm-chart` <br> `kubectl scale deployment/my-python-app --replicas=10` | Kubernetes provides robust tools for managing scalable applications and persistent storage. Helm simplifies the deployment of complex applications. Scaling up to 10 instances is managed via Kubernetes' declarative syntax, and Jenkins automates the build and deployment processes for new image versions, ensuring continuous integration and deployment. |

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
   
*lo5:*

9. Troubleshooting Podman Command for MySQL (The command provided doesn't specify a database root password, which is required by the MySQL image to start correctly. The -d flag at the end does not specify a detach mode properly and lacks an image at the end.):
    
| Task | Issue Identified | Corrected Command | Explanation |
|------|------------------|-------------------|-------------|
| 9    | Missing root password specification and incorrect syntax for detach mode. | `podman run -d --name mysql -e MYSQL_USER=mike -e MYSQL_PASSWORD=mike -e MYSQL_ROOT_PASSWORD=notSoSecret mysql:5.7` | Added `MYSQL_ROOT_PASSWORD=notSoSecret` to satisfy MySQL's requirement for a root password. `-d` for detached mode is correctly used before specifying the image name. |


10. Feature of Container Images for Deployment:
    
| Task | Feature | Description |
|------|---------|-------------|
| 10   | Consistency and Isolation | Container images ensure that the software runs the same way in any environment. This isolation between the application and its surroundings means developers don't have to worry about changes in the environment affecting the application's performance or functionalities. |


11. Troubleshooting Dockerfile (The Dockerfile command RUN apt update && echo it works" fails due to a syntax error with unmatched quotes and no instruction to install any packages):
    
| Task | Issue Identified | Corrected Dockerfile Line | Explanation |
|------|------------------|--------------------------|-------------|
| 11   | Syntax error and ineffective update command. | `RUN apt-get update && apt-get install -y some-package` | Corrected syntax by removing the unmatched quote and added an actual install command (`apt-get install -y some-package`) to make the update meaningful. The placeholder `some-package` should be replaced with actual package names as needed. |

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 *lo6:*
 
 12. Deploy the httpd Application on OpenShift (Deploying the httpd application on OpenShift, ensuring high availability and load balancing by utilizing multiple pods, and exposing the application through a route.):

 | Task | Command | Description |
|------|---------|-------------|
| Deploy httpd application | `oc new-app --name=httpd --docker-image=httpd:2.4 --labels=app=httpd` | This command creates a new application in OpenShift using the httpd Docker image version 2.4. The application is named 'httpd' and appropriately labeled. |
| Scale to 2 pods | `oc scale --replicas=2 deployment/httpd` | Scale the deployment to have 2 pods to ensure high availability and load balancing. |
| Expose the service | `oc expose svc/httpd` | Expose the httpd service to external traffic by creating a route in OpenShift. This command automatically uses the service 'httpd' and exposes it externally. |
| Use a custom template (optional) | `oc new-app -f custom-httpd-template.yaml` | If using a custom template named 'custom-httpd-template.yaml', deploy it with this command. Ensure the template includes specifications for no resource limits, as required. |


13. Why Use Docker Swarm Over OpenShift for a Startup (The rationale behind choosing Docker Swarm over OpenShift for a simple startup that lacks a dedicated DevOps employee.):

| Task | Reason | Description | Command |
|------|--------|-------------|---------|
| Choose Docker Swarm | Simplicity and Lower Operational Overhead | Docker Swarm is inherently simpler to set up and manage compared to OpenShift. For a startup without a dedicated DevOps team, Docker Swarm provides a more straightforward approach to container orchestration. It integrates directly with Docker, which many developers are familiar with, reducing the learning curve and operational complexity. This makes it more suitable for small teams or startups looking to quickly deploy and manage containerized applications without the need for advanced configurations or the overhead that comes with more comprehensive solutions like OpenShift. | `docker swarm init` |


