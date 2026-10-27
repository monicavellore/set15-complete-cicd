pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\admin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        KUBECTL = 'C:\\Users\\admin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe'
        KUBECONFIG = 'C:\\Users\\admin\\.kube\\config'
        PROJECT_DIR = 'C:\\k8s-labs\\set15-cicd'
        IMAGE = 'yazhini01/set15-web:1.0'
    }

    stages {
        stage('Checkout Source') {
            steps {
                bat 'dir "%PROJECT_DIR%"'
                bat 'dir "%PROJECT_DIR%\\app"'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'cd /d "%PROJECT_DIR%" && "%DOCKER%" build -t %IMAGE% .'
            }
        }

        stage('Test Application') {
            steps {
                bat '"%DOCKER%" run --rm %IMAGE% nginx -t'
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Docker Hub push will be configured after checking credentials.'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" apply -f "%PROJECT_DIR%\\k8s\\deployment.yaml"'
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" rollout status deployment/set15-web -n lab-sets --timeout=180s'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" get deployments -n lab-sets'
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" get pods -n lab-sets'
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" get services -n lab-sets'
            }
        }
    }
}