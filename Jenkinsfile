pipeline {
    agent any

    environment {
        MAVEN_HOME = "C:\\Program Files\\Apache Maven\\apache-maven-3.9.2"
        PATH = "${MAVEN_HOME}\\bin;%PATH%"
        registry = "https://your-artifactory-registry.com"
    }

    stages {
        stage("Build") {
            steps {
                echo "----------- Build Started ----------"
                bat 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build Completed ----------"
            }
        }

        stage("Jar Publish") {
            steps {
                script {
                    // Jar Publish Logic
                }
            }
        }
    }

    post {
        always {
            echo "----------- Pipeline Completed ----------"
        }
        success {
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
