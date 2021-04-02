pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "<YOUR_USERNAME>/train-schedule"
        DOCKER_IMAGE_TAG = 'latest'
        DOCKER_REGISTRY = 'https://registry.hub.docker.com'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {

            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {

            steps {
                script {
                    docker.withRegistry(DOCKER_REGISTRY, 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {

            steps {

                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'myapp.yaml',
                    enableConfigSubstitution: true
                )

            }
        }
    }
}
