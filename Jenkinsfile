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
                  
                  $env:SONAR_SCANNER_VERSION = "4.7.0.2747"
                   $env:SONAR_DIRECTORY = [System.IO.Path]::Combine($(get-location).Path,".sonar")
                   $env:SONAR_SCANNER_HOME = "$env:SONAR_DIRECTORY/sonar-scanner-$env:SONAR_SCANNER_VERSION-windows"
                   rm $env:SONAR_SCANNER_HOME -Force -Recurse -ErrorAction SilentlyContinue
                    New-Item -path $env:SONAR_SCANNER_HOME -type directory
                    (New-Object System.Net.WebClient).DownloadFile("https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-$env:SONAR_SCANNER_VERSION-windows.zip", "$env:SONAR_DIRECTORY/sonar-scanner.zip")
                    Add-Type -AssemblyName System.IO.Compression.FileSystem
                    [System.IO.Compression.ZipFile]::ExtractToDirectory("$env:SONAR_DIRECTORY/sonar-scanner.zip", "$env:SONAR_DIRECTORY")
                    rm ./.sonar/sonar-scanner.zip -Force -ErrorAction SilentlyContinue
                    $env:SONAR_SCANNER_OPTS="-server"

                    rm "$env:SONAR_DIRECTORY/build-wrapper-win-x86" -Force -Recurse -ErrorAction SilentlyContinue
                    (New-Object System.Net.WebClient).DownloadFile("https://sonarcloud.io/static/cpp/build-wrapper-win-x86.zip", "$env:SONAR_DIRECTORY/build-wrapper-win-x86.zip")
                    [System.IO.Compression.ZipFile]::ExtractToDirectory("$env:SONAR_DIRECTORY/build-wrapper-win-x86.zip", "$env:SONAR_DIRECTORY")
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
