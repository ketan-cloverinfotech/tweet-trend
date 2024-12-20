pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage("Test Maven") {
            steps {
                echo "----- Testing Maven -----"
                bat 'mvn -version'
                echo "--------------------------"
            }
        }
    }

    post {
        always {
            echo "----- Pipeline Execution Completed -----"
        }
    }
}
