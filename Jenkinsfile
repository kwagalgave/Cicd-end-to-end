pipeline {
    agent any
    
    tools{
        jdk 'jdk'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git checkout') {
            steps {
               git 'https://github.com/kwagalgave/Cicd-end-to-end.git'
            }
        }
        
        stage('Code compile') {
            steps {
               sh 'mvn clean compile'
            }
        }
        
        stage('sonarqube analysis') {
            steps {
              withSonarQubeEnv('sonar-scanner') {
                  sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=new \
                  -Dsonar.java.binaries=. \
                  -Dsonar.projectKey=new'''
              }
            }
        }
        stage('Code build') {
            steps {
               sh 'mvn clean install'
            }
        }
       
        stage('docker build') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'd415be1e-0d8d-4559-a2a8-f34183b74b09', toolName: 'docker') {
                       sh "docker build -t cicd ."
                  }
               }
            }
        }
        stage('docker push') {
            steps {
              script{
                  withDockerRegistry(credentialsId: 'd415be1e-0d8d-4559-a2a8-f34183b74b09', toolName: 'docker') {
                      sh "docker tag cicd kwagalgave/end-to-end:12"
                      sh "docker push kwagalgave/end-to-end:12"
                 }
              }
           }
       }
       
       stage('deploy to k8s') {
            steps {
               script{
                   withKubeCredentials(kubectlCredentials: [[ credentialsId: 'b7f2cf4e-a6ef-4e7b-a1ed-2782751a1ffb']]) {
                       sh "kubectl apply -f k8s/deployment.yaml"
                  }
               }
            }
        }
       
    }
}
