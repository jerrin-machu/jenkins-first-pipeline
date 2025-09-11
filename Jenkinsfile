pipeline {
  agent any

  environment {
    VENV_DIR = '.venv'
    PY_BIN = "${env.WORKSPACE}/${env.VENV_DIR}/bin/python" // builds the full path to the Python binary inside your venv
    PIP_BIN = "${env.WORKSPACE}/${env.VENV_DIR}/bin/pip" // points to the pip        binary inside your venv
  }

  stages {
    stage('Checkout') {
      steps {
        // either use checkout scm if Jenkinsfile lives in repo, or your git step
        git branch: 'main', url: 'https://github.com/jerrin-machu/jenkins-first-pipeline.git'
      }
    }

    stage('Setup virtualenv') {
      steps {
        sh '''
          set -euo pipefail

          echo "Workspace: $PWD"

          # ensure python3 exists
          if ! command -v python3 >/dev/null 2>&1; then
            echo "ERROR: python3 not found on this node. Install python3 and python3-venv."
            exit 1
          fi

          # remove previous venv so each build starts fresh (safe deletion)
          if [ -d "${VENV_DIR}" ]; then
            echo "Removing existing virtualenv ${VENV_DIR}"
            rm -rf "${VENV_DIR}"
          fi

          echo "Creating virtualenv at ${VENV_DIR}"
          python3 -m venv "${VENV_DIR}"

          echo "Upgrading pip and setuptools"
          ${PIP_BIN} install --upgrade pip setuptools

          if [ -f requirements.txt ]; then
            echo "Installing from requirements.txt"
            ${PIP_BIN} install -r requirements.txt
          else
            echo "requirements.txt not found — installing pytest only"
            ${PIP_BIN} install pytest
          fi

          echo "Python in venv: $(${PY_BIN} --version)"
          echo "Pip in venv: $(${PIP_BIN} --version)"
        '''
      }
    }

    stage('Build') {
      steps {
        echo "Building the App"
        // add build commands here if you have any (e.g. packaging, linting)
      }
    }

    stage('Test') {
      steps {
        sh '''
          set -euo pipefail
          # Use the venv python binary directly to run pytest
          ${PY_BIN} -m pytest -q || { echo "pytest failed"; exit 1; }
        '''
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deploy Step (placeholder)'
      }
    }
  }

  post {
    always {
      // optional: remove venv so next build will recreate it
      sh '''
        echo "Cleaning up virtualenv (optional)"
        rm -rf "${VENV_DIR}" || true
      '''
      // optionally archive test reports, artifacts etc.
    }
  }
}

//end of jenkins failed
