pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub_credentials') // Jenkins中配置的凭证ID
        DOCKER_IMAGE = 'yourusername/teedy-app' // 替换为你的Docker Hub仓库
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [[url: 'https://github.com/sismics/docs.git']]
                )
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${env.DOCKER_IMAGE}:${env.DOCKER_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_HUB_CREDENTIALS') {
                        docker.image("${env.DOCKER_IMAGE}:${env.DOCKER_TAG}").push()
                        docker.image("${env.DOCKER_IMAGE}:${env.DOCKER_TAG}").push('latest')
                    }
                }
            }
        }

        stage('Run Containers') {
            steps {
                script {
                    // 停止并删除可能存在的旧容器
                    sh 'docker stop teedy-8082 || true'
                    sh 'docker rm teedy-8082 || true'
                    sh 'docker stop teedy-8083 || true'
                    sh 'docker rm teedy-8083 || true'
                    sh 'docker stop teedy-8084 || true'
                    sh 'docker rm teedy-8084 || true'

                    // 运行三个容器
                    docker.image("${env.DOCKER_IMAGE}:${env.DOCKER_TAG}").run(
                        '--name teedy-8082 -d -p 8082:8080'
                    )
                    docker.image("${env.DOCKER_IMAGE}:${env.DOCKER_TAG}").run(
                        '--name teedy-8083 -d -p 8083:8080'
                    )
                    docker.image("${env.DOCKER_IMAGE}:${env.DOCKER_TAG}").run(
                        '--name teedy-8084 -d -p 8084:8080'
                    )

                    // 列出所有teedy容器
                    sh 'docker ps --filter "name=teedy-"'
                }
            }
        }
    }
}