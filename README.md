# Workflow/Kubernetes

To deploy an application into kubernetes cluster, follow the steps below:

Create a Cluster: First, you need to create a cluster using the command: minikube start.

Deploy Your Application Image: After creating a cluster, you can deploy your application image into it using a deployment with the command: kubectl create deployment <deployment name> --image=<image name>.

By default, Kubernetes runs the application within a private network. Objects inside the cluster can access each other, but they're not available outside the cluster unless exposed using a service.

Expose the Deployment: To make your application accessible from your host machine, you need to expose the deployment using a service. You can do this with the command: kubectl expose deployment <deployment name> --type=<TYPE> --port=<The application port>.
There are several ways to expose Objects in Kubernetes. For instance, NodePort assigns a high-numbered random port on all the services in your cluster to the object you're trying to expose.

Once an object is exposed outside of a cluster, a service is created. You can retrieve information about the service using the command: kubectl get service <service name>. The 'CLUSTER-IP' displayed is the internal IP address of the service inside the cluster. However, outside the cluster, the service is accessible using the 'outside port'. The format is 'PORT(S): <inside port>:<outside port>/TCP'.

Access the Service Outside the Cluster: To get the IP address of the service outside of the cluster, you can use Minikube's IP address with the command: minikube ip <ip address>. Your service will then be accessible on your machine as <ip address>:<outside port>.
Alternatively, you can let Minikube handle the process of finding the IP address and port, and launching the service in your web browser by running the command: `minikube service <service name>`.

Stop or Delete the Cluster: If you're finished with the cluster, you can either stop it using minikube stop or delete the cluster using minikube delete.

Containers and virtual machines
The major distinction between containers and virtual machines lies in their structure and functionality. A container encapsulates the application along with its dependencies, which may encompass certain features of an operating system (OS). On the other hand, a virtual machine represents a full-fledged OS capable of running various applications.

`ls -lah` means: "List all files (including hidden ones) in the current directory, in long format, and display the sizes in a human-readable format.

Multi-stage builds
A multi-stage build in Docker is a process where you use multiple FROM statements in your Dockerfile. Each FROM statement begins a new stage of the build.

The reason you might want to do this is to keep your final Docker image small and secure. Here's how:

First Stage (Build stage): In the first stage, you typically use a larger Docker image that contains all the tools and dependencies needed to build your application. Here, you compile your code and create an executable file. This stage results in a Docker image that has your compiled application along with all the build tools and dependencies - but this image might be quite large.

Second Stage (Production stage): In the second stage, you start with a new, often smaller, Docker image that contains only the minimal elements your application needs to run (like a slim version of an operating system). You then copy only the compiled application from the first stage into this new image. This leaves you with a smaller, more streamlined Docker image that is quicker to start, uses less memory, and has a smaller attack surface, which improves security.

So, in simple terms, a multi-stage Docker build is a way to build your application in one Docker image (stage), then copy the built application into a different, smaller Docker image. This results in a smaller final image that contains only what's needed to run your application.

If for some reason, you want to build your application image inside minikube, you can switch the docker context, and then build the image.

Docker context
The command `docker images` displays the Docker images that are currently stored on your local machine. However, when you execute `eval $(minikube docker-env)`, the Docker context shifts to utilize the Docker environment within Minikube. As a result, any subsequent `docker images` commands will reveal the Docker images stored within the Minikube environment, instead of those on your local machine.
If you have bash or zsh installed, you can switch to it by simply typing `bash` or `zsh` in your terminal.

`eval $(minikube docker-env -u)` command unset the environment variable that was set to switch the docker context, and subsequent `docker images` will reveal the Docker images stored within the local machine instead of minikube.

Docker image from a private image repository: here
Retrieve Docker Registry Credentials: Obtain the necessary credentials for your Docker registry. Typically, this involves a username and password or an access token. Make sure you have these credentials handy.

Create a Secret: Use the `kubectl create secret` command to create the Docker registry secret.
```
kubectl create secret docker-registry <secret-name> \
  --docker-server=<registry-server> \
  --docker-username=<username> \
  --docker-password=<access-token> \
  --docker-email=<email>
```
To allow Minikube to pull an image from a private registry repository, you need to use the secret in your deployments or pods.

Create the deployment
`kubectl create deployment <deployment name> –image=<image-name>`

Edit the deployment to add the secret
`kubectl edit deployment <deployment name>`
Then go to spec.template.spec and add imagePullSecrets, which is a list and add one list item, with the name of the secret you created earlier.

`kubectl expose deployment <deployment name> --type=<TYPE> --port=<The application port> –target-port=<application port>`

Scaling the application: Manually
We can edit the deployment file and increase the number of replicas to ensure higher availability of the application.

Monitoring the logs:
When we increase the number of replicas in our deployment to manually scale the application, it's important to ensure that requests are properly distributed to all the pods. One way to monitor this distribution is by observing the logs of the pods.

To accomplish this, you can use the `kubectl logs -f -l app=hello-go –prefix=true` command. Let's break down this command:

kubectl logs: This command is used to retrieve the logs of a specific pod or pods.
-f: This option allows you to continuously stream the logs, displaying new log entries as they are generated.
-l app=hello-go: This flag filters the logs based on the label selector. In this case, it selects pods with the label app=hello-go.
--prefix=true: Shows the different pods handling each request as the logs are generated.
By running this command, you will be able to observe the logs of all pods labeled with app=hello-go. 

Learn about service routing in kubernetes

Updating the image of a deployment in your cluster
If you need to update the image for a specific deployment because changes have been made and you want to ensure the latest version is running in your cluster, there are a couple of ways to accomplish this.

One approach is to manually edit the deployment. You can modify the deployment's YAML configuration file directly and specify the new image version. However, this method requires manual editing and is prone to human error.

Another approach is to use the kubectl set image command, which provides a convenient way to update the image version for a deployment. The command syntax is as follows:
`kubectl set image deployment/hello-go <container name>=<new image version>`

Here's what each component of the command means:
deployment/<deployment name>: This specifies the deployment you want to update. 
<container name>: This refers to the name of the container within the deployment that you want to update with a new image version.
<new image version>: This is the new version of the image that you want to use for the specified container.

By executing this command, Kubernetes will automatically update the deployment with the new image version for the specified container, rolling out the changes to the pods.

Using the kubectl set image command simplifies the process of updating the image for a deployment, making it more efficient and less error-prone compared to manual editing of YAML files.

You can watch the pods to ensure kubernetes is bringing up the new pods, and terminating the old ones.
`watch kubectl get pods -l app=hello-go`

Rollback Deployment in Kubernetes:

To revert or undo changes in Kubernetes, you can utilize the `kubectl rollout history deployment hello-go` command. This command allows you to view the different versions of a deployment that have been applied to the cluster over time. It provides a history of the deployment, enabling you to identify specific revisions and select a desired version for rollback.

By running kubectl rollout history deployment hello-go, you can retrieve information such as revision numbers, timestamps, and any relevant annotations associated with each deployment version. This visibility into the deployment history empowers you to make informed decisions when rolling back to a previous configuration.

Once you've identified the desired revision, you can proceed with the rollback process using the appropriate `kubectl rollout undo deployment hello-go` command which rolls back to the previous version.

Git
To sync your fork with the original repository (the origin), you need to add the original repo as an upstream source to your local clone of your fork. Then you'll fetch the commits from the upstream source and merge them into your local clone.
```
git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git

```
```
git fetch upstream
```

```
git checkout master
```
```
git merge upstream/master
```

```
git status
```

```
git push origin master
```

Format go files
```gofumpt -w ./cmd/*.go```

to select all in Vim, do ggVG.
To delete all in Vim, do ggdG
