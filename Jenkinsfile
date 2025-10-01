pipeline {
    agent any  // Cambiado - ya no necesitamos contenedor AWS CLI

    parameters {
        string(name: 'AWS_REGION', defaultValue: 'us-east-2')
        string(name: 'ECR_REPO', defaultValue: 'nginx-ecs-demo')
        string(name: 'ECS_CLUSTER', defaultValue: 'ecs-lab-cluster')
        string(name: 'ECS_SERVICE', defaultValue: 'nginx-lab-svc')
        string(name: 'TASK_FAMILY', defaultValue: 'nginx-lab-task')
        string(name: 'ACCOUNT_ID', defaultValue: '597619206075')
    }

    environment {
        ECR_URL = "${params.ACCOUNT_ID}.dkr.ecr.${params.AWS_REGION}.amazonaws.com"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Setup Tools') {
            steps {
                sh '''
                    # Instalar AWS CLI
                    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                    unzip awscliv2.zip
                    sudo ./aws/install
                    
                    # Instalar jq
                    sudo apt-get update && sudo apt-get install -y jq
                '''
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Push') {
            steps {
                script {
                    // Construir imagen Docker
                    sh "docker build -t ${ECR_URL}/${params.ECR_REPO}:${IMAGE_TAG} ."
                    
                    // Login a ECR y push
                    sh "aws ecr get-login-password --region ${params.AWS_REGION} | docker login --username AWS --password-stdin ${ECR_URL}"
                    sh "docker push ${ECR_URL}/${params.ECR_REPO}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Tu código de deploy ECS (el mismo)
                    def taskDef = sh(
                        script: "aws ecs describe-task-definition --task-definition ${params.TASK_FAMILY} --region ${params.AWS_REGION} --output json",
                        returnStdout: true
                    ).trim()

                    // ... (mantén todo el resto del código de deploy igual)
                    // El código de jq y actualización de task definition queda igual
                }
            }
        }
    }
}