node {
    
    checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: '/home/Downloads/simple-python-pyinstaller-app']]])

    stage('Build'){
        withDockerContainer('python:2-alpine') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    stage('Test'){
        withDockerContainer('qnib/pytest') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
}