node {
   def registryProjet='registry.gitlab.com/saidane06/presentations-jenkins/wartest'
   def IMAGE="${registryProjet}:version-${env.BUILD_ID}"
    stage('Build - Clone') {
          git 'https://github.com/saidane06/war-build-docker.git'
    }
    stage('Build - Maven package'){
            sh 'mvn package'
    }
    def img = stage('Build') {
          docker.build("$IMAGE",  '.')
    }
    stage('Build - Test') {
            img.withRun("--name run-$BUILD_ID -p 8081:8080") { c ->
            sh 'docker ps'
            sh 'netstat -ntaup'
            sh 'sleep 30s'
            sh 'curl 192.168.117.128:8081'
            sh 'docker ps'
          }
    }
    stage('Build - Push') {
          docker.withRegistry('https://registry.gitlab.com', 'gitlab') {
              img.push 'latest'
              img.push()
          }
    }
    stage('Deploy - Clone') {
          git 'https://github.com/saidane06/jenkins-ansible-docker.git'
    }
    stage('Deploy - End') {
      ansiblePlaybook (
          colorized: true,
          become: true,
          playbook: 'playbook.yml',
         inventory: '${HOST},',
          extras: "--extra-vars 'image=$IMAGE'"
      )
    }

}

