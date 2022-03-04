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
          sh 'scp -r -i /home/ubuntu/devsecops.pem -o StrictHostKeyChecking=no target/*.war ubuntu@52.14.200.122:/prod/apache-tomcat-9.0.59/webapps/webapp.war'
        }
      }
    }
  }
}
