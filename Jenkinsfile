pipeline {
    agent any
    stages {
        stage("Verifiy tooling") {
            steps {
                sh '''
                    podman info
                    podman version
                    podman-compose version
                '''
            }
        }
        stage("Clear all running podman containers"){
            steps {
                script {
                    try {
                        sh 'podman rm -f $(podman ps -a -q)'
                    } catch (Exception e){
                        echo 'No running container to clear up...'
                    }
                }
            }
        }
        stage("Start Podman"){
            steps {
                sh 'make up'
                sh 'podman-compose ps'
            }
        }
        stage("Run composer Install"){
            steps {
                script {
                    sh 'podman exec -it php /bin/bash'
                    sh 'composer install'
                }
            }
        }
        stage("Run Unit Test"){
            steps {
                script {
                    sh 'podman exec -it php /bin/bash'
                    sh 'php artisan test'
                }
            }
        }
    }
    post {
        always {
            sh 'podman-compose down --remove-orphans -v'
            sh 'podman-compose ps'
        }
    }
}