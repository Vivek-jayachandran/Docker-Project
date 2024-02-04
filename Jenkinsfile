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
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Build image1') {
            steps {
                script {
                    dockerImage = docker.build registry_mysql + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Test Credentials') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'vivekjenkins', variable: 'DOCKERHUB_TOKEN')]) {
                        def response = sh(script: 'docker login -u vivekdevopsfree -p $DOCKERHUB_TOKEN', returnStatus: true)
                        
                        if (response == 0) {
                            echo "DockerHub login successful"
                        } else {
                            error "DockerHub login failed"
                        }
                    }
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    sh 'docker push "vivekdevopsfree/flask:$BUILD_NUMBER"'
                }
            }
        }

        stage('Push sqlImage') {
            steps {
                script {
                    sh 'docker push "vivekdevopsfree/mysql:$BUILD_NUMBER"'
                }
            }
        }

    //stage('Deploy App') {
      //steps {
        //script {
          //kubernetesDeploy(configs: "frontend.yaml", kubeconfigId: "kube2")
        //}
      //}
    //}
        //stage('Deploy App') {
            //steps {
                //script {
                    // Assuming you have a Kubernetes configuration file (kubeconfig)
                   //def kubeconfig = credentials('kube1')  // Replace 'kubeconfig-id' with your actual credential ID
                    //echo "Kubeconfig: ${kubeconfig}"

                    // Apply the Kubernetes manifests
                    //sh "kubectl --kubeconfig=${kubeconfig} apply -f frontend.yaml"
                //}
           //}
        //}
     
   
        stage('Deploy App') {
            steps {
                script {
                    // Retrieve the kubeconfig from the credentials
                    def kubeconfigSecret = credentials('kube1')  // Replace 'kube1' with your actual credential ID
                    def kubeconfigPath = writeFile(file: 'kubeconfig', text: kubeconfigSecret).trim()
                    echo "Kubeconfig Path: ${kubeconfigPath}"

                    // Check if kubeconfigPath is not null
                    if (kubeconfigPath != '') {
                        // Apply the Kubernetes manifests
                        sh "kubectl apply --kubeconfig=${kubeconfigPath} -f frontend.yaml"
                    } else {
                        error "Failed to retrieve kubeconfig credentials"
                    }
                }
            }
        }
    

    }
}

