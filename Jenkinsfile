pipeline {
    agent {
        docker {
            image 'python:2-alpine'
        }
    }
    stages {
        stage('Test') { 
            steps {
                sh './jenkins/Jenkinsfile'
            }
        }
    }
}

// node {
//     def pythonImage = docker.build("python:2-alpine")

//     pythonImage.inside {
//         stage('Test') {
//             sh './jenkins/Jenkinsfile'
//         }
//     }
    
// }