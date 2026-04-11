# A Cloud-Native Full Stack Web-App on Kubernetes 

a clean, production-style system with:

✔  Frontend\
✔  Backend\
✔  Database\
✔  Cache (Redis)\
✔  Ingress (with proper routing, Single URL Access)
✔  GitHub CI/CD (auto deploy)


## What it does:

- User opens website
- **React** UI loads
- User clicks button

- **Backend** fetches:
  - Cached data (Redis) OR
  - Database (PostgreSQL)

- Returns response
- **Kubernetes** → runs, scales, and connects everything

----

## Features

✔ Fully containerized\
✔ Fully deployed on Kubernetes\
✔ Production-style architecture\
✔ Mini SaaS style\
✔ Microservices architecture

---

## Architecture

<img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/bf8212bc-5986-4068-89b1-48ce86fb8328" />

<img width="1358" height="854" alt="image" src="https://github.com/user-attachments/assets/94048ef5-4d21-4317-a309-a98b35402758" />


---

## Project Flow

```bash
User 
 ↓
Ingress (TLS-ready)
 ↓
Frontend Service
 ↓
Frontend Pod (React + Nginx)
 ↓
(API call via /api)
 ↓
Backend Service
 ↓
Backend Pod (Node.js)
 ↓
Redis (Cache)
 ↓
PostgreSQL (Database)
```
---

## Project Structure

```bash
kube-fullstack-app/
├── frontend/
│   ├── src/
│   │   └── App.js
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── package.json
│
├── backend/
│   ├── index.js
│   ├── db.js
│   ├── package.json
│   ├── Dockerfile
│
├── k8s/
│   ├── configmap.yaml
│   ├── secret.yaml
│   ├── postgres.yaml
│   ├── redis.yaml
│   ├── backend.yaml
│   ├── frontend.yaml
│   ├── ingress.yaml
│
├── .github/
│   └── workflows/
│       └── deploy.yml
```
-------

## STEP-BY-STEP BUILD


### STEP#1. Backend (Add Redis Support)


