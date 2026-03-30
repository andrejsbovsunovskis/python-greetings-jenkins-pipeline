def installPipDeps() {
    echo "Installing all required Python dependencies..."

    dir('python-greetings') {
        deleteDir()
        git url: 'https://github.com/mtararujs/python-greetings'

        sh '''
            echo "Listing files in python-greetings repository..."
            ls -la

            echo "Creating Python virtual environment..."
            python3 -m venv venv

            echo "Installing Python requirements..."
            ./venv/bin/python -m pip install -r requirements.txt
        '''
    }
}

def deployToEnv(String envName, String port) {
    echo "Deploying application to ${envName} environment on port ${port}..."

    dir("python-greetings-${envName}") {
        deleteDir()
        git url: 'https://github.com/mtararujs/python-greetings'

        sh """
            echo "Listing files in python-greetings repository for ${envName}..."
            ls -la

            echo "Creating Python virtual environment for ${envName}..."
            python3 -m venv venv

            echo "Installing Python requirements for ${envName}..."
            ./venv/bin/python -m pip install -r requirements.txt

            echo "Stopping existing PM2 process for ${envName} if it exists..."
            pm2 delete greetings-app-${envName} || true

            echo "Starting application for ${envName} on port ${port}..."
            pm2 start app.py --name greetings-app-${envName} --interpreter ./venv/bin/python -- --port ${port}

            echo "Saving PM2 process list..."
            pm2 save
        """
    }
}

def testOnEnv(String envName) {
    echo "Running API tests on ${envName} environment..."

    dir("course-js-api-framework-${envName}") {
        deleteDir()
        git url: 'https://github.com/mtararujs/course-js-api-framework'

        sh """
            echo "Listing files in course-js-api-framework repository for ${envName}..."
            ls -la

            echo "Installing npm dependencies for ${envName} tests..."
            npm install

            echo "Running greetings tests for ${envName}..."
            npm run greetings greetings_${envName}
        """
    }
}

pipeline {
    agent any

    stages {
        stage('install-pip-deps') {
            steps {
                script {
                    installPipDeps()
                }
            }
        }

        stage('deploy-to-dev') {
            steps {
                script {
                    deployToEnv('dev', '7001')
                }
            }
        }

        stage('tests-on-dev') {
            steps {
                script {
                    testOnEnv('dev')
                }
            }
        }

        stage('deploy-to-stg') {
            steps {
                script {
                    deployToEnv('stg', '7002')
                }
            }
        }

        stage('tests-on-stg') {
            steps {
                script {
                    testOnEnv('stg')
                }
            }
        }

        stage('deploy-to-preprod') {
            steps {
                script {
                    deployToEnv('preprod', '7003')
                }
            }
        }

        stage('tests-on-preprod') {
            steps {
                script {
                    testOnEnv('preprod')
                }
            }
        }

        stage('deploy-to-prod') {
            steps {
                script {
                    deployToEnv('prod', '7004')
                }
            }
        }

        stage('tests-on-prod') {
            steps {
                script {
                    testOnEnv('prod')
                }
            }
        }
    }
}
