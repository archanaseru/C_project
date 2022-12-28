pipeline {
    agent any

    options {
        // This is required if you want to clean before build with the "Workspace Cleanup Plugin"
        skipDefaultCheckout(true)
    }

    environment {
        SONARQUBE_URL = 'https://sonarcloud.io/' // Replace with your SonarQube server URL
    }    

    stages {
        stage('SCM') {
            steps {
                // Clean before build using the "Workspace Cleanup Plugin"
                cleanWs()
                checkout scm
            }
        }

      
stage('Build') {
            steps {
                bat ''' 
                  mkdir build   
                  C:\\Users\\archa\\Downloads\\build-wrapper-win-x86\\build-wrapper-win-x86 --out-dir bw-output cmake -S -B build
                 
                '''
            }
        }
        

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'; // Name of the SonarQube Scanner you created in "Global Tool Configuration" section
                    withSonarQubeEnv() {
                        bat "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.organization=archanaseru \
                        -Dsonar.projectKey=archanaseru_linux-cmake-jenkins-sq \
                          -Dsonar.sources=. \
                          -Dsonar.cfamily.build-wrapper-output=bw-output \
                            -Dsonar.host.url=https://sonarcloud.io "
                    }
                }
            }
        }
    }
}
