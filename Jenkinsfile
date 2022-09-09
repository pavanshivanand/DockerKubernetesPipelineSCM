def containerName="dockerkubernetespipelinescm"
def tag="latest"
def dockerHubUser="pavanshivanand"
def httpPort="8090"

node {

    stage('Checkout') {
        checkout scm
    }

    stage('Build'){
        sh "mvn clean install"
    }

    stage("Image Prune"){
         sh "docker image prune -a -f"
    }

    stage('Image Build'){
        sh "docker build -t $containerName:$tag  -t $containerName --pull --no-cache ."
        echo "Image build complete"
    }

    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            sh "docker login -u $USERNAME -p $PASSWORD"
            sh "docker tag $containerName:$tag $USERNAME/$containerName:$tag"
            sh "docker push $USERNAME/$containerName:$tag"
            echo "Image push complete"
        }
    }

    stage('Run App'){
        /*sh "docker rm $containerName -f"
        sh "docker pull $dockerHubUser/$containerName"
        sh "docker run -d --rm -p $httpPort:$httpPort --name $containerName $dockerHubUser/$containerName:$tag"
        echo "Application started on port: ${httpPort} (http)"
        */
        kubeconfig(credentialsId: 'kubectl', serverUrl: '') {
           sh "kubectl version"
           sh "kubectl get pods"
           sh "kubectl delete deployment dockerkubernetespipelinescm | true"
           sh "kubectl create deployment dockerkubernetespipelinescm --image=docker.io/pavanshivanand/dockerkubernetespipelinescm"
           sh "kubectl expose deployment dockerkubernetespipelinescm --type=NodePort --port=8090"
           sh "kubectl get pods"
           }
    }

}
