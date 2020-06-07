pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps(Build){
                bat 'mvn clean package -DskipTest=ture'
            }
        }
    }
}