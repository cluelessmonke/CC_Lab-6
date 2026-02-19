pipeline {
    agent any

    stages {

        stage('Build Backend') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 nginx-lb || true
                docker network create labnet || true

                docker run -d --network labnet --name backend1 backend-app
                docker run -d --network labnet --name backend2 backend-app
                '''
            }
        }

        stage('Start Nginx') {
            steps {
                sh '''
                docker run -d --network labnet -p 80:80 --name nginx-lb nginx
                sleep 5
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}
pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true
                docker run -d --name backend1 backend-app
                docker run -d --name backend2 backend-app
                '''
            }
        }
    }
}
