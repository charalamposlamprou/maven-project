pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: 'localhost:8090', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'localhost:9090', description: 'Production Server')
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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /var/lib/jenkins/.ssh/id_rsa **/target/*.war root@${params.tomcat_dev}:/opt/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /var/lib/jenkins/.ssh/id_rsa **/target/*.war root@${params.tomcat_prod}:/opt/webapps"
                    }
                }
            }
        }
    }
}