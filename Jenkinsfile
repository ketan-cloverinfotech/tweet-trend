pipeline {
    agent any

    environment {
        PATH = "/opt/apache-maven-3.9.2/bin:$PATH"
        // Ensure that the following environment variables are defined in your Jenkins environment or injected appropriately:
        // - registry
        // - GIT_COMMIT
    }

    stages {
        stage("Build") {
            steps {
                echo "----------- Build Started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build Completed ----------"
            }
        }

        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                    
                    // Initialize Artifactory server
                    def server = Artifactory.newServer(
                        url: "${registry}/artifactory",
                        credentialsId: "artfiact-cred"
                    )
                    
                    // Define properties for the artifacts
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                    
                    // Define the upload specification
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/(*)",
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
