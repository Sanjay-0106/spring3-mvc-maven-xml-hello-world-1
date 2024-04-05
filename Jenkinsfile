pipeline {
    agent any 
    tools {
        maven "maven"
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "54.159.62.103:8081"
        NEXUS_REPOSITORY = "nexus"
        NEXUS_CREDENTIAL_ID = "nexus-1"
    }
    stages {
        stage("clone code") {
            steps {
                script {
                    git 'https://github.com/betawins/spring3-mvc-maven-xml-hello-world-1.git';
                }
            }
        }
        stage("mvn build") {
            steps {
                script {
                    sh 'mvn -Dmaven.test.failure.ignore=true install'
                }
            }
        }
        stage("publish to nexus") {
            steps {
                script {
                    def pom = readMavenPom file: "pom.xml"
                    def filesByGlob = findFiles(glob: "target/*.${pom.packaging}")
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    def artifactPath = filesByGlob[0].path
                    def artifactExists = fileExists artifactPath
                    if (artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${env.BUILD_NUMBER}"
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: "${env.BUILD_NUMBER}",
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        )
                    } else {
                        error "*** File: ${artifactPath}, could not be found"
                    }
                }
            }
        }
    }
}
