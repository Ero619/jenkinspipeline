pipeline {
    agent any
    tools{
        maven 'localMaven'
    }
    parameters {
         string(name: 'tomcat_dev', defaultValue: '18.191.169.88', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.191.20.186', description: 'Production Server')
    }

    environment {
        tomcat_cert= '/Users/gussanchez/Downloads/tomcat-demo.pem'
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
                echo 'My user is:'
                sh 'whoami'
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
                        sh "scp -i ${tomcat_cert} **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat8/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i ${tomcat_cert} **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat8/webapps"
                    }
                }
            }
        }
    }
}