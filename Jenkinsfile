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
        withEnv(['VOLUME=$(pwd)/sources:/src', 'IMAGE=cdrx/pyinstaller-linux:python2']) {
            sh 'echo ${VOLUME}, ${IMAGE}'
        }
    }
}