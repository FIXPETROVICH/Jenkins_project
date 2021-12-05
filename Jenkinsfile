pipeline {
    agent {
        node {
            label "Slave_1"
        }
    }
    stages {
        stage('Clone repository') {
            steps {
                git url: 'git@github.com:Fixpetrovich/project-fix.git',
                credentialsId: "jenkins"
            }
        }
        stage('Kubeval test') {
            steps {
                sh """
                kubeval manifests/deploy.yaml -o json
                kubeval manifests/pv.yaml -o json
		kubeval manifests/pv2.yaml -o json
	        kubeval manifests/ingress.yaml -o json
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
