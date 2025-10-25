pipeline {
    agent any

    environment {
        // Repos for Task 1 and Task 3
        BACKEND_REPO = 'https://github.com/<your-username>/task1-backend.git'
        FRONTEND_REPO = 'https://github.com/<your-username>/task3-frontend.git'

        // Docker image names
        BACKEND_IMAGE = '<your-dockerhub-username>/task1-backend:latest'
        FRONTEND_IMAGE = '<your-dockerhub-username>/task3-frontend:latest'
    }

    stages {

        stage('Clone Repositories') {
            steps {
                echo 'Cloning Task 1 (Backend) and Task 3 (Frontend)...'
                sh 'rm -rf task1-backend task3-frontend || true'
                sh 'git clone ${BACKEND_REPO} task1-backend'
                sh 'git clone ${FRONTEND_REPO} task3-frontend'
            }
        }

        stage('Build Backend') {
            steps {
                dir('task1-backend') {
                    echo 'Building backend JAR...'
                    sh 'mvn clean package -DskipTests'
                    echo 'Building backend Docker image...'
                    sh 'docker build -t ${BACKEND_IMAGE} .'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('task3-frontend') {
                    echo 'Building frontend app...'
                    sh 'npm install'
                    sh 'npm run build'
                    echo 'Building frontend Docker image...'
                    sh 'docker build -t ${FRONTEND_IMAGE} .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                parallel (
                    "Backend Tests": {
                        dir('task1-backend') {
                            echo 'Running backend unit tests...'
                            sh 'mvn test'
                        }
                    },
                    "Frontend Tests": {
                        dir('task3-frontend') {
                            echo 'Running frontend tests...'
                            sh 'npm test -- --watchAll=false || true'
                        }
                    }
                )
            }
        }

        stage('Push Docker Images') {
            steps {
                echo 'Pushing Docker images to DockerHub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${BACKEND_IMAGE}"
                    sh "docker push ${FRONTEND_IMAGE}"
                }
            }
        }

        stage('Verification Run') {
            steps {
                echo 'Starting Docker containers for verification...'
                sh "docker run -d -p 8080:8080 --name backend-test ${BACKEND_IMAGE}"
                sh "docker run -d -p 3000:80 --name frontend-test ${FRONTEND_IMAGE}"
                sh "sleep 15"
                sh "docker ps"
                sh "docker logs backend-test || true"
                sh "docker logs frontend-test || true"
            }
        }
    }

    post 
    {
        always 
	{
            echo 'Cleaning up containers...'
            sh 'docker stop backend-test frontend-test || true'
            sh 'docker rm backend-test frontend-test || true'
        }
        success 
            { echo 'CI/CD pipeline success '}
        failure
            { echo 'Pipeline failed '}
    }
}

