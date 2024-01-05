pipeline {
    agent any

    stages {
        stage('Prerequsits & Essential Tools') {
            steps {
                // OS essentail tool
                sh 'sudo apt-get -y install software-properties-common apt-transport-https git gnupg sudo nano wget curl zip unzip tcl inetutils-ping net-tools'

                // PHP & its required extensions
                sh 'sudo add-apt-repository ppa:ondrej/php'
                sh 'sudo apt-get install -y php8.2 php8.2-fpm php8.2-bcmath php8.2-curl php8.2-imagick php8.2-intl php-json php8.2-mbstring php8.2-mysql php8.2-xml php8.2-zip'

                // Composer installation to build and run project
                sh '''sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"'''
                sh '''sudo php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"'''
                sh 'sudo php composer-setup.php'
                sh '''sudo php -r "unlink('composer-setup.php');"'''
                sh 'sudo mv composer.phar /usr/local/bin/composer'
            }
        }
        stage ('Build Project'){
            steps {
                sh 'sudo rm -rf sample-app'
                sh 'sudo composer create-project laravel/laravel sample-app'
            }
        }
        stage ('Deploy Project'){
            steps {
                sh 'cd sample-app && sudo php artisan serve &'
            }
        }
        stage('Testing'){
            steps {
                script {
                    def response = httpRequest 'http://localhost:8000'
                    if (response.status == 200) {
                        currentBuild.result = 'SUCCESS'
                    } else {
                        error 'Received a non-200 response code'
                    }
                }
            }
        }
    }
    
    post {
        success {
            sh 'echo "Project is running successfully."'
        }
    }
}
