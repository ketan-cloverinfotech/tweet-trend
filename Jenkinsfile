pipeline {
    agent any

    tools {
        // Reference the Maven installation by its exact name as configured in Jenkins
        maven 'maven'
    }

    environment {
        // Define Artifactory registry URL
        ARTIFACTORY_URL = "https://trialmjjh4j.jfrog.io/artifactory"
    }

    stages {
        stage("Print Environment Variables") {
            steps {
                echo "----- Environment Variables -----"
                bat 'echo PATH=%PATH%'
                bat 'echo MAVEN_HOME=%MAVEN_HOME%'
                bat 'echo ARTIFACTORY_URL=%ARTIFACTORY_URL%'
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
                // Create jarstaging directory if not exists
                bat 'if not exist jarstaging mkdir jarstaging'
                bat 'mvn clean package -Dmaven.test.skip=true -DoutputDirectory=jarstaging'
                echo "----------- Build Completed ----------"
                // Archive the artifacts
                archiveArtifacts artifacts: 'jarstaging/**', fingerprint: true
            }
        }

        stage("Publish to Artifactory") {
            steps {
                script {
                    echo '<--------------- Publish to Artifactory Started --------------->'

                    // Initialize Artifactory server using the configured server ID
                    def server = Artifactory.server 'artifactory-server' // Replace with your Server ID if different

                    // Define the upload specification
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging\\com\\valaxy\\demo-workshop\\2.1.4\\*.jar",
                                "target": "libs-release-local/com/valaxy/demo-workshop/2.1.4/",
                                "props": "build.id=${env.BUILD_ID};build.number=${env.BUILD_NUMBER}",
                                "exclusions": [ "*.sha1", "*.md5" ]
                            }
                        ]
                    }"""

                    // Upload the artifacts to Artifactory
                    def buildInfo = server.upload spec: uploadSpec

                    // Publish the build info to Artifactory
                    server.publishBuildInfo buildInfo

                    echo '<--------------- Publish to Artifactory Ended --------------->'
                }
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
