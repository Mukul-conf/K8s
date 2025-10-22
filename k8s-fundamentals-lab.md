# 🚀 Kubernetes Fundamentals Lab: Your First Container Adventure!

**Duration:** 30 minutes  
**Difficulty:** Beginner (Kid-friendly!)  
**What you'll learn:** How to deploy your first application on Kubernetes like a pro!

## 🎯 What is Kubernetes?

Imagine Kubernetes (we call it "K8s" for short) as a super smart robot manager that helps you run your applications. Just like how a restaurant manager makes sure all the waiters, chefs, and customers are happy, Kubernetes makes sure all your applications are running smoothly!

## 🛠️ What You'll Need

- A computer with internet
- Minikube installed (think of it as your personal Kubernetes playground)    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"

- kubectl installed (this is your magic wand to talk to Kubernetes)
- A text editor (like Notepad, but fancier)

## 📚 Quick Vocabulary Lesson

Before we start, let's learn some Kubernetes words:

- **Pod** = A cozy home for your application containers
- **Deployment** = Instructions on how many pods you want and what they should do
- **Service** = A phone book that helps other apps find your app
- **Node** = A computer that runs your pods (like a worker bee)
- **Cluster** = A group of nodes working together (like a bee hive)

## 🎪 Assignment 1: Deploy Your First Web Application (30 minutes)

### Step 1: Start Your Kubernetes Playground (5 minutes)

First, let's wake up Minikube:

```bash
# Start your personal Kubernetes cluster
minikube start

# Check if everything is working
kubectl get nodes
```

You should see something like:
```
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   30s   v1.28.0
```

🎉 **Congratulations!** You just started your own Kubernetes cluster!

### Step 2: Create Your First Pod (5 minutes)

Let's create a simple web application. Think of this like putting a toy in a box:

Create a file called `my-first-pod.yaml`:

```yaml
# This is like a recipe card for Kubernetes
apiVersion: v1
kind: Pod
metadata:
  name: my-web-app
  labels:
    app: web
spec:
  containers:
  - name: web-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

Now let's tell Kubernetes to create this pod:

```bash
# Create the pod (like following the recipe)
kubectl apply -f my-first-pod.yaml

# Check if your pod is running
kubectl get pods
```

You should see:
```
NAME         READY   STATUS    RESTARTS   AGE
my-web-app   1/1     Running   0          30s
```

🎉 **Amazing!** Your first pod is running!

### Step 3: Make Your App Accessible (10 minutes)

Right now, your app is like a toy inside a locked box. Let's create a "door" so others can reach it!

Create a file called `my-first-service.yaml`:

```yaml
# This creates a door to your application
apiVersion: v1
kind: Service
metadata:
  name: my-web-service
spec:
  selector:
    app: web
  ports:
  - port: 8080
    targetPort: 80
  type: NodePort
```

Apply the service:

```bash
# Create the service (open the door)
kubectl apply -f my-first-service.yaml

# See your service
kubectl get services
```

Now let's visit your website:

```bash
# Get the URL to your app
minikube service my-web-service --url
```

Copy the URL and open it in your browser! 🌐

🎉 **WOW!** You can see the nginx welcome page!

### Step 4: Scale Your Application (5 minutes)

What if lots of people want to visit your website? Let's create more copies!

First, let's delete our single pod and create a deployment instead:

```bash
# Remove the single pod
kubectl delete pod my-web-app
```

Create a file called `my-deployment.yaml`:

```yaml
# This tells Kubernetes to always keep 3 copies running
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-web-deployment
spec:
  replicas: 3  # We want 3 copies!
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web-container
        image: nginx:latest
        ports:
        - containerPort: 80
```

Apply it:

```bash
# Create the deployment
kubectl apply -f my-deployment.yaml

# Watch your pods appear!
kubectl get pods
```

You should see 3 pods now! 

```
NAME                                READY   STATUS    RESTARTS   AGE
my-web-deployment-abc123-def45      1/1     Running   0          10s
my-web-deployment-abc123-ghi78      1/1     Running   0          10s
my-web-deployment-abc123-jkl90      1/1     Running   0          10s
```

### Step 5: Explore and Learn (5 minutes)

Let's explore what we created:

```bash
# See all your resources
kubectl get all

# Get detailed info about your deployment
kubectl describe deployment my-web-deployment

# See the logs from one of your pods (replace with actual pod name)
kubectl logs my-web-deployment-abc123-def45

# Go inside a pod and look around (like entering a toy house)
kubectl exec -it my-web-deployment-abc123-def45 -- /bin/bash
# Type 'exit' to come back out
```

## 🎮 Fun Experiments to Try

### Experiment 1: Break Something and Watch It Heal!
```bash
# Delete one pod
kubectl delete pod my-web-deployment-abc123-def45

# Quickly run this to see Kubernetes create a new one
kubectl get pods
```

🪄 **Magic!** Kubernetes automatically created a new pod to replace the deleted one!

### Experiment 2: Scale Up and Down
```bash
# Scale to 5 pods
kubectl scale deployment my-web-deployment --replicas=5

# Watch them appear
kubectl get pods

# Scale back down to 2
kubectl scale deployment my-web-deployment --replicas=2

# Watch some disappear
kubectl get pods
```

## 🧹 Clean Up Time

Always clean up your toys when you're done:

```bash
# Delete everything we created
kubectl delete deployment my-web-deployment
kubectl delete service my-web-service

# Stop minikube
minikube stop
```

## 🏆 What You Just Learned

Congratulations! You just learned:

1. **Pods** - How to put applications in containers
2. **Services** - How to make apps accessible to others
3. **Deployments** - How to manage multiple copies of your app
4. **Scaling** - How to handle more traffic
5. **Self-healing** - How Kubernetes automatically fixes problems

## 🤔 Think About This

- What happened when you deleted a pod? Why did Kubernetes create a new one?
- Why do you think we need 3 copies of the same application?
- What would happen if one of the computers (nodes) in your cluster broke?

## 🎯 Next Steps

You're now ready for more advanced Kubernetes adventures! In the next assignment, we'll learn about Apache Kafka and how to run it on Kubernetes.

## 🆘 Help! Something Went Wrong?

Don't worry! Here are common fixes:

**Pod won't start?**
```bash
kubectl describe pod [pod-name]
kubectl logs [pod-name]
```

**Can't reach your service?**
```bash
# Make sure minikube tunnel is running
minikube tunnel
```

**Everything is broken?**
```bash
# Start fresh
minikube delete
minikube start
```

Remember: Making mistakes is how we learn! Every expert was once a beginner who kept trying. 🌟
