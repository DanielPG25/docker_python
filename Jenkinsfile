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
                        git branch:'main',url:'https://github.com/DanielPG25/docker_python.git'
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
                stage ('SSH') {
    steps{
        sshagent(credentials : ['SSH_ROOT']) {
            sh 'ssh -o StrictHostKeyChecking=no root@blackstar.sysadblog.com wget https://raw.githubusercontent.com/DanielPG25/docker_python/main/docker-compose.yaml -O docker-compose.yaml'
            sh 'ssh -o StrictHostKeyChecking=no root@blackstar.sysadblog.com docker-compose up -d --force-recreate'
        }
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
