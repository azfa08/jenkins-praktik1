pipeline {
    agent {
        docker {
            image 'python:3.10'
        }
    }

    environment {
        VENV = 'venv'
        PYTHON = './venv/bin/python'
        PIP = './venv/bin/pip'
        PYTEST = './venv/bin/pytest'
    }

    stages {
        stage('Setup Environment & Install Dependencies') {
            steps {
                sh '''
                    python -m venv $VENV
                    $PIP install --upgrade pip
                    $PIP install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    $PYTEST test_app.py
                '''
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/.*", comparator: "REGEXP"
                }
            }
            steps {
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://canary.discord.com/api/webhooks/1371137419285631051/Afp1xOY_y0LXo1UApetjAj7G10AKna7jU81qoxPjNP22PHkcc9waA5Kpf_yxmKS73PkM'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://canary.discord.com/api/webhooks/1371137419285631051/Afp1xOY_y0LXo1UApetjAj7G10AKna7jU81qoxPjNP22PHkcc9waA5Kpf_yxmKS73PkM'
                )
            }
        }
    }
}
