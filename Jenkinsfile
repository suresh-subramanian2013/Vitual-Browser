pipeline {
    agent any


   environment {
        SCANNER_HOME = tool 'sonar-scanner'
        WORKSPACE_DIR = '/home/ubuntu/jenkins-workspace/Vitual-Browser'
    }


   stages {
        stage('git Checkout') {
            steps {
               dir(WORKSPACE_DIR) {
                    git branch: 'main', url: 'https://github.com/suresh-subramanian2013/Vitual-Browser.git'
                }
            }
        }


       stage('Check User') {
            steps {
                script {
                    def currentUser = sh(script: 'whoami', returnStdout: true).trim()
                    echo "Current user running the job: ${currentUser}"
                }
            }
        }


       stage('Dependency Check') {
            steps {
                script {
                    dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }


       stage('Sonar Scanner') {
            steps {
                script {
                    withSonarQubeEnv('sonar-server') {
                        sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=VirtualBrowser -Dsonar.projectName=VirtualBrowser"
                    }
                }
            }
        }


       stage('Docker Build and Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir("${WORKSPACE_DIR}/.docker/opera")  {
                            sh "docker build -t suresh10214/vb:latest ."
                        }
                    }
                }
            }
        }


       stage('Image Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push suresh10214/vb:latest"
                    }
                }
            }
        }


       stage('Deploy') {
            steps {
                script {
                    sh "docker-compose up -d"
                }
            }
        }
    }
}
