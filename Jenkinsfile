pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:kalash'
       appRegistry = "434954100083.dkr.ecr.ap-south-1.amazonaws.com/kalash"
       capstoneRegistry = "https://434954100083.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "kalash"
        service = "kalash"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Kalash2002/kalash'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'kalash', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
