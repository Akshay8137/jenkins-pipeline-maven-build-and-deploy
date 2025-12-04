pipeline {
    agent any

    environment {
        GITHUB_CREDS = credentials('github-packages-cred')

        JAVA_HOME = tool name: 'jdk-my'
        MAVEN_HOME = tool name: 'my-mvn'
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

                    bat """
                        REM ---- Set GitHub Variables (Windows way) ----
                        set GH_USER=%GITHUB_CREDS_USR%
                        set GH_TOKEN=%GITHUB_CREDS_PSW%

                        REM ---- Run Maven ----
                        "%MAVEN_HOME%\\bin\\mvn" -s "%MAVEN_SETTINGS%" -B clean package
                        "%MAVEN_HOME%\\bin\\mvn" -s "%MAVEN_SETTINGS%" -B deploy
                    """
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
