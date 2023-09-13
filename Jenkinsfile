pipeline {
    agent any
    
     tools {
        jdk 'jdk11'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('clean Workspace') {
            steps {
                echo 'Hello World'
            }
        }
        
        stage ('checkout scm') {
            steps {
                git branch: 'main', url: 'https://github.com/Velocity9919/Python-Webapp.git'
            }
        }
        
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format XML ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy FS SCan') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                
                withSonarQubeEnv('sonar-server'){
                  sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=python-webapp \
                    -Dsonar.projectKey=python-webapp ''' 
               }
            }
        }
        
        stage("quality gate"){
            steps {
                script {
                  waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
                }
            }
        }
        
        stage('Docker Build & Tag') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'dockerhub-cred') {
                        sh "make image"
                    }
                }
            }
        }
        
        stage('Trivy docker image SCan') {
            steps {
                sh "trivy image nareshbabu1991/python-webapp:latest"
            }
        }
        
        stage('Docker Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'dockerhub-cred') {
                        sh "make push"
                    }
                }
            }
        }
        
        stage('Docker Deploy') {
            steps {
                sh "docker run -d -p 5000:5000 nareshbabu1991/python-webapp"
            }
        }
    }
}
