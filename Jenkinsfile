pipeline{
    agent any
    
    environment {
     DOCKER_TAG = getVersion()
    }
    
    stages{
        stage('SCM'){
            steps{
                git branch: 'main',
                credentialsId: 'github',
                url: 'https://github.com/chanthini-a/Edureka_DevOps_Project.git'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t chanthini-a/javaapp:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                  sh "docker login -u chanthini-a -p ${dockerHubPwd}"
                }
                
                sh "docker push chanthini-a/javaapp:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }    
}

def getVersion(){
   def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
   return commitHash
}
