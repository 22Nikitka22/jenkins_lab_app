pipeline {
    agent any

    stages {
        stage('Клонирование') {
            steps {
                // Клонирование репозитория
                git url: 'https://github.com/22Nikitka22/jenkins_lab_app.git', branch: 'main'
            }
        }

        stage('Сборка') {
            steps {
                echo 'Сборка приложения...'
                sh 'apt-get update'
                sh 'apt-get install -y nodejs'
                sh 'apt-get install -y python3'
                sh 'apt-get install python3-venv'
                sh 'apt-get install -y python3-pip'
                sh 'python3 -m venv venv'
                sh '. venv/bin/activate'
                sh '. venv/bin/activate && python3 -m compileall app.py'
                sh '. venv/bin/activate && pip install -r requirements.txt'
            }
        }

        stage('Запуск автотестов') {
            steps {
                echo 'Запуск тестов...'
                sh '. venv/bin/activate && python3 -m unittest discover -s . -p "test_*.py"'
            }
        }

        stage('Сборка результатов тестов в Allure') {
            steps {
                echo 'Сборка результатов в Allure...'
                sh '. venv/bin/activate && pytest --alluredir=allure-results'
                
                echo 'Генерация отчета Allure...'
                allure([
                    includeProperties: false,
                    jdk: '',
                    results: [[path: 'allure-results']]
                ])
            }
        }

        stage('Анализ исходного кода Sonar') {
            steps {
                echo 'Анализ исходного кода с помощью SonarQube...'
                script {
                    def scannerHome = tool 'sonarscanner'
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=my_python_app -Dsonar.projectName='my_python_app' -Dsonar.projectVersion=1.0 -Dsonar.sources=. -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=squ_5f2195c08dc69048e1833241944f6e0a1e2db691 -Dsonar.exclusions=**/tests/** -Dsonar.python.coverage.reportPaths=coverage.xml -Dsonar.sourceEncoding=UTF-8"
                }
            }
        }

        stage('Сборка Docker контейнера') {
            steps {
                echo 'Сборка Docker контейнера...'
                sh 'docker build -t nikitka22/myapp:2.0.0 .'
            }
        }
    }

    post {
        always {
            echo 'Очистка после сборки...'
        }
    }
}