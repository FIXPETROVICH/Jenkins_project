pipeline {
    agent {
        node {
            label "Slave_1"
        }
    }
    stages {
        stage('Clone repository') {
            steps {
                git url: 'https://github.com/FIXPETROVICH/Jenkins_project.git',
                credentialsId: "github2"
            }
        }
        stage('Kubeval test') {
            steps {
                sh """
                kubeval manifests/deploy.yaml -o stdout
                kubeval manifests/pv.yaml -o stdout
		kubeval manifests/pv2.yaml -o stdout
	        kubeval manifests/ingress.yaml -o stdout
                """
            }
        }
        stage('Deploy') {
            steps {
                sh """
                cd manifests
                kubectl apply -f pv.yaml
                kubectl apply -f pvc.yaml
                kubectl apply -f deploy.yaml
                kubectl apply -f ingress.yaml
                """
            }
        }
    }    
    post {
        success {
                slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
            failure {
                slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        always {
            deleteDir()
        }
    }
}
