pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'Java'
    }

    environment {
        NEXUS_CREDENTIALS = credentials('nexuscred')
    }
    stages{
        stage('Build'){
            steps{
                 sh script: 'mvn clean package'
                 archiveArtifacts artifacts: 'target/*.war', onlyIfSuccessful: true
            }
        }
        stage('Upload War To Nexus'){
            steps{
                script{

                    def mavenPom = readMavenPom file: 'pom.xml'
                    def nexusRepoName = mavenPom.version.endsWith("RELEASE") ? "Nexus-Integrate-snapshot" : "Nexus-Integrate-release"
                     def warFileName = sh(
                        script: "ls target/*.war | xargs -n 1 basename",
                        returnStdout: true
                    ).trim()
                    nexusArtifactUploader artifacts: [
                        
                         [
                            artifactId: 'web-application', 
                            classifier: '', 
                            file: "target/simple-app-${mavenPom.version}.war", 
                            type: 'war'
                        ]
                    ], 
                    credentialsId: 'nexuscred', 
                    groupId: 'com.techworldwithmurali', 
                    nexusUrl: '65.1.95.151:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepoName, 
                    version: "${mavenPom.version}"
                    }
            }
        }
    }
}
