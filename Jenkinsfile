pipeline {
  environment {
    VERCEL_PROJECT_NAME = 'learn-jenkins-app'
    VERCEL_TOKEN = credentials('vercel-token') // ดึงจาก Jenkins
  }
  agent {
    kubernetes {
      // This YAML defines the "Docker Container" you want to use
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: my-builder  # We will refer to this name later
            image: node:20-alpine
            command:
            - cat
            tty: true
      '''
    }
  }
  stages {
    stage('Test npm') {
      steps {
        container('my-builder') {
          sh 'npm --version'
          sh 'node --version'
        }
      }
    }
    stage('Build') {
      steps {
        container('my-builder') {
          sh 'npm ci'
          sh 'npm run build'
        }
      }
    }
    stage('Test Build') {
      steps {
        container('my-builder') {
          sh 'npm run test'
        }
      }
    }
    stage('Deploy') {
      steps {
        container('my-builder') {
          sh 'npm install -g vercel@latest'
          // ตั้งค่า Vercel CLI
          sh '''
            vercel login --token $VERCEL_TOKEN
            vercel link $VERCEL_PROJECT_NAME --token $VERCEL_TOKEN
          '''
        }
      }
    }

  }
  post {
    always {
      junit 'test-results/junit.xml'
    }
  }
}
