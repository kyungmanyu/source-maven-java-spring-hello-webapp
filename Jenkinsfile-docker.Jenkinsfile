pipeline {
  agent none

  stages {
    stage('Checkout') {
      agent any
      steps {
        git branch: 'main', url: 'https://github.com/kyungmanyu/source-maven-java-spring-hello-webapp.git'
      }
    }
    stage('Build') {
      agent {
        docker { image 'maven:3-openjdk-8' }
      }
      steps {
        sh 'mvn clean package -DskipTests=true'
      }
    }
    stage('Test') {
      agent {
        docker { image 'maven:3-openjdk-8' }
      }
      steps {
        sh 'mvn test'
      }
    }
    stage('Build Docker Image') {
      agent any
      steps {
        sh 'docker image build -t hello-world .'
      }
    }
    stage('Tag Docker Image') {
      agent any
      steps {
        sh 'docker image tag hello-world docker.io/kyungmanyu/hello-world:latest'
        sh 'docker image tag hello-world docker.io/kyungmanyu/hello-world:$BUILD_NUMBER'
      }
    }
    stage('Publish Docker Image') {
      agent any
      steps {
        withDockerRegistry(credentialsId: 'docker-hub-token', url: 'https://index.docker.io/v1/') {
          sh 'docker image push docker.io/kyungmanyu/hello-world:latest'
          sh 'docker image push docker.io/kyungmanyu/hello-world:$BUILD_NUMBER'
        }
      }
    }
    stage('Run Docker Container') {
      agent {
        docker { image 'docker:dind' }
      }
      steps {
        // sh 'docker -H tcp://172.31.42.13:2375 container run --detach --name <CONTAINER_NAME> -p 80:8080 <IMAGE>:$BUILD_NUMBER'
        sh 'docker -H tcp://172.31.42.13:2375 container run --detach --name hello-world -p 80:8080 docker.io/kyungmanyu/hello-world:$BUILD_NUMBER'
      }
    }
  }
}