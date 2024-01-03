pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/suresh-subramanian2013/Vitual-Browser'
            }
        }
        stage('Dependency Check') {
            steps {
                script {
                    def scanCmd = 'dependency-check --scan ./ --odcInstallation=DP'
                    sh scanCmd
                }
                junit allowEmptyResults: true, testResults: '**/dependency-check-report.xml'
            }
        }
        stage('Sonar Scanner') {
            steps {
                withSonarQubeEnv('sonar') {
                    script {
                        sh ''' 
                            $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=VirtualBrowser \
                            -Dsonar.projectName=VirtualBrowser 
                        '''
                    }
                }
            }
        }
       
        stage('Docker Build & Tag Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'Docker') {
                        dir('/home/ubuntu/.jenkins/workspace/Virtual-Browser/.docker/google-chrome') {
                            sh "docker build -t suresh10214/vb:latest ."
                        }
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

