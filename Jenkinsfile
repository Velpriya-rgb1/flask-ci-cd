pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/your-username/flask-ci-cd.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install flask'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'python -m unittest test_app.py'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker tag flask-app your-dockerhub-username/flask-app:latest'
                    sh 'docker push your-dockerhub-username/flask-app:latest'
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@your-ec2-ip '
                    docker pull your-dockerhub-username/flask-app:latest &&
                    docker run -d -p 5000:5000 your-dockerhub-username/flask-app:latest
                    '
                    '''
                }
            }
        }
    }
}
