pipeline {
    agent {  label 'aci-ansible-nae'  }

    stages {
        stage('Git '){
            steps{
            git branch: 'main', url: 'https://github.com/saurabhkothari/Cisco-ACI.git'
            }
        }
        stage('Generate Config'){
          
            steps {
             withCredentials([file(credentialsId: 'ansible-user-key', variable: 'key')]){
               ansiColor('xterm') {
                
                            ansiblePlaybook(
                             playbook: 'tenant.yml' ,
                             inventory: 'inventory',
                             extras: '--check -vvv',
                             vaultCredentialsId: 'ansible-vault-id',
                             extraVars: [
                                 private_key: "${key}",
                                 ],
                             colorized: true)
                    
               
                            } 
                        }
                    }
                }

        stage('Validate Config'){
          
            steps {
             withCredentials([file(credentialsId: 'ansible-user-key', variable: 'key')]){
               ansiColor('xterm') {
                
                            ansiblePlaybook(
                             playbook: 'pcv.yaml' ,
                             inventory: 'inventory',
                             vaultCredentialsId: 'ansible-vault-id',
                             colorized: true)
                    
               
                            } 
                        }
                    }
                }
        stage('Push Config'){
          
            steps {
             withCredentials([file(credentialsId: 'ansible-user-key', variable: 'key')]){
               ansiColor('xterm') {
                
                            ansiblePlaybook(
                             playbook: 'tenant.yml',
                             inventory: 'inventory',
                             vaultCredentialsId: 'ansible-vault-id',
                             extraVars: [
                                 private_key: "${key}",
                                 ],
                             colorized: true)
                    
               
                            } 
                        }
                    }
                }          
    }
    post { 
        success { 
         wrap([$class: 'BuildUser']) {    
          withCredentials([string(credentialsId: 'webex-key', variable: 'webex')]){ 
            
            sh """
                    curl --location --request POST 'https://webexapis.com/v1/messages' \
--header 'Authorization: Bearer ${webex}' \
--header 'Content-Type: application/json' \
--data-raw '{
  "roomId": "Y2lzY29zcGFyazovL3VzL1JPT00vYWRhOWQ0ODAtZTYwNy0xMWViLTk3YWYtOTkwNTEzNzMzMjBl",
  
  "markdown": "Started at: ${BUILD_TIMESTAMP}\\nAuthor:${env.BUILD_USER}(${env.BUILD_USER_EMAIL})\\nBuild ID: ${BUILD_NUMBER}\\nBuild_URL: ${JOB_URL}\\nResult: ${currentBuild.currentResult}" 
}'
         """
        
                }
            }
        }
        failure { 
         wrap([$class: 'BuildUser']) {       
          withCredentials([string(credentialsId: 'webex-key', variable: 'webex')]){ 
             
            sh """
                    curl --location --request POST 'https://webexapis.com/v1/messages' \
--header 'Authorization: Bearer ${webex}' \
--header 'Content-Type: application/json' \
--data-raw '{
  "roomId": "Y2lzY29zcGFyazovL3VzL1JPT00vYWRhOWQ0ODAtZTYwNy0xMWViLTk3YWYtOTkwNTEzNzMzMjBl",
  
  "markdown": "Started at: ${BUILD_TIMESTAMP}\\nAuthor:${env.BUILD_USER}(${env.BUILD_USER_EMAIL})\\nBuild ID: ${BUILD_NUMBER}\\nBuild_URL: ${JOB_URL}\\nResult: ${currentBuild.currentResult}" 
}'
         """    
                }
            }
        }
    }
}
