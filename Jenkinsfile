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
                cleanWs() //clean workspace
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
                // Runs 'npm test' to execute tests
                // This step is for testing the Node.js modules
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
                sh 'docker container rm -f spring-with-maven || true'
                sh 'docker container run -d --name spring-with-maven mateyp/spring-with-maven'
            }
        }
    }
}
