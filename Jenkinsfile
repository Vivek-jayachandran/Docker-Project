pipeline {

  environment {
    registry = "docker.io/vivekdevopsfree/flask"
    registry_mysql = "docker.io/vivekdevopsfree/mysql"
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

    stage('Push Image') {
    steps {
        script {
             withDockerRegistry([ credentialsId: "vivekjenkins1", url: ""]) {
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
         withDockerRegistry([ credentialsId: "vivekjenkins1", url: "" ]) {
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
