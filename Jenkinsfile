pipeline {
    agent { 
        node {
            label 'docker-tdp-builder'
            }
      }
    triggers {
        pollSCM '0 1 * * *'
      }
    stages {
        stage('clone') {
            steps {
                echo "Cloning..."
                git branch: 'branch-2.1-TDP-alliage', url: 'https://github.com/Yanis77240/hbase'
            }
        }
        stage('Build') {
            steps {
                echo "Building..."
                sh '''
                mvn clean install assembly:single -DskipTests -Dhadoop.profile=3.0
                '''
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                echo "Deploy..."
                withCredentials([usernamePassword(credentialsId: '4b87bd68-ad4c-11ed-afa1-0242ac120002', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh 'mvn clean deploy assembly:single -DskipTests -Dhadoop.profile=3.0 -s settings.xml'
                }
            }        
        }
    }
}