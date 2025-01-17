pipeline {
    agent {
        node {
            label 'jaya'
            customWorkspace '/home/jenkins/'
        }
    }
    stages {
        stage('Installing packages') {
            steps {
                script {
                    sh 'pip install -r requirements.txt'
                }
            }
        }
        stage('Static Code Checking') {
            steps {
                script {
                    sh 'find . -name \\*.py | xargs pylint -f parseable | tee pylint.log'
                    recordIssues(
                        tool: pyLint(pattern: 'pylint.log'),
                        unstableTotalHigh: 100,
                    )
                }
            }
        }
        stage('Running Unit tests') {
            steps {
                script {
                    sh 'pytest --with-xunit --xunit-file=pyunit.xml --cover-xml --cover-xml-file=cov.xml tests/*.py || true'
                    step([$class: 'CoberturaPublisher', 
                        coberturaReportFile: "cov.xml",
                        onlyStable: false,
                        failNoReports: true,
                        failUnhealthy: false,
                        failUnstable: false,
                        autoUpdateHealth: true,
                        autoUpdateStability: true,
                        zoomCoverageChart: true,
                        maxNumberOfBuilds: 10,
                        lineCoverageTargets: '80, 80, 80',
                        conditionalCoverageTargets: '80, 80, 80',
                        classCoverageTargets: '80, 80, 80',
                        fileCoverageTargets: '80, 80, 80',
                    ])
                    junit "pyunit.xml"
                }
            }
        }
    }
}
