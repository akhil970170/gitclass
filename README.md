pipeline {
    agent any

    environment{
      PATH="/opt/maven3/bin:$PATH"
    }
   stages {
        stage("Git Checkout") {
            steps {
                git credentialsId: 'myjava', url: 'https://github.com/akhil970170/gitclass.git'
            }
        }
        stage("Maven Build") {
            step {
            sh "mvn clean package"
            sh "mv target/*.war target/myweb.war"
            }
        }
         stage("deploy-dev"){
         steps{
            sshagent(['tomcat']) {
            sh """
                scp -o StrictHostKeyChecking=no target/myweb.war  ubuntu@172.31.7.85:/home/ubuntu/apache-tomcat-9.0.54/webapps/
                ssh ubuntu@172.31.7.85 /home/ubuntu/apache-tomcat-9.0.54/bin/shutdown.sh
                ssh ubuntu@172.31.7.85 /home/ubuntu/apache-tomcat-9.0.54/bin/startup.sh
                
                """
             }
           }
        }
    }
}
