pipeline{
    agent any
    tools {
      maven 'Digian-Maven'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('Java SCM'){
            steps{
                git branch: 'main', credentialsId: 'Digian-GitHub', url: 'https://github.com/NomanAkram29/java-app.git'
            }
        }
        
        stage('Java Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Java Docker Build'){
            steps{
                sh "docker build . -t nomana29/java-app:${DOCKER_TAG}"
            }
        }
        
        stage('Java DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'Digian-DockerHub', variable: 'DockerHubPWD')]) {
                    sh "docker login -u nomana29 -p ${DockerHubPWD}"
                }
                
                sh "docker push nomana29/java-app:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'Digian-SSH', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
