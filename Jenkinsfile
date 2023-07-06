podTemplate(containers: [
    containerTemplate(
        name: 'tdp-builder', 
        image: 'yanisbariteau/tdp-builder:jenkins', 
        command: 'sleep', 
        args: '30d'
        )
  ]) {

    node(POD_LABEL) {
        container('tdp-builder') {
            environment {
                number="${currentBuild.number}"
            }
            stage('Git Clone') {
                echo "Cloning.."
                git branch: 'branch-2.1-TDP-alliage-k8s', url: 'https://github.com/Yanis77240/hbase'
            }   
            stage ('Build') {
                echo "Building.."
                sh '''
                mvn clean install -DskipTests -Dhadoop.profile=3.0
                '''
            }
            stage('Test') {
                echo "Testing.."
                sh '''
                mvn clean test -Dhadoop.profile=3.0 --fail-never
                '''
            }
            stage('Deliver') {
                echo "Deploy..."
                withCredentials([usernamePassword(credentialsId: '4b87bd68-ad4c-11ed-afa1-0242ac120002', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh 'mvn clean deploy assembly:single -DskipTests -Dhadoop.profile=3.0 -s settings.xml'
                }
            }
            stage("Publish tar.gz to Nexus") {
                echo "Publish tar.gz..."
                withEnv(["number=${currentBuild.number}"]) {
                    withCredentials([usernamePassword(credentialsId: '4b87bd68-ad4c-11ed-afa1-0242ac120002', passwordVariable: 'pass', usernameVariable: 'user')]) {
                        sh '''
                        curl -v -u $user:$pass --upload-file hbase-assembly/target/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-bin.tar.gz http://10.110.4.212:8081/repository/maven-tar-files/hbase/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-bin-${number}.tar.gz
                        curl -v -u $user:$pass --upload-file hbase-assembly/target/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-client-bin.tar.gz http://10.110.4.212:8081/repository/maven-tar-files/hbase/hbase-2.1.10-TDP-0.1.0-SNAPSHOT-client-bin-${number}.tar.gz
                        '''
                    }
                }
            }       
        }
    }
}