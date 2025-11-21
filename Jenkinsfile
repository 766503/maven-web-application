pipeline
{
    agent any

    tools
    {
        maven 'Maven_3.9.7'
    }
    
    environment
    {
        buildNumber = "${BUILD_NUMBER}"
    }

    stages
    {
        stage('Git Checkout')
        {
            steps()
            {
                git branch: 'DevOpsnew', url: 'https://github.com/766503/maven-web-application.git'
            }
        }

        stage('Build Project')
        {
            steps()
            {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image')
        {
            steps()
            {
                sh 'docker build -t 290109/maven_login:${buildNumber} .'
            }
        }

        stage('Push Docker Image to DockerHub Registry')
        {
            steps()
            {
                withCredentials([string(credentialsId: 'Docker_Hub_Password', variable: 'Docker_Hub_Password')])
                {
                    sh 'docker login -u 290109 -p ${Docker_Hub_Password}'
                }
                sh 'docker push 290109/maven_login:${buildNumber}'
            }
        }

        stage('Delete Docker Image Locally in Jenkins Build Server')
        {
            steps()
            {
                sh 'docker rmi -f 290109/maven_login:${buildNumber}'
            }
        }

        stage('Deploy Application to Docker Deployment Server')
        {
            steps()
            {
                sshagent(['DeploymentServer_SSH']) 

                {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@54.226.250.42 docker rm -f mavenwebapplication || true"
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@54.226.250.42 docker run -d --name mavenwebapplication -p 8080:8080 290109/maven_login:${buildNumber}"
                }
            }
        }
    }
}



