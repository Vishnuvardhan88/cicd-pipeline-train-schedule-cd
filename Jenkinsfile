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
            }
            steps {
                withCredentials([usernamePassword(credenialsId: 'webserver_login', usernameVariable: 'USERNAME' , userpassVariable: 'USERPASS' )]) {
                    sshPublisher(
                        failOnError: true ,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassPhrase: "$USERPASS"
                                    ],
                               transfers: [
                                   sshTransfer(
                                       sourceFiles: '/dist/trainSchedule.zip',
                                       removePrefix: 'dist/',
                                       remoteDirectory: '/tmp',
                                       execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/train-schedule -d /opt/train-schedule/* && sudo /usr/bin/systemctl start train-schedule '                
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
