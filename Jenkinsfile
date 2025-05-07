pipeline {
    agent any
    stages {
        stage('Build Image') {
            steps {
                script {  // 必须包裹在 script 块中
                    docker.build("teedy2025_manual")
                }
            }
        }
        stage('Run Containers') {
            steps {
                script {
                    // 停止旧容器（忽略错误）
                    sh '''
                        docker stop teedy-8082 || true
                        docker rm teedy-8082 || true
                        docker stop teedy-8083 || true
                        docker rm teedy-8083 || true
                        docker stop teedy-8084 || true
                        docker rm teedy-8084 || true
                    '''
                    // 启动新容器
                    docker.run(
                        image: 'teedy2025_manual',
                        name: 'teedy-8082',
                        ports: ['8082:8080'],
                        detach: true
                    )
                    docker.run(
                        image: 'teedy2025_manual',
                        name: 'teedy-8083',
                        ports: ['8083:8080'],
                        detach: true
                    )
                    docker.run(
                        image: 'teedy2025_manual',
                        name: 'teedy-8084',
                        ports: ['8084:8080'],
                        detach: true
                    )
                }
            }
        }
    }
}
