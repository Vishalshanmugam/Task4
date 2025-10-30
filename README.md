# 🧩 Task 4 — CI/CD Pipeline

## 📖 Overview
This task demonstrates a **CI/CD pipeline** for the applications created in:
- **Task 1:** Spring Boot + MongoDB backend  
- **Task 3:** React + TypeScript + Ant Design frontend  

The pipeline automatically:
- Builds, tests, and packages both backend and frontend  
- Builds Docker images for both services  
- Optionally pushes them to a container registry (e.g., Docker Hub or GitHub Container Registry)

---

## 🏗️ Repository Structure

Task1/ → Backend (Spring Boot + MongoDB)
Task3/ → Frontend (React + TypeScript + Ant Design)
.github/
└── workflows/
└── ci-cd.yml ← GitHub Actions pipeline file

## 🚀 Running the Pipeline

### 1. Commit and push your code to GitHub:
```bash
git add .
git commit -m "Add CI/CD pipeline"
git push origin main
```

### 2. Open the Actions tab in your GitHub repository.
The pipeline will start automatically on push.

## 3. Wait until both jobs complete:

✅ Backend Build Successful

✅ Frontend Build Successful

## 🐳 Docker Run Commands
🔹 1. Backend (Task 1)

Your Spring Boot backend runs on port 8080.
If MongoDB is running locally or in another container, we’ll also link it.

### 🅰️ If using local MongoDB
```bash
docker run -d \
  --name task1-backend \
  -p 8080:8080 \
  -e SPRING_DATA_MONGODB_URI=mongodb://localhost:27017/taskdb \
  task1-backend:latest
```

### 🅱️ If using a MongoDB container

1. First start MongoDB:
```bash
docker run -d \
  --name mongodb \
  -p 27017:27017 \
  mongo:latest
```

2. Then run backend and link it:
```bash
docker run -d \
  --name task1-backend \
  --link mongodb:mongo \
  -p 8080:8080 \
  -e SPRING_DATA_MONGODB_URI=mongodb://mongo:27017/taskdb \
  task1-backend:latest
```

* create a mongodb free account and create a username and password to access the Database

3. Access backend:
👉 http://localhost:8080/api/tasks

### 🔹 2. Frontend (Task 3)

Your React app (built via Vite + Nginx) runs on port 80 inside the container.

### Run:
```bash
docker run -d \
  --name task3-frontend \
  -p 3000:80 \
  -e REACT_APP_API_URL=http://localhost:8080/api \
  task3-frontend:latest
```
