pipeline {
    environment {
        registry = "brunolv/jenkins-pipeline"
        credentials = credentials('docker-hub')
    }
    agent {
        docker {
            image 'brunolv/docker-node'
            //args '-p 3000:3000'
            //args '-w /app'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage("Build"){
            steps {
                sh 'npm install'
            }
        }
        stage("Test"){
            steps {
                sh 'npm test'
            }
        }
        stage("Build & Push Docker image") {
            steps {
                sh "docker image build -t $registry:$BUILD_NUMBER ."
                sh "docker login -u $credentials_USR -p $credentials_PSW"
                sh "docker image push $registry:$BUILD_NUMBER"
                sh "docker image rm $registry:$BUILD_NUMBER"
            }
        }
        stage('Deploy and smoke test') {
            steps {
                bash './jenkins/scripts/deploy.sh'
            }
        }
        stage('Cleanup') {
            steps{
                bash './jenkins/scripts/cleanup.sh'
            }
        }
    }
}