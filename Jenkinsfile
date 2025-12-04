pipeline{
    agent any
     environment {
        DOCKER_CREDS = credentials('dockerhub-cred')
     }
     stages{
        stage('GIT URL'){
            steps{
              git  branch: 'main',
                url: 'https://github.com/Naresh1770/Flask-demo.git'
            }
        }
        stage('Docker Build'){
            steps{
              sh 'docker build -t flask-app:v1 .'
            }
        }
        stage('Docker login & push'){
            steps{
              sh '''
                    echo "$DOCKER_CREDS_PSW" | docker login -u "$DOCKER_CREDS_USR" --password-stdin
                    docker tag flask-app:v1 $DOCKER_CREDS_USR/flask-app:v1
                    docker push $DOCKER_CREDS_USR/flask-app:v1
                '''
            }
        }
        stage('Deploy to EC2 '){
            steps{
                sshagent (['ec2-key']){
              sh '''
              ssh -o StrictHostKeyChecking=no ubuntu@16.171.41.203 \
              'docker pull $DOCKER_CREDS_USR/flask-app:v1 &&
              docker stop flask-app || true &&
              docker rm flask-app || true &&
              docker run -d -p 5000:5000 --name flask-app $DOCKER_CREDS_USR/flask-app:v1' 
              '''
           }
            }
     }
}
}