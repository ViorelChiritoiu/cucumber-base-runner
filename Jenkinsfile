pipeline{

    agent any

    parameters {
      choice choices: ['chrome', 'firefox'], description: 'Select the browser', name: 'BROWSER'
    }

    stages {

        stage('Start Grid') {
            steps {
                bat "docker-compose -f grid.yaml up --scale ${params.BROWSER}=2 -d"
            }
        }

        stage('Run Tests') {
            steps {
                bat "docker-compose -f test-suites.yaml up --pull=always"
                script {
                    if (fileExists('output/cucumber-base/testng-failed.xml')) {
                        error('Failed tests found')
                    }
                }
            }
        }
    }

    post {
        always {
            bat "docker-compose -f grid.yaml down"
            bat "docker-compose -f test-suites.yaml down"
        }
        success {
            cucumber buildStatus: 'null',
            customCssFiles: '',
            customJsFiles: '',
            failedFeaturesNumber: -1,
            failedScenariosNumber: -1,
            failedStepsNumber: -1,
            fileIncludePattern: '**/cucumber.json',
            jsonReportDirectory: 'output',
            pendingStepsNumber: -1,
            skippedStepsNumber: -1,
            sortingMethod: 'ALPHABETICAL',
            undefinedStepsNumber: -1
        }
    }

}