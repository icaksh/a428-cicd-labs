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
            withDockerContainer(args: "--entrypoint=''", image: 'six8/pyinstaller-alpine-linux-amd64:alpine-3.12-python-2.7-pyinstaller-v3.4'){
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
    }
}
