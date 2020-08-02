node {
    stage('Lint') {
        sh 'tidy -q -e *.html'
    }

    stage('Build') {
        docker.build('cloud_devops_capstone')
    }
    stage('Docker push') {
        docker.withRegistry('https://937431759388.dkr.ecr.us-east-2.amazonaws.com', 'ecr:us-east-2:Jenkins-docker') {
            docker.image('cloud_devops_capstone').push('latest')
        } 
    }
    stage('Create Service in Kubernetes') {
        withAWS(credentials:'eks_access', region:'us-east-2') {
            sh "aws eks --region us-east-2 update-kubeconfig --name capstone"
            sh "kubectl config use-context arn:aws:eks:us-east-2:937431759388:cluster/capstone"
            sh "kubectl apply -f controller.json"
            sh "kubectl apply -f service.json"
        }
    }
}
