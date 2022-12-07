pipeline 
{
    agent any
    stages 
    {
        stage('Git Clone'){
            steps
            {
                script
                {
                    git branch: 'main',
                    git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'git@github.com:harrisonmlima/kube-news-kubernetes.git'
                }
            }
        }
        stage ( 'Build Docker Image')
        {
            steps
            {
                script
                {
                    dockerapp = docker.build("harrisonlima/kube-news:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage ('Push Docker Image')
        {
            steps
            {
                script
                {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub')
                    {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage ('Deploy Kubernetes')
        {
            environment
            {
                tag_version = "${env.BUILD_ID}"
            }
            steps
            {
                
                    sh 'sed -i "s/{{TAG}}/$tag_version/g" ./src/k8s/api/deployment.yaml'
                    sh 'kubectl apply -f ./src/k8s -R'
                
            }
        }
    }
}