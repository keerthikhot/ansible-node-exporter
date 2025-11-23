# ansible-node-exporter
# 🚀 Monitoring Stack with Ansible, Prometheus, Grafana & Node Exporter

This project demonstrates a complete, lightweight, and production-relevant monitoring stack deployed on a **MacBook M2** using **Docker** and **Ansible**.

It automates Node Exporter installation on multiple Ubuntu nodes and sets up Prometheus + Grafana locally for visualization.

---

# 🎯 Project Overview

This setup simulates a real DevOps/SRE monitoring environment:

- **Ansible** → deploys Node Exporter to 3 Ubuntu nodes  
- **Node Exporter** → exposes system metrics on port `9100`  
- **Prometheus (Docker)** → scrapes metrics  
- **Grafana (Docker)** → visualizes metrics  
- **Docker networking** → connects all containers without recreating nodes  

---

# 📌 Architecture Diagram

MacBook (Docker Host)
│
├── Prometheus (localhost:9090)
├── Grafana (localhost:3000)
│
└── Ubuntu Nodes (Running in Docker)
├── k8s-master
├── k8s-worker1
└── k8s-worker2
└── node_exporter → 9100

---

# Prometheus scrapes the node_exporters using Docker internal DNS:

k8s-master:9100
k8s-worker1:9100
k8s-worker2:9100


---

# 📁 Folder Structure


---

# 🛠️ Deployment Steps

## 1️⃣ Start Prometheus + Grafana

```bash
cd docker
docker-compose up -d

Prometheus UI → http://localhost:9090
Grafana UI → http://localhost:3000
```

## 2️⃣ create containers 
```
docker run -d --name k8s-master -p 2222:22 ubuntu:22.04 sleep infinity
docker run -d --name k8s-worker1 -p 2223:22 ubuntu:22.04 sleep infinity
docker run -d --name k8s-worker2 -p 2224:22 ubuntu:22.04 sleep infinity
docker run -d --name ansible-controller -p 2225:22 ubuntu:22.04 sleep infinity
```

## 3️⃣ set up ssh 
```
sudo apt install openssh-server
ls -la .ssh
ssh-keygen -t ed25519 -C "ansible default"
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@172.17.0.x
```

## 3️⃣ Attach worker nodes to Prometheus network
```
docker network connect prom-stack_default k8s-master
docker network connect prom-stack_default k8s-worker1
docker network connect prom-stack_default k8s-worker2
```

# 3️⃣ Deploy Node Exporter using Ansible
```
ansible-playbook playbooks/deploy-node-exporter.yml
```

# 4️⃣ Verify Node Exporter
```
curl http://localhost:9100/metrics
```
# 5️⃣ Verify Prometheus Targets
```
👉 http://localhost:9090/targets
Expected: 3 / 3 targets UP
```

---

# 📊 Grafana Setup
# 1️⃣ Add Prometheus Data Source
```
Grafana → Connections → Data Sources → Add → Prometheus
Use URL: http://prometheus:9090
```

# 2️⃣ Import Dashboard
```
Go to:
Grafana → Dashboards → New → Import
Upload: roles/grafana/files/dashboards/node_exporter_full.json
```

---

# 🖼 Screenshots
<img width="812" height="450" alt="Screenshot 2025-11-23 at 4 14 28 PM" src="https://github.com/user-attachments/assets/b10ec8f4-4577-4ef6-b070-a7857eb3299b" />
<img width="1313" height="830" alt="Screenshot 2025-11-23 at 4 31 43 PM" src="https://github.com/user-attachments/assets/829952fc-5d2e-425c-bcc5-1e5f03c82a28" />
<img width="1469" height="629" alt="Screenshot 2025-11-23 at 5 02 45 PM" src="https://github.com/user-attachments/assets/d288ce2d-9d5b-40d7-8c0e-b8c46b60dc30" />
<img width="1468" height="795" alt="Screenshot 2025-11-23 at 5 02 55 PM" src="https://github.com/user-attachments/assets/a42b0a28-fdf9-420d-82bd-a5667de2fa39" />
<img width="1449" height="467" alt="Screenshot 2025-11-23 at 9 51 36 PM" src="https://github.com/user-attachments/assets/8375bf13-d66b-46c0-b0e1-e1a157dea19b" />












