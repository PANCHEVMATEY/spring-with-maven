pipeline {
    agent {
        label 'build-in-slave'
    }
    tools {
        nodejs 'Maven'
        dockerTool 'Docker'
    }
    triggers {
        githubPush()
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_creds')
        IMAGE_NAME = 'mateyp/spring-with-maven'
    }
    stages {
        stage('Clean') {
            steps {
                cleanWs()
        }
    }
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/PANCHEVMATEY/spring-with-maven.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker Login') {
            steps {
                sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
            }
        }
        stage('Docker build and tag') {
            steps {
                sh 'docker build -t $IMAGE_NAME -f Dockerfile .'
                sh 'docker tag $IMAGE_NAME $IMAGE_NAME:${BUILD_NUMBER}'
            }
        }
        stage('Docker Push') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
           steps {
                sh 'docker run -d -p port:8080 ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
    }
}