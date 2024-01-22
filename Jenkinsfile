pipeline {

  environment {
    registry = "vivekdevopsfree/flask"
    registry_mysql = "vivekdevopsfree/mysql"
    dockerImage = ""
  }

  agent any
    stages {
  
    stage('Checkout Source') {
      steps {
        git 'https://github.com/Vivek-jayachandran/Docker-Project'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
      
      stage('Test Credentials') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'vivekjenkins', variable: 'MY_SECRET')]) {
                        echo "Credentials: $MY_SECRET"
                    }
                }
            }
        }

    stage('Push Image') {
    steps {
        script {
             withDockerRegistry([ credentialsId: "vivekjenkins", url: ""]) {
               //echo "Credentials found. Pushing Docker image..."
               //sh "echo 'Credentials: $MY_SECRET'"
               dockerImage.push()    
                  
            }
        }
    }
}

    stage('current') {
      steps{
        dir("${env.WORKSPACE}/mysql"){
          sh "pwd"
          }
      }
   }
   stage('Build mysql image') {
     steps{
         withDockerRegistry([ credentialsId: "vivekjenkins", url: "" ]) {
       sh 'docker build -t "vivekdevopsfree/mysql:$BUILD_NUMBER"  "$WORKSPACE"/mysql'
        
        sh 'docker push "vivekdevopsfree/mysql:$BUILD_NUMBER"'
        }
      }
   }
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "frontend.yaml", kubeconfigId: "kube1")
        }
      }
    }

  }

}
