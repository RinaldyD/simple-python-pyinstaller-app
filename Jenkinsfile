node {
    stage('Build'){
        withDockerContainer('python:2-alpine').inside{
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test'){
        withDockerContainer('qnib/pytest').inside{
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
}