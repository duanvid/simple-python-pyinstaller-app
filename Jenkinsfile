node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            checkout scm
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
       }
    }
}
