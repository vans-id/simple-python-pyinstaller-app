node {
    stage('Build') {
        withDockerContainer('python:2-alpine') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash includes: 'sources/*.py*', name: 'compiled-results'
        }
    }
    stage('Test') {
        try {
            withDockerContainer('qnib/pytest') {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
                stash includes: 'sources/*.py*', name: 'compiled-results'
            }
        } catch (e) {
            echo 'Terjadi kesalahan'
            throw e
        } finally {
            junit 'test-reports/results.xml'   
            input 'Lanjutkan ke tahap Deploy?'
        }
    }
    stage('Deploy') {
        withEnv(['VOLUME=$(pwd)/sources:/src', 'IMAGE=cdrx/pyinstaller-linux:python2']) {
            try {
                dir("${env.BUILD_ID}") {
                    unstash 'compiled-results'
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                }
            } catch (e) {
                echo 'Terjadi kesalahan'
                throw e
            } finally {
                archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                sh "scp -i ${env.BUILD_ID}/sources/dist/add2vals ec2-user@ec2-54-255-151-88.ap-southeast-1.compute.amazonaws.com:~/."
                sh "ssh ec2-user@ec2-54-255-151-88.ap-southeast-1.compute.amazonaws.com echo 'Hello World'"
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                sleep time: 1, unit: 'MINUTES'
            }
        }
    }
}