pipeline {
    agent any

     environment {
        NEXUS_CREDENTIALS = credentials('nexuslogin')
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
                nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: NEXUS_REPO_URL,
        groupId: pom.groupId,
        version: pom.version,
        repository: 'maven-project',
        credentialsId: NEXUS_CREDENTIALS,
        artifacts: [
            [artifactId: maven-project,
             classifier: '',
             file: 'webapp' + version + '.war',
             type: 'war']
        ]
     )
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
