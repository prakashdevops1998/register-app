pipeline {
    agent any 
    environment {
        APP_NAME = "registry-app" // Set your application name here
        RELEASE = "1.0.0"
        DOCKER_USER = "prakashdevops774"
        DOCKER_PASS = 'docker-hub' // Use the correct credentials ID for Docker Hub
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    tools {
        jdk 'java17'
        maven 'Maven3'
    }
    stages {
        stage("clean workspace") {
            steps {
                cleanWs()
            }
        }
        stage("checkout from SCM") {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/prakashdevops1998/register-app.git']]])
            }
        }
        stage("build application") {
            steps {
                script {
                    sh "mvn clean package"
                }
            }
        }
        stage("Test Application") {
            steps {
                script {
                    sh "mvn test"
                }
            }
        }
        stage("SonarQube analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'Sonar-token') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("BUILD & PUSH DOCKER IMAGE") {
            steps {
                script {
                    // Log in to Docker registry
                    withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_PASS') {
                            def dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                            dockerImage.push()
                            dockerImage.push("latest")
                        }
                    }
                }
            }
        }
    }
}
