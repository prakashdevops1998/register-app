pipeline {
    agent any 
    
    tools {
        jdk 'java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "registry-app" // Set your application name here
        RELEASE = "1.0.0"
        DOCKER_USER = "prakashdevops774"
        DOCKER_PASS = 'docker-hub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
    }
}
