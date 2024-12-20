pipeline {
    agent any

    tools {
        // Reference the Maven installation by its exact name as configured in Jenkins
        maven 'maven'
    }

    environment {
        // Define Artifactory registry URL
        registry = "https://your-artifactory-registry.com"
    }

    stages {
        stage("Print Environment Variables") {
            steps {
                echo "----- Environment Variables -----"
                bat 'echo PATH=%PATH%'
                bat 'echo MAVEN_HOME=%MAVEN_HOME%'
                bat 'echo Registry URL=%registry%'
                bat 'mvn -version' // Verify Maven is accessible
                echo "---------------------------------"
            }
        }

        stage("Test Maven") {
            steps {
                echo "----- Testing Maven -----"
                bat 'mvn -version'
                echo "--------------------------"
            }
        }

        stage("Build") {
            steps {
                echo "----------- Build Started ----------"
                bat 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build Completed ----------"
            }
        }
    }

    post {
        always {
            echo "----- Pipeline Execution Completed -----"
        }
        success {
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
