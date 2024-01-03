pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/suresh-subramanian2013/Vitual-Browser.git'
            }
        }
        stage('Depanancy check'){
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Sonar Scanner'){
            steps {
                withSonarQubeEnv('sonar') {
                 sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=VirtualBrowser \
                 -Dsonar projectName=VirtualBrower '''            
       }
            }
        }
       stage('Docker Build & Tag Image'){
            steps{
                withDockerRegistry(credentialsId: 'docker-cred', toolName: 'Docker') 
                {dir('/home/ubuntu/.jenkins/workspace/Virtual-Browser/.docker/google-chrome ') {
                    sh "docker build -t suresh10214/vb.latest"
               }        
           }  
         }
       }
       stage('Trivy Docker Scan') {
            steps {
                script {
                    sh "trivy image suresh10214/vb:latest > trivy.txt"
                }
            }
        }
       stage('Docker push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'Docker') {
                        
                            sh "docker build -t suresh10214/vb:latest ."
                        }
                    }
                }
            }
        }
                
      }
    
            
    

