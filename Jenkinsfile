pipeline {
    agent any
    
        environment 
        {
         registry = "212153177317.dkr.ecr.us-west-1.amazonaws.com/myimagerepo" #aws ecr arn
        }            

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/heydevopsbatch5/cicdhelmeks.git']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    dockerImage = docker.build registry 
                    dockerImage.tag("$BUILD_NUMBER")
                  }
            }
        }

          stage ("Pushing to ECR") {
            steps {
                script {
                   sh "aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin 212153177317.dkr.ecr.us-west-1.amazonaws.com"
                   sh "docker push 212153177317.dkr.ecr.us-west-1.amazonaws.com/myimagerepo:$BUILD_NUMBER"

                  }
            }
        }
        
         stage('K8S Deploy') {
        steps{   
            script {
                withKubeConfig([credentialsId: 'kubeconfig-raw', serverUrl: '']) {
                sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
       }
}
}


