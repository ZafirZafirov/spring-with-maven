pipeline {
    agent {
        label 'my-ssh-slave'
    }
    tools {
        nodejs 'nodejs'
        dockerTool 'Docker_installations'
        maven 'Maven tool'
    }
    triggers {
        githubPush()
    }
    environment {
      DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_creds')
      IMAGE_NAME = 'zafirzafirov/springwithmaven'
    }
    stages {
        stage('Clean') {
            steps {
                cleanWs()
            }
        }
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/ZafirZafirov/spring-with-maven.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install' //This is for building the nodejs project
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test' //This is for testing the nodejs modules
            }
        }
       stage('Docker login'){
           steps {
               sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
       }
        }
        stage('Docker build and tag'){
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
    stage('Docker Push'){
            steps {
              sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
           steps {
                sh 'docker run -d -p 7000:8081 ${IMAGE_NAME}:${BUILD_NUMBER}'
           }
        }
    }
}
