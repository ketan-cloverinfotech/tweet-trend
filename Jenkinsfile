pipeline {
    agent any

    environment {
        // Update the Maven path to your actual Maven installation directory on Windows
        MAVEN_HOME = "C:\\Program Files\\Apache Maven\\apache-maven-3.9.2"
        PATH = "${MAVEN_HOME}\\bin;%PATH%"
        
        // Ensure these environment variables are defined appropriately
        // For example, you can define 'registry' and 'GIT_COMMIT' here or inject them as needed
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
