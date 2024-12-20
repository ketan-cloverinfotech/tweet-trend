pipeline {
    agent any

    tools {
        maven 'maven' // Ensure 'maven' is correctly configured in Jenkins
    }

    environment {
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

        stage("Build") {
            steps {
                echo "----------- Build Started ----------"
                bat 'mvn clean package -Dmaven.test.skip=true'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                echo "----------- Build Completed ----------"
            }
        }

        stage("Publish to Artifactory") {
            steps {
                script {
                    echo '<--------------- Publish to Artifactory Started --------------->'

                    // Initialize Artifactory server
                    def server = Artifactory.server('artifactory-server')

                    // Define the upload specification without props initially
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "target/*.jar",
                                "target": "libs-release-local/com/valaxy/demo-workshop/2.1.4/",
                                "exclusions": [ "*.sha1", "*.md5" ]
                            }
                        ]
                    }"""

                    echo "Upload Spec: ${uploadSpec}"

                    // Upload the artifacts to Artifactory
                    def buildInfo = server.upload spec: uploadSpec

                    // Optionally, add properties after successful upload
                    // def buildInfo = server.upload spec: uploadSpec
                    // buildInfo.env.capture = true // Example of capturing environment variables

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
