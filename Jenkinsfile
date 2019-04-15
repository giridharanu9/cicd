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
        sh 'rm -rf .terraform/'
        sh 'terraform init -input=false'
        sh "terraform plan -input=false -out tfplan"
        sh 'terraform show -no-color tfplan > tfplan.txt'
        sh 'terraform apply -input=false tfplan'
        sh 'terraform apply --target aws_ecr_repository.myapp'
    }
 }
 
stage('Docker Build & Push')
{
    dir ('docker/myapp') {
        sh '$(aws ecr get-login --region ap-southeast-1 --no-include-email)'
        sh 'docker build -t myapp .'
        sh 'docker tag myapp $(terraform output myapp-repo):latest'
        sh 'docker push $(terraform output myapp-repo):latest'
        }
    }
 }
 
 stage('Deploy Application') {
    dir('terraform') {
        sh 'terraform apply'
    }
 }
 /*
 stage('Destroy') {
    dir('terraform') {
        sh 'terraform destroy -force'
    }
 }
 */
}
