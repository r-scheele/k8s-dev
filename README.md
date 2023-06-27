# Workflow/Kubernetes

## Creating a Kubernetes Cluster

1. **Create a Cluster**: First, you need to create a cluster using the command: `minikube start`.
2. **Deploy Your Application Image**: After creating a cluster, you can deploy your application image into it using a deployment with the command: `kubectl create deployment <deployment name> --image=<image name>`.
3. **Expose the Deployment**: To make your application accessible from your host machine, you need to expose the deployment using a service. You can do this with the command: `kubectl expose deployment <deployment name> --type=<TYPE> --port=<The application port>`.
4. **Access the Service Outside the Cluster**: To get the IP address of the service outside of the cluster, you can use Minikube's IP address with the command: `minikube ip <ip address>`.
5. **Stop or Delete the Cluster**: If you're finished with the cluster, you can either stop it using `minikube stop` or delete the cluster using `minikube delete`.

## Understanding Containers and Virtual Machines

The major distinction between containers and virtual machines lies in their structure and functionality. A container encapsulates the application along with its dependencies, which may encompass certain features of an operating system (OS). On the other hand, a virtual machine represents a full-fledged OS capable of running various applications.

## Interpreting Command Line Instructions

`ls -lah` means: "List all files (including hidden ones) in the current directory, in long format, and display the sizes in a human-readable format."

## Understanding Multi-stage Builds

A multi-stage build in Docker is a process where you use multiple FROM statements in your Dockerfile. Each FROM statement begins a new stage of the build.

## Working with Docker Context

If for some reason, you want to build your application image inside Minikube, you can switch the Docker context, and then build the image. 

## Accessing Docker Images from a Private Image Repository

1. **Retrieve Docker Registry Credentials**: Obtain the necessary credentials for your Docker registry. 
2. **Create a Secret**: Use the `kubectl create secret` command to create the Docker registry secret. 
      ```
      kubectl create secret docker-registry <secret-name> \
        --docker-server=<registry-server> \
        --docker-username=<username> \
        --docker-password=<access-token> \
        --docker-email=<email>
      ```

## Creating and Modifying a Deployment

1. **Create the deployment**: `kubectl create deployment <deployment name> –image=<image-name>`
2. **Edit the deployment to add the secret**: `kubectl edit deployment <deployment name>`

## Scaling the Application Manually

We can edit the deployment file and increase the number of replicas to ensure higher availability of the application.

## Monitoring the Logs

When we increase the number of replicas in our deployment to manually scale the application, it's important to ensure that requests are properly distributed to all the pods.

## Understanding Service Routing in Kubernetes

## Updating the Image of a Deployment

If you need to update the image for a specific deployment because changes have been made and you want to ensure the latest version is running in your cluster, there are a couple of ways to accomplish this.

## Rolling Back Deployment in Kubernetes

To revert or undo changes in Kubernetes, you can utilize the `kubectl rollout history deployment hello-go` command.

## Working with Git

To sync your fork with the original repository (the origin), you need to add the original repo as an upstream source to your local clone of your fork.

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


## Formatting Go Files

Use `gofumpt -w ./cmd/*.go` to format Go files.

## Working with Vim

To select all in Vim, do `ggVG`.
To delete all in Vim, do `ggdG`.



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

