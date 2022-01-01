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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
<<<<<<< HEAD
                        sh "cp **/target/*.war /opt/tomcat/webapps"
=======
                        sh "cp **/target/*.war /opt/tomcat/webapp"
>>>>>>> 31d3071dce9da5c9fe5fa0882ef3b2d84791241d
                    }
                }

                stage ("Deploy to Production"){
                    steps {
<<<<<<< HEAD
                        sh "cp **/target/*.war /opt/tomcat-prod/webapps"
=======
                        sh "cp **/target/*.war /opt/tomcat-prod/webapp"
>>>>>>> 31d3071dce9da5c9fe5fa0882ef3b2d84791241d
                    }
                }
            }
        }
    }
}