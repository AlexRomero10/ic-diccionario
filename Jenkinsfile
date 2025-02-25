pipeline {
    agent {
        docker {
            image 'debian'
            args '-u root:root'
        }
    }
    stages {
        stage('Clone') {
            steps {
                git branch: 'master', url: 'https://github.com/AlexRomero10/ic-diccionario'
            }
        }
        stage('Install') {
            steps {
                sh 'apt-get update && apt-get install -y aspell-es'
            }
        }
        stage('Test') {
            steps {
                sh '''
                export LC_ALL=C.UTF-8
                OUTPUT=$(cat doc/*.md | aspell list -d es -p ./.aspell.es.pws)
                if [ -n "$OUTPUT" ]; then
                    echo "🔔 Se encontraron errores ortográficos:"
                    echo "$OUTPUT"
                    exit 1
                else
                    echo "✅ No se encontraron errores ortográficos."
                fi
                '''
            }
        }
    }
    post {
        always {
            mail to: 'aletromp00@gmail.com',
                 subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
                 body: "${env.BUILD_URL} has result ${currentBuild.result}"
        }
    }
}
