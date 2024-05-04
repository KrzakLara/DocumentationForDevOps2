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
