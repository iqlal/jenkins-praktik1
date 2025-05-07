pipeline {
    agent {
        docker {
            image 'python:3.10'
        }
    }

    environment {
        VENV = 'venv'
    }
    
    stages {
        stage('Setup ENV & Install Deps') {
            steps {
                sh '''
                python -m venv $VENV
                . $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }
        stage('Run Tests') {
            steps {
                sh 'pytest test_app.py'
            }
        }
        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: 'release/*', comparator: 'REGEXP'
                }
            }
            steps {
                echo "simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build success on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1369692688869494925/KVsl4LRNCrLurlNDFg7BkTlCI8E2Sb4ToY47mf9tqxwDLxbKUqg12Owhd0RP9nwUyg6l'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "❌ Build failed on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1369692688869494925/KVsl4LRNCrLurlNDFg7BkTlCI8E2Sb4ToY47mf9tqxwDLxbKUqg12Owhd0RP9nwUyg6l'
                )
            }
        }
    }
}
