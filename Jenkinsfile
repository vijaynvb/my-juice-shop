pipeline {
    agent any

    tools {
        nodejs 'node24'
    }


    parameters {
        booleanParam(
            name: 'ENFORCE_SONAR_GATE',
            defaultValue: false,
            description: 'If true, a failed SonarCloud Quality Gate aborts the pipeline. Off by default so later stages (SCA, container scan, DAST) can still run and be demonstrated even when the gate fails, e.g. due to missing coverage data.'
        )
        booleanParam(
            name: 'ENFORCE_SNYK_GATE',
            defaultValue: false,
            description: 'If true, critical Snyk vulnerabilities abort the pipeline. Off by default so later stages can still run and be demonstrated.'
        )
        booleanParam(
            name: 'ENFORCE_TRIVY_GATE',
            defaultValue: false,
            description: 'If true, critical Trivy container vulnerabilities abort the pipeline. Off by default so later stages can still run and be demonstrated.'
        )
        booleanParam(
            name: 'ENFORCE_ZAP_GATE',
            defaultValue: false,
            description: 'If true, High-risk ZAP alerts abort the pipeline. Juice Shop is deliberately full of High-risk findings (SQLi, XSS, etc.), so this is off by default to let the pipeline demonstrate its later stages; turn it on to see the strict gate behavior.'
        )
    }


    environment {
        SONAR_TOKEN     = credentials('sonar-token')
        SNYK_TOKEN      = credentials('snyk-token')
        IMAGE_NAME      = 'juice-shop-local'
        CONTAINER_NAME  = 'juice-shop-dast'
        APP_PORT        = '3000'
        REPORT_DIR      = 'reports'
        NODE_OPTIONS    = '--max-old-space-size=4096'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }


    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install --include=dev'
            }
        }

        stage('SAST - SonarQube') {
            steps {
                withSonarQubeEnv('sonarcloud') {
                    sh '''
                        npx sonar-scanner \
                          -Dsonar.projectKey=vijaynvb_my-juice-shop \
                          -Dsonar.organization=vijaynvb \
                          -Dsonar.host.url=https://sonarcloud.io \
                          -Dsonar.token=${SONAR_TOKEN}
                    '''
                }
            }
        }

    }
}