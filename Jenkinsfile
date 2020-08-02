pipeline {
    agent any
    stages {
        stage('Lint') {
            steps {
                sh 'tidy -q -e *.html'
            }
        }

        stage('Build') {
            steps{
                docker.build('cloud_devops_capstone')
            }
        }
        stage('Docker push') {
            steps {
                docker.withRegistry('https://937431759388.dkr.ecr.us-east-2.amazonaws.com', 'ecr:us-east-2:Jenkins-docker') {
                    docker.image('cloud_devops_capstone').push('latest')
                }
            }   
        }
        stage('Create Service in Kubernetes') {
            steps {
                withAWS(credentials:'eks_access', region:'us-east-2') {
                    sh "aws eks --region us-east-2 update-kubeconfig --name capstone"
                    sh "kubectl config use-context arn:aws:eks:us-east-2:937431759388:cluster/capstone"
                    sh "kubectl apply -f controller.json"
                    sh "kubectl apply -f service.json"
                }
            }
        }
    }
}
