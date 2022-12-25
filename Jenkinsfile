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

        stage('Download Build Wrapper') {
            steps {
                bat '''
                  mkdir -p .sonar
                  curl -sSLo .sonar/build-wrapper-linux-x86.zip https://sonarcloud.io/static/cpp/build-wrapper-linux-x86.zip
                  tar -xf  .sonar/build-wrapper-linux-x86.zip -C .sonar/
                '''
            }
        }
stage('Build') {
            steps {
                bat ''' 
                  mkdir build                 
                  cmake -C C:\\MinGW\\bin\\gcc -S . -B build
                '''
            }
        }
        

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'; // Name of the SonarQube Scanner you created in "Global Tool Configuration" section
                    withSonarQubeEnv() {
                        bat "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
    }
}
