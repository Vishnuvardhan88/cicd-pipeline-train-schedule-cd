pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        
        stage ('DeployingtoStage') {
            
            when {
                branch 'master' 
                withCredentials([usernamePassword(credenialsId: 'webserver_login', usernameVariable: 'USERNAME' , userpassVariable: 'USERPASS' )]) {
                    sshPublisher(
                        failOnError: 'true' ,
                        continueonError: 'false',
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: '$USERNAME',
                                    encryptedPassPhrase: '$USERPASS'
                                    ],
                               transfers: [
                                   sshTransfer(
                                       sourceFiles: '/dist/trainSchedule.zip'
                                       removePrefix: 'dist/'
                                       remoteDirectory: '/tmp'
                                       execCommands: '/usr/bin/systemctl stop train-schedule && rm -rf /opt/trainSchedule/* && unzip /tmp/train-schedule -d /opt/train-schedule/* && /usr/bin/systemctl start train-schedule '                
                                       )
                                   ]
                                )
                                ]
                        )
               
                                 }
                                 }
                    
        }
        
    }
}
