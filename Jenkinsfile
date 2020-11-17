pipeline {
    environment {
        imageName = 'jjvdgeer/logitechmediaserver'
        registry = "http://qnap:5000/"
        dockerImage = ''
    }
    agent { label 'docker' }
    stages {
        stage('Clone sources') {
            steps {
                git url: 'https://github.com/jjvdgeer/docker-logitechmediaserver'
            }
        }
        stage('Building docker image') {
            steps {
                sh 'make update'
                sh 'make build'
                script {
                    env.TAG = """${sh(returnStdout: true, script: "cat lmsdeb.txt | sed 's/.*_\\([0-9\\.~]*\\)_all.deb/\\1/' | sed 's/~/-/'")}"""
                }
            }
        }
        stage('Upload image') {
            steps {
                script {
                    dockerImage = docker.image("$imageName:${env.TAG}")
                    docker.withRegistry("$registry") {
                        dockerImage.push()
                        dockerImage.push('$BUILD_NUMBER')
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $imageName:${env.TAG}"
            }
        }
    }
}
