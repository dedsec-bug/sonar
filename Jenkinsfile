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
    
    stage ('Check-Git-Secrets') {
      steps {
        sh '''        
                    rm trufflehog || true
                    docker run dxa4481/trufflehog --json https://github.com/dedsec-bug/sonar.git > trufflehog'
                    cat trufflehog || true
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
          sh 'scp -r -i /home/ubuntu/devsecops.pem -o StrictHostKeyChecking=no target/*.war ubuntu@3.135.208.140:/prod/apache-tomcat-9.0.59/webapps/webapp.war'
        }
      }
    }
  }
}
