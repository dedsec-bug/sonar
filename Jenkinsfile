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
    
    stage ('Source Composition Analysis') {
      steps {
       sh 'rm owasp* || true'
       sh 'wget https://raw.githubusercontent.com/dedsec-bug/sonar/master/owasp-dependency-check.sh'
       sh 'chmod +x owasp-dependency-check.sh'
       sh 'bash owasp-dependency-check.sh'
       sh 'cat /var/lib/jenkins/OWASP-Check/reports/dependency-check-report.xml'
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
