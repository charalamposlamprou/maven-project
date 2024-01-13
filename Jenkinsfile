pipeline {
    agent any

     environment {
        NEXUS_CREDENTIALS = credentials('nexuslogin')
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
                            file: 'target/webapp-1.0-SNAPSHOT.war', 
                            type: 'war'
                        ]
                    ], 
                        credentialsId: 'nexuslogin', 
                        groupId: 'com.example.maven-project', 
                        nexusUrl: 'http://172.16.240.128:8083/', 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: 'http://172.16.240.128:8083/repository/maven-project', 
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
