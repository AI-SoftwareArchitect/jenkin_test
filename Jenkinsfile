pipeline {
  agent any
  environment {
    IMAGE_NAME = "demo-fastapi-ci"
  }
  stages {
    stage("Checkout") {
      steps {
        checkout scm
      }
    }
    stage("Setup & Install") {
      steps {
        script {
          if (isUnix()) {
            sh '''
              python3 -m venv .venv
              . .venv/bin/activate
              pip install -U pip
              pip install -r requirements.txt semgrep pytest
            '''
          } else {
            bat '''
              python -m venv .venv
              powershell -NoProfile -ExecutionPolicy Bypass -Command ". .\\.venv\\Scripts\\Activate.ps1; pip install -U pip"
              powershell -NoProfile -ExecutionPolicy Bypass -Command ". .\\.venv\\Scripts\\Activate.ps1; pip install -r requirements.txt semgrep pytest"
            '''
          }
        }
      }
    }
    stage("SAST: semgrep") {
      steps {
        script {
          if (isUnix()) {
            sh '''
              . .venv/bin/activate
              semgrep --config .semgrep.yml .
            '''
          } else {
            bat '''
              powershell -NoProfile -ExecutionPolicy Bypass -Command ". .\\.venv\\Scripts\\Activate.ps1; .\\.venv\\Scripts\\semgrep.exe --config .semgrep.yml ."
            '''
          }
        }
      }
    }
    stage("Unit tests") {
      steps {
        script {
          if (isUnix()) {
            sh '''
              . .venv/bin/activate
              pytest -q
            '''
          } else {
            bat '''
              powershell -NoProfile -ExecutionPolicy Bypass -Command ". .\\.venv\\Scripts\\Activate.ps1; .\\.venv\\Scripts\\python.exe -m pytest -q"
            '''
          }
        }
      }
    }
    stage("Build image") {
      steps {
        script {
          if (isUnix()) {
            sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
          } else {
            bat "docker build -t %IMAGE_NAME%:${env.BUILD_NUMBER} ."
          }
        }
      }
    }
    // Deploy aşaması buraya entegre edilebilir
  }
  post {
    always {
      echo "Pipeline finished."
    }
  }
}
