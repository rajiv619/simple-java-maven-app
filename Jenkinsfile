pipeline{
  agent {
    docker {
      image 'maven:3-alpine'
      args '-v /root/.m2:/root/.m2'
    }
  }
  stages {
    stage('Build'){
      steps{
        sh 'mvn -B -DskipTests clean package'
      }
     }
    stage('Test'){
      steps{
        sh 'mvn test'
      }
      }
  }
  tage("Build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('simplemaven') {
                 sh 'java -version'
                 sh 'mvn clean package sonar:sonar'
              }
            }
          }
       stage("Quality gate") {
            steps {
               waitForQualityGate abortPipeline: true
            }
        }
   
     stage('Deploy to artifactory'){
        steps{
        rtUpload(
         serverId : 'Art-server',
         spec :'''{
           "files" :[
           {
           "pattern":"target/*.jar",
           "target":"Art-simplemaven-2021"
           }
           ]
         }''',
         
      )
      }
     }
	 
	 post {  
         always {  
             echo 'This will always run'  
         }  
         success {   
            echo "========Deploying executed successfully========"
            emailext attachLog: true, body: "<b>Example</b><br>Project: ${env.JOB_NAME}", from: 'workemails619@gmail.com', mimeType: 'text/html', replyTo: '', subject: "Deploy Success CI: Project name -> ${env.JOB_NAME}", to: "workemails619@gmail.com";
         }  
         failure {  
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'workemails619@gmail.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "workemails619@gmail.com";  
         }  
         unstable {  
             echo 'This will run only if the run was marked as unstable'  
         }  
         changed {  
             echo 'This will run only if the state of the Pipeline has changed'  
             echo 'For example, if the Pipeline was previously failing but is now successful'  
         }  
     }
}
