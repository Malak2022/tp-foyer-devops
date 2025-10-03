pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        SONAR_HOST = 'http://192.168.33.10:9000'
        SONAR_TOKEN = credentials('sonar-token')
        DOCKER_HUB_CREDS = credentials('docker-hub')
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Malak2022/tp-foyer-devops.git', credentialsId: 'github-creds', branch: 'main'
            }
        }
        stage('Build Backend') {
            steps {
                dir('TP-Projet') {
                    sh 'mvn clean package'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'sonar-scanner -Dsonar.projectKey=tp-foyer -Dsonar.sources=. -Dsonar.host.url=${SONAR_HOST} -Dsonar.token=${SONAR_TOKEN}'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                dir('TP-Projet') {
                    sh 'docker build -t malak2022/tp-foyer-backend:latest .'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKER_HUB_CREDS_PSW | docker login -u $DOCKER_HUB_CREDS_USR --password-stdin'
                sh 'docker push malak2022/tp-foyer-backend:latest'
            }
        }
    }
}
