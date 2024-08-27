pipeline {
    
    agent any
    environment{
        SONAR_HOME = tool "Sonar"
    }
    stages {
        
        stage("Code"){
            steps{
                git url: "https://github.com/prateekmudgal/DevSecOps_ToDo_app_Jenkins_sonarqube.git" , branch: "main"
                echo "Code Cloned Successfully"
            }
        }
        stage("SonarQube Analysis"){
            steps{
               withSonarQubeEnv("Sonar"){
                   sh "${SONAR_HOME}/bin/sonar-scanner -Dsonar.projectName=node-todo-app -Dsonar.projectKey=node-todo-app -X"
               }
            }
        }
        stage("SonarQube Quality Gates"){
            steps{
               timeout(time: 5, unit: "MINUTES"){
                   waitForQualityGate abortPipeline: false
               }
            }
        }
        
        stage("Build & Test"){
            steps{
                sh 'docker build -t devsecops-todo-app:latest .'
                echo "Code Built Successfully"
            }
        }
        stage("Trivy"){
            steps{
                sh "trivy image devsecops-todo-app"
            }
        }
        stage("Push to Private Docker Hub Repo"){
            steps{
                withCredentials([usernamePassword(credentialsId:"DockerHubCreds",passwordVariable:"dockerPass",usernameVariable:"dockerUser")]){
                sh "docker login -u ${env.dockerUser} -p ${env.dockerPass}"
                sh "docker tag devsecops-todo-app:latest ${env.dockerUser}/devsecops-todo-app:latest"
                sh "docker push ${env.dockerUser}/devsecops-todo-app:latest"
                }
                
            }
        }
        stage("Deploy"){
            steps{
                sh "docker-compose up -d"
                echo "App Deployed Successfully"
            }
        }
    }
}