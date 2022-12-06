


pipeline{
    environment {
        PROD_BRANCH = 'master'
        STAGING_BRANCH = 'staging'
        user_env_input = 'Development'
    }
    
    

    agent any
    tools { 
       gradle'gradle7' 
       
    } 
   
    stages {
        stage('Which environment to build?') {
            steps {
            sh 'echo which environment to build'       
            }
        }
        stage('Confirm') {
            steps {
                script {
             
                        input("Do you want to proceed building in ${user_env_input} environment?")
                    }
                }
            
        }
        stage('Clean Build') {
            steps {
                sh 'gradle clean build'
                echo 'Building..'
            }
        }
        
        stage('Check Unit Tests') {
            steps {
                sh './gradlew test'
                echo 'Building..'
            }
        }
        
        stage('Sonar qube') {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube-server', credentialsId: 'sonarqube-secret-token') {
                    

                     sh './gradlew sonarqube \
  -Dsonar.projectKey=test \
  -Dsonar.host.url=http://localhost:9000 \
-Dsonar.login=admin \
 -Dsonar.password=password '

                    
                }
            }
        }
        
        stage('Code Coverage with jaccoco'){
            steps{   
        jacoco()
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 300, unit: 'SECONDS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

}
