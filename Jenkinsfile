pipeline {
    agent any
    stages {
        stage('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }
        stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'Sonar_Scanner'
            }
            steps{
                withSonarQubeEnv('Sonar'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBackEnd -Dsonar.host.url=http://localhost:9000 -Dsonar.login=ef91ebe92562a6e8758656ba39aab9499b9c8961 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                sleep(5)
                timeout(time: 1, unit:'MINUTES'){
                waitForQualityGate abortPipeline:true
                }
            }

        }
        stage('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'TomCat', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('Api Test'){
            steps{
                dir('api-test'){
                git 'https://github.com/evionadc/tasks-api-tests'
                bat 'mvn test'
                }
            }
        }
        stage('Deploy FrontEnd'){
            steps{
                dir('frontend'){
                git 'https://github.com/evionadc/tasks-frontend'
                bat 'mvn clean package -DskipTests=true'
                deploy adapters: [tomcat8(credentialsId: 'TomCat', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
    }
}


