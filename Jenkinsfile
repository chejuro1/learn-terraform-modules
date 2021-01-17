pipeline {
    agent { node { label 'node' } }

    parameters {
        string(name: 'environment', defaultValue: 'default', description: 'Workspace/environment file to use for deployment')
       // string(name: 'version', defaultValue: '', description: 'Version variable to pass to Terraform')
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    }
    
    environment {
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-key-id')
        TF_IN_AUTOMATION      = '1'
    }

    stages {
        stage('Plan') {
            steps {
                script {
                    currentBuild.displayName = params.version
                }
                sh 'whoami'
                sh 'ls'
                 
               
                //sh 'terraform workspace new ${environment}'
                sh 'terraform init -input=false '
                sh 'terraform workspace list '
                sh 'terraform workspace select ${environment}'
                //sh 'terraform workspace delete terraform'
              
                
                sh 'export AWS_ACCESS_KEY_ID="anaccesskey"'
                sh 'export AWS_SECRET_ACCESS_KEY="asecretkey"'
                sh 'export AWS_DEFAULT_REGION="us-east-2"'
                //sh "terraform plan -input=false -out tfplan -var  --var-file=${params.environment}.tfvars"
                
                sh 'terraform plan -out tfplan '
                
                sh 'terraform show -no-color tfplan > tfplan.txt'
                //'version=${params.version}'
            }
        }

        stage('Approval') {
            when {
                not {
                    equals expected: true, actual: params.autoApprove
                }
            }

            steps {
                script {
                    def plan = readFile 'tfplan.txt'
                    input message: "Do you want to apply the plan?",
                        parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }

        stage('Apply') {
            steps {
                sh 'terraform destroy --auto-approve  '
               // sh "terraform apply -input=false tfplan"
               
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'tfplan.txt'
        }
    }
}
