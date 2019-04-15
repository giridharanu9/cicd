node {
 stage('checkout') {
     git branch: 'master', url: 'https://github.com/giridharanu9/cicd.git'
 }

withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-credentials', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) { 
 stage('Set Terraform path') {
    def tfHome = tool name: 'terraform'
    env.PATH = "${tfHome}:${env.PATH}"
 }
 
 stage('Provision infrastructure') {
    dir('terraform') {
        sh 'terraform init'
        sh 'terraform apply --target aws_ecr_repository.myapp'
        sh 'export REPO_URL=$(terraform output myapp-repo)'
        sh '$(aws ecr get-login --region us-east-1 --no-include-email)'
    }
 }
 
 stage('Docker Build & Push')
 
 {
    dir ('docker/myapp') {
        sh 'docker build -t myapp .'
        sh 'docker tag myapp ${REPO_URL}:latest'
        sh 'docker push ${REPO_URL}:latest'
    }
 }
 
 stage('Deploy Application') {
    sh 'terraform apply'
 }
}
}
