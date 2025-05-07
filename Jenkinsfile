pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "teedy2025_manual"  // 镜像名称
    }
    stages {
        // 阶段1：拉取代码
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // 阶段2：构建 Docker 镜像
        stage('Build Image') {
            steps {
                script {
                    docker.build("${env.DOCKER_IMAGE}")
                }
            }
        }

        // 阶段3：运行三个容器
        stage('Run Containers') {
            steps {
                script {
                    // 停止旧容器（如果存在）
                    sh 'docker stop teedy-8082 teedy-8083 teedy-8084 || true'
                    sh 'docker rm teedy-8082 teedy-8083 teedy-8084 || true'

                    // 启动新容器
                    docker.image("${env.DOCKER_IMAGE}").run('--name teedy-8082 -d -p 8082:8080')
                    docker.image("${env.DOCKER_IMAGE}").run('--name teedy-8083 -d -p 8083:8080')
                    docker.image("${env.DOCKER_IMAGE}").run('--name teedy-8084 -d -p 8084:8080')
                }
            }
        }
    }
}
