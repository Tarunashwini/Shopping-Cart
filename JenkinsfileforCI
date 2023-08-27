pipeline {
    agent any
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        
        stage("Git Checkout"){
            steps{
                git url: "https://github.com/Tarunashwini/Shopping-Cart.git", branch: "main"
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
        stage("Sonarqube Analysis"){
            steps{
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://40.76.240.36:9000/  -Dsonar.login=squ_d6cd1258944498b4866faafd845ff88e35819368 -Dsonar.projectName=shopping-cart\
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=shopping-cart'''
            }
        }
        
        
        
        stage("Build"){
            steps{
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Pushing the docker file to the dockerhub') {
            steps {
                echo 'Pushing the image to docker hub'
                withCredentials([usernamePassword(credentialsId:"dockerhub",passwordVariable:"dpass", usernameVariable:"duser")]){
                    sh "docker login -u ${env.duser} -p ${env.dpass}"
                    sh "docker build -t shopping-cart -f docker/Dockerfile ."
                    sh "docker tag shopping-cart ${env.duser}/shopping-cart:latest"
                    sh "docker push ${env.duser}/shopping-cart:latest"
                    
                    
                }
            }
        }
        
        stage("Trigger CD Pipeline"){
            steps{
                build job: "CD_Pipe", wait: true
            }
        }
    }
}
