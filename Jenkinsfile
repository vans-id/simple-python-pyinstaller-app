node {
    stage('Build') {
        withDockerContainer('python:2-alpine') {
            sh 'python -m py_compile /home/simple-python-pyinstaller-app/sources/add2vals.py /home/simple-python-pyinstaller-app/sources/calc.py'
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
}