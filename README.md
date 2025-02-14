# Highly-Available-WebApp-Azure

# Highly Available Web App with Linux, Docker, and Nginx Load Balancer on Azure

## Overview
This project demonstrates deploying a **highly available web application** using:
- **Azure Virtual Machines (VMs)**
- **Dockerized Web Application**
- **Nginx Load Balancer**
- **Health Checks & Monitoring**

The architecture consists of:
- **VM1 (webserver1)** → Runs the web app in a Docker container
- **VM2 (webserver2)** → Runs the web app in a Docker container
- **VM3 (Load Balancer)** → Runs Nginx to distribute traffic between VM1 & VM2

## Steps to Deploy

### 1️⃣ Create Linux Virtual Machines on Azure
1. Log in to **Azure Portal**
2. Create **3 Ubuntu VMs**
3. Assign **public IPs** for SSH access

### 2️⃣ Install Docker on VM1 & VM2
```sh
ssh username@VM_Public_IP
sudo apt update
sudo apt install docker-ce -y
```

### 3️⃣ Deploy Web Application in Docker (VM1 & VM2)
```sh
mkdir ~/webapp && cd ~/webapp
```
Create an `index.html` file:
```sh
echo "<html><body><h1>Welcome to Web App!</h1></body></html>" > index.html
```
Create a `Dockerfile`:
```sh
echo "FROM nginx:alpine\nCOPY index.html /usr/share/nginx/html/index.html" > Dockerfile
```
Build & run the container:
```sh
docker build -t webapp:latest .
docker run -d -p 80:80 --name webapp-container webapp:latest
```

### 4️⃣ Install & Configure Nginx Load Balancer (VM3)
```sh
ssh username@LoadBalancer_VM_Public_IP
sudo apt update
sudo apt install nginx -y
```
Edit the Nginx configuration:
```sh
sudo nano /etc/nginx/sites-available/default
```
Add the following:
```nginx
upstream backend {
    server webserver1_Private_IP;
    server webserver2_Private_IP;
}
server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```
Restart Nginx:
```sh
sudo systemctl restart nginx
```

### 5️⃣ Test the Load Balancer
Visit `http://LoadBalancer_VM_Public_IP` in a browser.

### 6️⃣ Enable Health Checks
Modify `/etc/nginx/sites-available/default`:
```nginx
upstream backend {
    server webserver1_Private_IP max_fails=3 fail_timeout=30s;
    server webserver2_Private_IP max_fails=3 fail_timeout=30s;
}
```
Restart Nginx:
```sh
sudo systemctl restart nginx
```

### 7️⃣ (Optional) Monitoring with Prometheus & Grafana
```sh
sudo apt install prometheus grafana -y
sudo systemctl start prometheus
sudo systemctl start grafana-server
```
Access Grafana: `http://LoadBalancer_VM_Public_IP:3000`

## Conclusion
✅ **Highly Available Web App on Azure**  
✅ **Traffic Load Balancing with Nginx**  
✅ **Scalable & Fault-Tolerant Deployment**  

🚀 **Perfect for real-world cloud deployments & DevOps portfolios!**
