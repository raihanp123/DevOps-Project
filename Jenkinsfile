pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    environment {
        DB_PW=credentials('DB_PW')
        DB_URI=credentials('DB_URI')
        SEC_KEY=credentials('SEC_KEY')
        USERNAME=credentials('DockerHub_user')
        PASSWORD=credentials('DockerHub_pass')
    }
    stages {
        stage('Build') {
            steps {
                sh "cd DevOps-Project && docker-compose build"
            }
        }
        
        stage('Test') {
            steps {
                sh "cd DevOps-Project/frontend && pip3 install -r requirements.txt && python3 -m pytest --cov application/ --cov-report html"
                sh "cd DevOps-Project/backend && pip3 install -r requirements.txt && python3 -m pytest --cov application/ --cov-report html"
            }
        }
        
        stage('Push') {
            steps {
                sh 'cd DevOps-Project && docker login -u ${USERNAME} -p ${PASSWORD} && docker-compose push'
            }
        }
        
        stage('Deploy') {
            steps {
                sh "cd DevOps-Project && docker stack deploy --compose-file docker-compose.yaml project-stack"
            }
        
        }
    }
}
