node {
    def workspc = pwd()

    checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: '/home/Downloads/simple-python-pyinstaller-app']]])

    docker.image('python:2-alpine').inside{
        stage('Build'){
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash includes: 'sources/*.py*', name: 'compiled-results'
        }
    }

    docker.image('qnib/pytest').inside{
        stage('Test'){
            sh 'py.test --verbose --junit-xml report.xml sources/test_calc.py'
        }
        step([$class: 'JUnitResultArchiver', checksName: '', testResults: 'report.xml'])
    }

    withEnv(["VOLUME=${pwd()}/sources:/src", "IMAGE=cdrx/pyinstaller-linux:python2"]) {
        
        stage('Deploy'){
            
            dir("${pwd()}/sources:/src.BUILD_ID}") {
                unstash 'compiled-results'
                sh "docker run --rm -v $VOLUME $IMAGE \'pyinstaller -F add2vals.py\'"
                
            }
            archiveArtifacts artifacts: "$env.BUILD_ID/sources/dist/add2vals", followSymlinks: false
            sh "docker run --rm -v $VOLUME $IMAGE \'rm -rf build dist\'"
        }
    }
}