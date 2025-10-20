# 🧩 Acronis ↔ AlgaPSA Integration Middleware  
**Repository:** [jpasley/Acronis-AlgaPSA-Integration](https://github.com/jpasley/Acronis-AlgaPSA-Integration)  
**Docker Image:** `ghcr.io/jpasley/acronis-algapsa-integration:latest`  
**Maintainer:** Jacob Pasley (Venterprise MSP)

Lightweight integration middleware that synchronizes **Acronis Cyber Protect Cloud** tenants with **AlgaPSA** customers using both platforms’ OpenAPI specifications.  
Includes a modern **FastAPI backend**, **Setup Wizard**, **Admin Dashboard**, and full **Docker auto-install** scripts for one-command deployment on Ubuntu.

---

## 🚀 Quick Install (Ubuntu Server, one line)

Run this on a **clean Ubuntu 22.04+** or newer server:

```bash
wget -qO- https://raw.githubusercontent.com/jpasley/Acronis-AlgaPSA-Integration/main/scripts/bootstrap.sh | sudo bash -s -- \
  --repo https://github.com/jpasley/Acronis-AlgaPSA-Integration.git \
  --image-repo ghcr.io/jpasley/acronis-algapsa-integration \
  --port 8000
