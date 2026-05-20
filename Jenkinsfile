pipeline {
    agent {
        docker { image 'node:20-alpine' }
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Building ${env.BRANCH_NAME ?: 'local'} branch"
            }
        }

        stage('Install') {
            steps {
                sh 'npm install || true'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Package') {
            steps {
                sh 'tar -czf app.tar.gz app.js package.json'
                archiveArtifacts artifacts: 'app.tar.gz', fingerprint: true
            }
        }
	stage('Deploy') {
            environment {
                API_KEY = credentials('my-api-key')
            }
            steps {
                sh 'echo "Calling API with key length: ${#API_KEY}"'
                sh 'apk add --no-cache curl >/dev/null 2>&1 || true'
                sh 'curl -s -H "Authorization: Bearer $API_KEY" https://httpbin.org/headers'
            }
        }
    }

    post {
        success { echo '✅ Pipeline succeeded' }
        failure { echo '❌ Pipeline failed — check console output' }
        always  { echo "Finished build #${env.BUILD_NUMBER}" }
    }
}
