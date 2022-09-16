node {
    def pythonImage = docker.build("python:2-alpine")

    pythonImage.inside {
        stage('Test') {
            sh './jenkins/Jenkinsfile'
        }
    }
    
}