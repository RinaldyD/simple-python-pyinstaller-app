node {
    
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

    withEnv(['VOLUME = \'$(pwd)/sources:/src\'', 'IMAGE = \'cdrx/pyinstaller-linux:python2\'']) {
        
        stage('Deploy'){
            
            dir('env.BUILD_ID') {
                unstash 'compiled-results'
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
            }

            archiveArtifacts artifacts: '${env.BUILD_ID}/sources/dist/add2vals', followSymlinks: false
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
        }
    }
}


pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deploy') {
            agent any
            environment {
                VOLUME = '$(pwd)/sources:/src'
                IMAGE = 'cdrx/pyinstaller-linux:python2'
            }
            steps {
                dir(path: env.BUILD_ID) {
                    unstash(name: 'compiled-results')
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                }
            }
        }
    }
}