pipeline {
    agent {
        node {
            label 'master'
        }
    }
    options {
        ansiColor('xterm')
    }
    environment {
        KUBERNETES_API = 'https://proxtest1:6443'
        STOCKS_APP_NAMESPACE = 'stocks-app'
    }
    stages {
        stage('DeployQual'){
            steps {
                echo "Deploying the stocks app to the qual environment"
                withKubeConfig([credentialsId: 'local_kube_stocksapp', serverUrl: "${env.KUBERNETES_API}", namespace: "${env.STOCKS_APP_NAMESPACE}"]) {
                    sh 'kubectl apply -f stocks-db.yaml'
                    sh 'kubectl apply -f stocks-apiclient.yaml'
                    sh 'kubectl apply -f stocks-frontend.yaml'
                    sh 'kubectl apply -f stocks-competitors.yaml'
                    sh 'kubectl apply -f stocks-import.yaml'
                    sh 'kubectl apply -f stocks-autoupdate.yaml'
                }
            }
        }
    }
}