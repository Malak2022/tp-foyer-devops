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
                git url: 'https://github.com/Malak2022/tp-foyer-devops.git', credentialsId: 'github-malak-creds'
            }
        }
        stage('Build Backend') {
            steps {
                dir('TP-Projet') {
                    sh 'mvn clean package -DskipTests=true'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    script {
                        def scannerHome = tool name: 'SonarQubeScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                        withEnv(["PATH+SONAR=${scannerHome}/bin"]) {
                            sh 'sonar-scanner -Dsonar.projectKey=tp-foyer -Dsonar.sources=. -Dsonar.host.url=${SONAR_HOST} -Dsonar.token=${SONAR_TOKEN}'
                        }
                    }
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
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh 'docker push malak2022/tp-foyer-backend:latest'
                }
            }
        }
    }
}
