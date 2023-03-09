pipeline {
    agent { 
        node {
            label 'docker-tdp-builder'
            }
      }
    environment {
        number="${currentBuild.number}"
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
        /*stage('Test') {
            steps {
                echo "Testing..."
                sh '''
                mvn test -Dhadoop.profile=3.0 --fail-never
                '''
            }
        }*/
        stage("Publish to Nexus Repository Manager") {
            steps {
                echo "Deploy..."
                withCredentials([usernamePassword(credentialsId: '4b87bd68-ad4c-11ed-afa1-0242ac120002', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh 'mvn clean deploy -DskipTests -Dhadoop.profile=3.0 -s settings.xml'
                }
            }        
        }
        stage("Publish tar.gz to Nexus") {
            steps {
                echo "Publish tar.gz..."
                withCredentials([usernamePassword(credentialsId: '4b87bd68-ad4c-11ed-afa1-0242ac120002', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh '''
                    curl -v -u $user:$pass --upload-file hbase-assembly/target/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-bin.tar.gz http://172.19.0.2:8081/repository/maven-tar-files/hbase/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-bin-${number}.tar.gz
                    curl -v -u $user:$pass --upload-file hbase-assembly/target/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-client-bin.tar.gz http://172.19.0.2:8081/repository/maven-tar-files/hbase/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-client-bin-${number}.tar.gz
                    '''
                }
            }        
        }
    }
}