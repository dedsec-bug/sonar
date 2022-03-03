pipeline{
  agent any
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
           '''        
      }
    }
    
    stage ('Build') {
      steps {
        sh 'mvn clean package'
    }
    }
    
    stage ('Deploy-To-Tomcat') {
      steps {
        sshagent(['tomcat']) {
          sh 'scp -i /home/ubuntu/devsecops.pem -o StrictHostKeyChecking=no target/*.war ubuntu@3.135.208.140:/prod/apache-tomcat-9.0.59/webapps/webapp.war'
        }
      }
    }
  }
}
