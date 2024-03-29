## Task
The Nautilus DevOps team is planning to set up a Jenkins CI server to create/manage some deployment pipelines for some of the projects. They want to set up the Jenkins server on Kubernetes cluster. Below you can find more details about the task:

1) Create a namespace jenkins

2) Create a Service for jenkins deployment. Service name should be jenkins-service under jenkins namespace, type should be NodePort, nodePort should be 30008

3) Create a Jenkins Deployment under jenkins namespace, It should be name as jenkins-deployment , labels app should be jenkins , container name should be jenkins-container , use jenkins/jenkins image , containerPort should be 8080 and replicas count should be 1.

Make sure to wait for the pods to be in running state and make sure you are able to access the Jenkins login screen in the browser before hitting the Check button.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.
## Solution
Create a namespace named `jenkins`.

```sh
thor@jump_host ~$ kubectl create namespace jenkins
namespace/jenkins created
```

Create a yaml file.

```sh
thor@jump_host ~$ vi jenkins.yaml
```

Define specification of the NodePort and the deployment.
```yml
apiVersion: v1
kind: Service
metadata:
  name: jenkins-service
spec:
  type: NodePort
  selector:
    app: jenkins
  ports:
    - port: 8080
      nodePort: 30008


---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
        - name: jenkins-container
          image: jenkins/jenkins:latest
          ports:
            - containerPort: 8080
```

Create the NodePort and the deployment.
```sh
thor@jump_host ~$ kubectl apply -f jenkins.yaml -n jenkins
service/jenkins-service created
deployment.apps/jenkins-deployment created
```

And after a while Jenkins should be running.

## References

[Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

[NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport)
