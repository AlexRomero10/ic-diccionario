pipeline {
    agent {
        docker {
            image 'debian'
            args '-u root:root'  // Ejecuta como root
        }
    }
    stages {
        stage('Clone') {
            steps {
                echo 'Clonando repositorio...'
                git branch: 'master', url: 'https://github.com/AlexRomero10/ic-diccionario'
            }
        }

        stage('Install') {
            steps {
                echo 'Actualizando e instalando aspell-es...'
                sh '''
                    apt-get update
                    apt-get install -y aspell-es
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Realizando comprobación ortográfica...'
                sh '''
                    export LC_ALL=C.UTF-8
                    OUTPUT=$(cat doc/*.md | aspell list -d es -p ./.aspell.es.pws)
                    if [ -n "$OUTPUT" ]; then
                        echo "Se encontraron errores ortográficos:"
                        echo "$OUTPUT"
                        exit 1
                    else
                        echo "No se encontraron errores ortográficos."
                    fi
                '''
            }
        }
    }

    post {
        always {
            script {
                def changes = ""
                if (currentBuild.changeSets.size() > 0) {
                    currentBuild.changeSets.each { changeSet ->
                        changeSet.items.each { item ->
                            changes += "- ${item.commitId.take(7)}: ${item.msg} (por ${item.author})\n"
                        }
                    }
                } else {
                    changes = "No hubo cambios desde el último build."
                }

                mail to: 'aletromp00@gmail.com',
                     from: 'aletromp00@gmail.com',
                     subject: "Estado del pipeline: ${currentBuild.fullDisplayName}",
                     body: """Detalles del build: ${env.BUILD_URL}
Resultado: ${currentBuild.result}

Cambios desde el último build:
${changes}
"""
            }
        }
    }
}
