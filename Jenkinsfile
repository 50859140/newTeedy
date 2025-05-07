pipeline {
    agent any
    stages {
        // 阶段1：拉取代码
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // 阶段2：构建Docker镜像
        stage('Build Image') {
            steps {
                script {
                    sh 'docker build -t teedy2025_manual .'
                }
            }
        }

        // 阶段3：运行三个容器（关键修改部分）
        stage('Run Containers') {
            steps {
                script {
                    sh '''
                        # 停止并删除旧容器（忽略错误）
                        docker stop teedy-8082 teedy-8083 teedy-8084 || true
                        docker rm teedy-8082 teedy-8083 teedy-8084 || true

                        # 启动三个新容器
                        docker run -d -p 8082:8080 --name teedy-8082 teedy2025_manual
                        docker run -d -p 8083:8080 --name teedy-8083 teedy2025_manual
                        docker run -d -p 8084:8080 --name teedy-8084 teedy2025_manual

                        # 验证容器状态
                        echo "当前运行的容器："
                        docker ps --filter "name=teedy-"
                    '''
                }
            }
        }
    }
    
    post {
        always {
            // 归档构建产物（可选）
            archiveArtifacts artifacts: '**/target/*.?ar', fingerprint: true  
            // 保存测试报告（可选）
            junit '**/target/surefire-reports/*.xml'  
        }
    }
}
