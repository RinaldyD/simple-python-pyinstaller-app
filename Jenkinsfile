node {
    
    checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: '/home/Downloads/simple-python-pyinstaller-app']]])

    docker.image('python:2-alpine').inside{
        stage('Build'){
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    docker.image('qnib/pytest').inside{
        stage('Test'){
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        step([$class: 'JUnitResultArchiver', checksName: '', testResults: '/test-reports/results.xml'])
    }
}