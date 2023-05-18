node {
    withDockerContainer(args: '-p 3000:3000' ,image: 'node:16-buster-slim'){
        stage('Build') { 
            checkout scm
            sh 'npm install' 
        }
        stage('Test') { 
            sh './jenkins/scripts/test.sh' 
        }
        stage('Manual Approval'){
            input message: 'Lanjutkan ke tahap Deploy? (Klik Proceed untuk melanjutkan eksekusi pipeline ke tahap Deploy atau Abort untuk menghentikan eksekusi pipeline)'
        }
        stage('Deploy') {
            try{
                sh './jenkins/scripts/deliver.sh'
            } catch (e) {
                throw e
            } finally {
                sleep 60
                input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)' 
                sh './jenkins/scripts/kill.sh' 
            }    
        }
    }
    stage("Deliver"){
        checkout scm
        withCredentials([string(credentialsId: 'GITHUB_HOST_KEY', variable: 'GITHUB_HOST_KEY')]) {
            sh 'mkdir -p ~/.ssh && echo "$GITHUB_HOST_KEY" >> ~/.ssh/known_hosts'
        }
        sshagent (credentials: ['icaksh']) {
            sh('git config --global user.email "me@icaksh.my.id"')
            sh('git config --global user.name "icaksh"')
            sh('git add . && git commit -am "[Jenkins CI] Add build file"')
            sh('git push git@github.com:icaksh/a428-cicd-labs.git HEAD:react-app  --tags -f --no-verify')
        }
    }
}
