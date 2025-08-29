


# 🚀 Docker Swarm Cluster – Deploy Nginx Web App

# This project demonstrates how to set up a **Docker Swarm cluster** on a single-node (Amazon Linux) and deploy a simple **Nginx-based web application** using Docker and Swarm stack deployment.

---

## 📋 Prerequisites
- Amazon Linux EC2 Instance (or any Linux server with Docker support)
- Basic knowledge of Docker & Linux commands
- Security group with port `8080` open

---

## ⚙️ Step 1: Install Docker
```bash
sudo yum update -y
sudo amazon-linux-extras enable docker
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker

docker --version
````

---

## ⚡ Step 2: Enable Docker Swarm

```bash
sudo docker swarm init --advertise-addr $(hostname -I | awk '{print $1}')
```

---

## 📂 Step 3: Prepare Your Project

```bash
mkdir harshalapp
cd ~/harshalapp
nano Dockerfile
```

**Dockerfile**

```dockerfile
FROM nginx:alpine

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom config
COPY harshalapp.conf /etc/nginx/conf.d/

# Copy website files
COPY . /usr/share/nginx/html/
```

---

## 📝 Step 4: Create Nginx Config

```bash
nano harshalapp.conf
```

**harshalapp.conf**

```nginx
server {
    listen 8080;

    server_name _;

    root /usr/share/nginx/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    error_log /var/log/nginx/harshalapp_error.log;
    access_log /var/log/nginx/harshalapp_access.log;
}
```

---

## 🛠️ Step 5: Build Docker Image

```bash
sudo docker build -t harshalapp:latest .
sudo docker images
```

---

## 📦 Step 6: Create `docker-compose.yml`

Go **outside project folder**:

```bash
mkdir ~/harshal-swarm
cd ~/harshal-swarm
nano docker-compose.yml
```

**docker-compose.yml**

```yaml
version: "3.8"

services:
  web:
    image: harshalapp:latest
    ports:
      - target: 8080
        published: 8080
        mode: host
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure
```

---

## 🚀 Step 7: Deploy Stack

```bash
sudo docker stack deploy -c docker-compose.yml harshalstack
```

---

## 🔎 Step 8: Verify Deployment

```bash
sudo docker stack ls
sudo docker service ls
sudo docker service ps harshalstack_web
sudo docker service logs -f harshalstack_web
```

---

## 🌐 Step 9: Test in Browser

Open:

```
http://<EC2_PUBLIC_IP>:8080
```

---

## ✅ Summary

You have successfully:

1. Installed Docker & enabled Swarm
2. Built a custom Nginx image
3. Created a stack using Docker Compose
4. Deployed the service in Swarm mode

---

## 📌 Notes

* To scale services:

  ```bash
  sudo docker service scale harshalstack_web=3
  ```
* To remove stack:

  ```bash
  sudo docker stack rm harshalstack
  ```

---

## 🧑‍💻 Author

**Harshal Mali** – *DevOps & Cloud Enthusiast*

```

---

👉 Do you also want me to add a **GitHub project folder structure with all files (`Dockerfile`, `harshalapp.conf`, `docker-compose.yml`)** so that your repo is ready-to-use?
```
