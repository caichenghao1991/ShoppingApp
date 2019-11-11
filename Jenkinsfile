pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "maven"
      jdk "java8"
    
   }
   
   triggers {
       pollSCM('* * * * *')
   }
   parameters{ 
      string(defaultValue: "B11", 
             description: 'Enter the branch name to be used for this build:', name: 'BRANCH_NAME')
     string(defaultValue: "payalbnsl_ShoppingApp", 
             description: 'Enter the projectKey for sonar analysis:', name: 'projectKey')
      string(defaultValue: "payalbnsl-github", 
             description: 'Enter the projectKey for sonar analysis:', name: 'organization')
     string(defaultValue: "https://sonarcloud.io", 
             description: 'Enter the url for sonar analysis:', name: 'url')
     string(defaultValue: "879e860cbdec39587da8e729bf50dd823518dfcf", 
             description: 'Enter the login for sonar analysis:', name: 'login')
      
   }
     options { 
         buildDiscarder(logRotator(numToKeepStr: '2')) 
    }
   stages {
       
       stage('git checkout'){
            steps{
               checkout([$class: 'GitSCM', branches: [[name: '*/B11']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/payalbnsl/ShoppingApp']]])
           }
       }
      stage('Build') {
         steps {
            // Get some code from a GitHub repository
        //    git 'https://github.com/payalbnsl/ShoppingApp.git'
        
            // Run Maven on a Unix agent.
            sh "mvn -Dmaven.test.failure.ignore=true clean package"

            // To run Maven on a Windows agent, use
            // bat "mvn -Dmaven.test.failure.ignore=true clean package"
         }

         post {
            // If Maven was able to run the tests, even if some of the test
            // failed, record the test results and archive the jar file.
            success {
               junit '**/target/surefire-reports/TEST-*.xml'
               archiveArtifacts 'target/*.jar'
            }
         }
      }
      stage('test-coverage'){
          steps{
           jacoco execPattern: 'target/**.exec', sourceExclusionPattern: '**/src/test/java'
          }
      }
    
   stage('SonarCloud'){
     
          steps{
              
         
              sh "mvn -DskipTests sonar:sonar -Dsonar.projectKey=${params.projectKey} -Dsonar.organization=${params.organization} -Dsonar.host.url=${params.url} -Dsonar.login=${params.login}" 
          
     
                
    }
              
          
            post {
          always {
            step([$class: 'Mailer',
                notifyEveryUnstableBuild: true,
                recipients: "payal@rjtcompuquest.com",
                sendToIndividuals: true])
        }
        
      }
      
         
      }

  
    
    
    
   }
   
}
