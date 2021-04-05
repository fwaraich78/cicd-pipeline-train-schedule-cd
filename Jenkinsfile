pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            } // end steps
        } // end stage
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', 'passwordVariable: 'USERPASS' )]) {
                                                  sshPublisher(
                                                      faileOnError: true,
                                                      ContinueOnError: false,
                                                      publishers: [
                                                          sshPublishedDesc(
                                                              configName: 'staging',
                                                              sshCredentials: [
                                                                  username: "$USERNAME",
                                                                  encryptedPassphrase: "$USERPASS"
                                                                  ],
                                                              transfers: [
                                                                  sshTransfer(
                                                                      sourceFiles: 'dist/trainSchedule.zip',
                                                                      removePrefix: 'dist/',
                                                                      remoteDirectory: '/temp',
                                                                      execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                                                      )
                                                                  ]
                                                              )
                                                          ]
                                                      )
                                                  }
    }
}
