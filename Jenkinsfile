pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        ARTIFACTORY_URL = "https://trialmjjh4j.jfrog.io/artifactory"
    }

    stages {
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

                    def server = Artifactory.server('artifactory-server')

                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "target/*.jar",
                                "target": "libs-release-local/com/valaxy/demo-workshop/2.1.4/",
                                "props": "build.id=${env.BUILD_ID};build.number=${env.BUILD_NUMBER}",
                                "exclusions": [ "*.sha1", "*.md5" ]
                            }
                        ]
                    }"""

                    echo "Upload Spec: ${uploadSpec}"

                    def buildInfo = server.upload spec: uploadSpec, failNoOp: true
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
