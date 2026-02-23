# Deployed a Reddit Clone on Kubernetes with Ingress Enabled

**In this project, I deployed a Reddit Clone (Node.js application) on Kubernetes using:**

* **🐳 Docker for containerization**  
* **☸️ Kubernetes for orchestration**  
* **🌐 Service & Ingress for networking**  
* **📦 DockerHub for image storage**

**The Docker image used in this deployment:**

saksham1111/reddit-clone

**Let’s walk through the complete step-by-step implementation.**

###  **Project Objective**

**The goal of this project is to:**

* **Containerize a Node.js Reddit clone application**  
* **Deploy it on a Kubernetes cluster**  
* **Expose it using a Service**  
* **Configure Ingress for domain-based routing**

**This project demonstrates real-world DevOps workflow from development to production-style deployment.**

### **Architecture Description — Reddit Clone Deployment**

**This architecture represents the end-to-end deployment of the Reddit Clone application using Docker, DockerHub, AWS, and Kubernetes. The developer first pushes the source code to GitHub and builds a Docker image locally, which is then pushed to DockerHub (`saksham1111/reddit-clone`). An AWS EC2 instance pulls this image from DockerHub and runs it inside a Kubernetes cluster. Kubernetes manages the pods and services, while Ingress handles external traffic routing to the application. This setup ensures containerized deployment, scalability, high availability, and proper traffic management in a cloud-native environment.**

![Untitled Diagram](https://github.com/user-attachments/assets/0517e698-feac-4477-b4e9-26cb97cc0367)


### **Step 1: Clone the Source Code**

**First, clone the GitHub repository:**

git clone https://github.com/Saksham8784/reddit-clone-kubernetes-ingress
cd reddit-clone-k8s-ingress

**Now we have the Node.js application ready for containerization.**

---

### **Step 2: Containerize the Application Using Docker**

**Create a `Dockerfile`:**

FROM node:19-alpine3.15  
WORKDIR /reddit-clone  
COPY . /reddit-clone  
RUN npm install  
EXPOSE 3000  
CMD \["npm","run","dev"\]

### **Explanation:**

* **FROM → Uses lightweight Alpine Node.js base image**  
* **WORKDIR → Sets working directory inside container**  
* **COPY → Copies app source code**  
* **RUN npm install → Installs dependencies**  
* **EXPOSE 3000 → App runs on port 3000**  
* **CMD → Starts development server**

---

### **Step 3: Build Docker Image**

**Build the Docker image:**

docker build \-t saksham1111/reddit-clone .

**This creates a local image ready for deployment.**

---

### **Step 4: Push Image to DockerHub**

**Login to DockerHub:**

docker login

**Push the image:**

docker push saksham1111/reddit-clone

**Now Kubernetes can pull the image from DockerHub.**

---

### **Step 5: Create Kubernetes Deployment**

**Create `Deployment.yml`:**

apiVersion: apps/v1  
kind: Deployment  
metadata:  
 name: reddit-clone-deployment  
 labels:  
   app: reddit-clone  
spec:  
 replicas: 2  
 selector:  
   matchLabels:  
     app: reddit-clone  
 template:  
   metadata:  
     labels:  
       app: reddit-clone  
   spec:  
     containers:  
     \- name: reddit-clone  
       image: saksham1111/reddit-clone  
       ports:  
       \- containerPort: 3000

### **Why Deployment?**

* **Maintains desired number of replicas (2 pods)**  
* **Provides high availability**  
* **Automatically restarts failed pods**  
* **Supports rolling updates**

**Apply it:**

kubectl apply \-f Deployment.yml  
---

### **Step 6: Create Kubernetes Service**

**Create `Service.yml`:**

apiVersion: v1  
kind: Service  
metadata:  
 name: reddit-clone-service  
 labels:  
   app: reddit-clone  
spec:  
 type: NodePort  
 ports:  
 \- port: 3000  
   targetPort: 3000  
   nodePort: 31000  
 selector:  
   app: reddit-clone

### **Why Service?**

* **Pods have dynamic IP addresses**  
* **Service provides stable internal endpoint**  
* **Load balances traffic between pods**

**Apply it:**

kubectl apply \-f Service.yml

**Test using:**

kubectl get services

**You can access the app via:**

http://\<Minikube-IP\>:31000  
---

### **Step 7: Configure Ingress**

### **Why Ingress?**

**Without Ingress:**

* **Every service needs a separate NodePort**  
* **Port management becomes messy**  
* **No domain-based routing**

**Ingress solves this by acting as a Layer 7 reverse proxy.**

---

### **Enable Ingress in Minikube**

minikube addons enable ingress

---

### **Create `ingress.yml`**

apiVersion: networking.k8s.io/v1  
kind: Ingress  
metadata:  
 name: ingress-reddit-app  
spec:  
 rules:  
 \- host: "domain.com"  
   http:  
     paths:  
     \- pathType: Prefix  
       path: "/test"  
       backend:  
         service:  
           name: reddit-clone-service  
           port:  
             number: 3000  
 \- host: "\*.domain.com"  
   http:  
     paths:  
     \- pathType: Prefix  
       path: "/test"  
       backend:  
         service:  
           name: reddit-clone-service  
           port:  
             number: 3000

**Apply it:**

kubectl apply \-f ingress.yml

**Verify:**

kubectl get ingress  
---

### **Step 8: Test the Application**

Test NodePort and Ingress::

curl \-L http://\<minikube-ip\>:31000 Ingress:  
curl \-L http://domain.com/test

If configured correctly, traffic will flow through Ingress → Service → Pods → Application.

<img width="1680" height="1050" alt="Screenshot 2026-02-22 at 9 42 00 PM" src="https://github.com/user-attachments/assets/a22cbbb1-74e6-4188-acf2-dc8caa23a982" />

### **Conclusion**

This project demonstrates a complete cloud-native deployment workflow for a Reddit Clone application using Docker and Kubernetes. Starting from containerizing the Node.js application, pushing the image (`saksham1111/reddit-clone`) to DockerHub, and deploying it on a Kubernetes cluster, the setup showcases real-world DevOps practices. By using Deployments for scalability, Services for internal networking, and Ingress for external traffic routing, the application is made highly available, organized, and production-ready in structure.

Overall, this project strengthens hands-on skills in containerization, orchestration, cloud deployment, and Kubernetes networking — building a strong foundation for modern DevOps and cloud engineering roles.

### 

[image1]: 
