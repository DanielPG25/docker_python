pipeline {
    environment {
        IMAGEN = "dparrales/django_python"
        LOGIN = 'USER_DOCKERHUB'
    }
    agent none
    stages {
        stage("Desarrollo") {
            agent {
                docker { image "python:3"
                args '-u root:root'
                }
            }
            stages {
                stage('Clone') {
                    steps {
                        git branch:'master',url:'https://github.com/DanielPG25/django_tutorial.git'
                    }
                }
                stage('Install') {
                    steps {
                        sh 'pip install -r requirements.txt'
                    }
                }
                stage('Test')
                {
                    steps {
                        sh 'python3 manage.py test'
                    }
                }

            }
        }
        stage("Construccion") {
            agent any
            stages {
                stage('CloneAnfitrion') {
                    steps {
                        git branch:'master',url:'https://github.com/DanielPG25/docker_python.git'
                    }
                }
                stage('BuildImage') {
                    steps {
                        script {
                            newApp = docker.build "$IMAGEN:latest"
                        }
                    }
                }
                stage('UploadImage') {
                    steps {
                        script {
                            docker.withRegistry( '', LOGIN ) {
                                newApp.push()
                            }
                        }
                    }
                }
                stage('RemoveImage') {
                    steps {
                        sh "docker rmi $IMAGEN:latest"
                    }
                }
            }
        }           
    }
    post {
        always {
            mail to: 'daniparrales16@gmail.com',
            subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
            body: "${env.BUILD_URL} has result ${currentBuild.result}"
        }
    }
}
