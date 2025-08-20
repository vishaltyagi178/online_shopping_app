pipeline{
    agent any;
    stages{
        stage("Cloning code"){
            steps{
                echo "code clone ho gya "
                git url: "https://github.com/vishaltyagi178/online_shopping_app.git", branch: "master"
            }
        }
        stage("Build docker image from cloned code"){
         steps{
               echo "code build ho gya " 
               sh "docker build -t online-shop ."
            }   
        }
        
        stage("Push image to ACR"){
          steps{
               withCredentials([usernamePassword(
                   credentialsId: "acrcred",
                   passwordVariable: "acrPass",
                   usernameVariable: "acrUser"
                   )]){
                sh "docker login jenkins1.azurecr.io -u ${env.acrUser} -p ${env.acrPass}"
                sh "docker image tag online-shop jenkins1.azurecr.io/online-shop"
                sh "docker push jenkins1.azurecr.io/online-shop:latest"
           }
          }
        }
        stage("fetch yamls from git hub"){
          steps{
              git branch: "dev",
              url: "https://github.com/vishaltyagi178/online_shopping_app.git",
               credentialsId: "gitHubPat"
          }
        }
      
        stage("Deploy container to k8s"){
          steps{
              withKubeConfig([credentialsId:"K8s"]){
                echo "adding deployment to cluster"
                echo "Applying namespace"
                sh "kubectl apply -f kubernetes/online-shop/k8s/namespace.yml"
                sh "kubectl apply -f kubernetes/online-shop/k8s/deployment.yml"
                sh "kubectl apply -f kubernetes/online-shop/k8s/service.yml"
                
            }  
          }
        }
    }
}
