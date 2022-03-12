pipeline {
    agent any

    stages {
        stage('Verify Branch') {
            steps {
                echo "$GIT_BRANCH"
            }
        }
        stage('Docker Build') {
            steps {
                pwsh 'docker images -a'
                pwsh(script: """
                    cd azure-vote/
                    docker build -t jenkins-pipeline .
                    docker images -a
                    cd ..
                """)
            }
        }
        stage('Start test app') {
            steps {
                pwsh(script: """
            docker-compose up -d
            ./scripts/test_container.ps1
            """)
            }
            post {
                success {
                    echo "App started successfully :)"
                }
                failure {
                    echo "App failed to start :("
                }
            }
        }
        stage('Run tests') {
            steps {
                pwsh(script: """
                python3 -m pytest ./tests/test_sample.py 
                """)
            }
        }
        stage('Stop test app') {
            steps {
                pwsh(script: """
                docker-compose down
                """)
            }
        }
        stage('Push Container') {
            steps {
                echo "Workspace is $WORKSPACE"
                dir("$WORKSPACE/azure-vote") {
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
                            def image = docker.build('frennesson/jenkins-course:latest')
                            image.push()
                        }
                    }
                }
            }
        }
        stage('Scan with Trivy') {
            steps {
                sh ("""
                    trivy image frennesson/jenkins-course:latest
                """)
            }
        }
    }
}
