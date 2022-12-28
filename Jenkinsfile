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
                  export SONAR_SCANNER_VERSION=4.7.0.2747
                    export SONAR_SCANNER_HOME=$HOME/.sonar/sonar-scanner-$SONAR_SCANNER_VERSION-linux
                    curl --create-dirs -sSLo $HOME/.sonar/sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-$SONAR_SCANNER_VERSION-linux.zip
                    unzip -o $HOME/.sonar/sonar-scanner.zip -d $HOME/.sonar/
                    export PATH=$SONAR_SCANNER_HOME/bin:$PATH
                    export SONAR_SCANNER_OPTS="-server"

                    curl --create-dirs -sSLo $HOME/.sonar/build-wrapper-linux-x86.zip https://sonarcloud.io/static/cpp/build-wrapper-linux-x86.zip
                    unzip -o $HOME/.sonar/build-wrapper-linux-x86.zip -d $HOME/.sonar/
                    export PATH=$HOME/.sonar/build-wrapper-linux-x86:$PATH
                '''
            }
        }
stage('Build') {
            steps {
                bat ''' 
                  mkdir build   
                  build-wrapper-linux-x86-64 --out-dir bw-output cmake -S -B build
                 
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
