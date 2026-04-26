pipeline {
    agent any
    tools {
        maven "maven default" 
    }
    stages {
        stage('Git fetch') {
            steps {
                git branch: 'main', url: 'https://github.com/ualhmis2026-proyectotrio/Practica-5-Jesus.git'
            }
        }
        stage('Compile, Test, Package') {
            steps {
                sh "mvn -f pom.xml clean package"
            }
        }
        stage ('Analysis') {
            steps {
                sh "mvn -f pom.xml site"
            }
            post {
                always {
                    recordIssues enabledForFailure: true, tool: checkStyle()
                    recordIssues enabledForFailure: true, tool: pmdParser()
                    recordIssues enabledForFailure: true, tool: cpd()
                    recordIssues enabledForFailure: true, tool: findBugs()
                    recordIssues enabledForFailure: true, tool: spotBugs()
                    dependencyCheckPublisher pattern: '**/target/site/dependency-check-report.xml'
                }
            }
        }
        stage ('Documentation') {
            steps {
                sh "mvn -f pom.xml javadoc:javadoc javadoc:aggregate"
            }
        }
    }
    post {
        always { 
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts '**/target/*.jar'
            
            jacoco(
                execPattern: '**/target/jacoco.exec',
                classPattern: '**/target/classes',
                sourcePattern: '**/src/',
                exclusionPattern: '**/test/'
            )
            
            publishHTML(target: [reportName: 'Maven Site', reportDir: 'target/site', reportFiles: 'index.html', keepAll: false])
            
            script {
                try {
                    step([$class: 'JavadocArchiver', javadocDir: 'target/site/apidocs', keepAll: false])
                } catch (Exception e) {
                    echo "Aviso: No se pudo archivar Javadoc porque Maven no creó la carpeta apidocs."
                }
            }
        }
    }
}
