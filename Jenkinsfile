pipeline {
    agent any

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
                        credentialsId: 'nexuslogin', 
                        groupId: 'com.example.maven-project', 
                        nexusUrl: '192.168.146.132:8081', 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: 'maven-project', 
                        version: '1.0-SNAPSHOT'
                
            }
            post {
                        success {
                          echo 'Artifacts deployed to Nexus.'
                        }

                        failure {
                          echo 'Artifacts failed to be deployed to Nexus.'
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
