node {
    stage('Build') {
        withDockerContainer('python:2-alpine') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash includes: 'sources/*.py*', name: 'compiled-results'
        }
    }
    try {
        stage('Test') {
            withDockerContainer('qnib/pytest') {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
                stash includes: 'sources/*.py*', name: 'compiled-results'
            }
        }
    } catch (e) {
        echo 'Terjadi kesalahan'
        throw e
    } finally {
        junit 'test-reports/results.xml'   
    }
    stage('Deliver') { 
        withEnv(["VOLUME = '$(pwd)/sources:/src'",
                 "IMAGE = 'cdrx/pyinstaller-linux:python2'"]) {
            try {
                dir('env.BUILD_ID') {
                    unstash(name: 'compiled-results') 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                }
            } catch (e) {
                echo 'Terjadi kesalahan'
                throw e
            } finally {
                def currentResult = currentBuild.result
                if (currentResult == 'SUCCESS') {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                }
            }
        }
    }
}