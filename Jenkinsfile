pipeline {
    agent { label 'windows' }

    environment {
        // username/password credential will expose *_USR and *_PSW vars
        GITHUB_CREDS = credentials('github-packages-cred')
        // tool(...) returns the installation path on the node
        JAVA_HOME = tool name: 'jdk-my'
        MAVEN_HOME = tool name: 'my-mvn'
        // Windows PATH separator is ';' and use backslashes for tool paths
        PATH = "${JAVA_HOME}\\bin;${PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build & Deploy') {
            steps {
                configFileProvider([configFile(fileId: 'maven-github-settings', variable: 'MAVEN_SETTINGS')]) {
                    // Use 'bat' on Windows agents and expand Jenkins-provided env vars with %VAR%
                    bat '''
                        @echo off
                        setlocal

                        rem Export GitHub credentials into env vars consumed by your settings.xml (if configured that way)
                        set GH_USER=%GITHUB_CREDS_USR%
                        set GH_TOKEN=%GITHUB_CREDS_PSW%

                        rem Call Maven using the Windows mvn wrapper
                        "%MAVEN_HOME%\\bin\\mvn.cmd" -s "%MAVEN_SETTINGS%" -B clean package
                        "%MAVEN_HOME%\\bin\\mvn.cmd" -s "%MAVEN_SETTINGS%" -B deploy

                        endlocal
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Build and deployment completed successfully."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
