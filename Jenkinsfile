@Library('your-shared-library') _

import org.jenkinsci.plugins.nexus.pipeline.NexusArtifactUploader

pipeline {
    agent any

     environment {
        NEXUS_CREDENTIALS = credentials('c165e35e-4b2c-4e0a-874f-5004d5750e8e')
        NEXUS_REPO_URL = 'http://172.16.240.128:8083/repository/maven-project/'
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

    stage('Deploy to Nexus') {
            steps {
                script {
                    def nexusArtifactUploader = NexusArtifactUploader.fromMaven()
                    nexusArtifactUploader.credentialsId = NEXUS_CREDENTIALS
                    nexusArtifactUploader.repositoryUrl = NEXUS_REPO_URL
                    nexusArtifactUploader.artifacts = [
                        // Specify the file(s) to upload
                        [artifactId: 'your-artifact-id', classifier: '', file: '/var/lib/jenkins/workspace/maven-project/webapp/target/webapp.war']
                    ]

                    // Perform the upload
                    nexusArtifactUploader.deploy()
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "sudo cp **/target/*.war /opt/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        timeout(time:5, unit:'DAYS'){
                        input message:'Approve PRODUCTION Deployment?'
                        }

                        sh "sudo cp **/target/*.war /opt/tomcat-prod/webapps"
                    }
                    post {
                        success {
                          echo 'Code deployed to Production.'
                        }

                        failure {
                          echo ' Deployment failed.'
                        }
                    }
                }
            }
        }
    }
}
