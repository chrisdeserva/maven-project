pipeline {
    agent any
    
    parameters {
        string(name: 'tomcat_dev', defaultValue: '35.177.46.72', description: 'Staging server')
        string(name: 'tomcat_prod', defaultValue: '35.176.195.231', description: 'Production server')
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Deployments'){
            parallel {
                stage('Deploy to staging'){
                    steps {
                        sh "scp -i /var/lib/jenkins/tomcat.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage('Deploy to production'){
                    steps {
                        sh "scp -i /var/lib/jenkins/tomcat.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }

        }    
    } 

}
