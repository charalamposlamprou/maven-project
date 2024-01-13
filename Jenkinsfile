pipeline {
    agent any

     environment {
        NEXUS_CREDENTIALS = credentials('nexuslogin')
        NEXUS_REPO_URL = '172.16.240.128:8083' 
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
                
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: 'maven-project', 
                            classifier: '', 
                            file: '/var/lib/jenkins/workspace/maven-project/webapp/target/webapp.war', 
                            type: 'war'
                        ]
                    ], 
                        credentialsId: NEXUS_CREDENTIALS, 
                        groupId: 'com.example.maven-project', 
                        nexusUrl: NEXUS_REPO_URL, 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: 'maven-project', 
                        version: '1.0-SNAPSHOT'
                
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
