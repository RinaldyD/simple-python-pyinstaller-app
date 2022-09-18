node {
    
    checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: '/home/Downloads/simple-python-pyinstaller-app']]])

    docker.image('python:2-alpine').inside{
        stage('Build'){
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    docker.image('qnib/pytest').inside{
        stage('Test'){
            sh 'py.test --verbose --junit-xml report.xml sources/test_calc.py'
        }
        step([$class: 'JUnitResultArchiver', checksName: '', testResults: 'report.xml'])
    }

    docker.image('cdrx/pyinstaller-linux:python2').inside{
        stage('Deploy'){
            
            sh 'pyinstaller --onefile sources/add2vals.py'
            archiveArtifacts artifacts: 'dist/add2vals', followSymlinks: false
        }
    }
}