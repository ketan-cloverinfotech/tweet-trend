pipeline {
    agent any

    tools {
        // Ensure 'Maven 3.9.2' is configured under Jenkins > Global Tool Configuration
        maven 'Maven 3.9.2'
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
                bat 'mvn -version'
                echo "---------------------------------"
            }
        }

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
                    echo '<--------------- Jar Publish Started --------------->'

                    // Initialize Artifactory server
                    def server = Artifactory.newServer(
                        url: "${env.registry}/artifactory",
                        credentialsId: "artfiact-cred"
                    )

                    // Define properties for the artifacts
                    def properties = "buildid=${env.BUILD_ID},commitid=${env.GIT_COMMIT}"

                    // Define the upload specification
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging\\(*)",
                                "target": "libs-release-local/{1}",
                                "flat": "false",
                                "props" : "${properties}",
                                "exclusions": [ "*.sha1", "*.md5" ]
                            }
                        ]
                    }"""

                    // Upload the artifacts to Artifactory
                    def buildInfo = server.upload(uploadSpec)

                    // Collect environment variables related to the build
                    buildInfo.env.collect()

                    // Publish build information to Artifactory
                    server.publishBuildInfo(buildInfo)

                    echo '<--------------- Jar Publish Ended --------------->'
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
