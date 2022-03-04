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
       sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
      }
    }
    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
        sh 'mvn sonar:sonar'
        sh 'cat target/sonar/report-task.txt'
        }
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
          sh 'scp -r -i /home/ubuntu/devsecops.pem -o StrictHostKeyChecking=no target/*.war ubuntu@18.216.104.78:/prod/apache-tomcat-9.0.59/webapps/webapp.war'
        }
      }
    }
    
    stage ('DAST') {
      steps {
        sshagent (['zap']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@3.12.41.170 "docker run -t owasp.zap2docker-stable zap-baseline.py -t http://18.216.104.78:8080/webapp/" '
        }
      }
    }
    
  }
}
