pipeline {
    agent any 

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage ('git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/suresh-subramanian2013/Vitual-Browser.git' 
            }
        }

        stage ('Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage ('Sonar Scanner') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=VirtualBrowser -Dsonar.projectName=VirtualBrowser"
                }
            }
        }

       stage ("Docker Build and Tag") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/root/.jenkins/workspace/VB/.docker/opera') {
                            sh "docker build -t suresh10214/vb:latest ."
                        }
                    }
                }
            }
        }

   }
}
