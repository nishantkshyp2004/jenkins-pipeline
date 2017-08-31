#!groovy

pipeline{

environment{
    def vault_cred_postdata = '{"key_path": "secret/25709213-8a1c-4f73-b6c6-cf486be4d1fe"}'
    def sc_postdata = '{"path": "source_code_tool_type/github"}'
    def jenkins_url = 'http://127.0.0.1:8080'
    def jenkins_store = 'system::system::jenkins'
    def jenkins_username = 'root'
    def jenkins_password = 'root@123'
}

agent any

stages{
    stage("Get Source Control Tool command"){
        steps{
           script{
               try{
                sc_tool_response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: sc_postdata, url: 'http://127.0.0.1:8000/get_node_data/'
               }
               catch (exc)
               {
                    error "Error in fetch source control tool from Zookeeper API,"

               }

           println("Status: "+sc_tool_response.status)
           println("Content: "+sc_tool_response.content)
           sc_tool_command = sc_tool_response.content

           }
        }
    }
    stage("Get Credentials from Vault"){
        steps{
            script{
                println("sc_tool_response : "+"${sc_tool_response.content}")
                try{
                    vault_response = httpRequest acceptType:'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: vault_cred_postdata, url: 'http://ec2-34-196-246-23.compute-1.amazonaws.com:8150/api/viewsecret/'

                }
                catch (exc){
                    error "Error in fetching credentials from Vault service."

                }
                println("Status: "+vault_response.status)
                println("Content: "+vault_response.content)
                response_json = readJSON text: vault_response.content
                vault_response_username = response_json['response']['data']['json_data']['username']

            }
        }
    }
    stage("Storing Credentails to build tool if not exist"){
        steps{
            script{
            println("vault_response_username: "+"${vault_response_username}")

            list_credential_command = 'java -jar jenkins-cli.jar -s '+ jenkins_url + ' list-credentials ' + jenkins_store + ' --username '+jenkins_username+' --password ' + jenkins_password + ' | grep -w'+ " ${vault_response_username}"
            println("list_credential_command: "+"${list_credential_command}")

            def output = sh script: list_credential_command, returnStdout: true

            def list_credential_username = sh script:'echo ${output} | awk {print$2} | sed s:/[^/]*$::', returnStdout: true
            println("list_credential_username: "+"${list_credential_username}")

            if ("${vault_response_username}"  != list_credential_username){

                def passwordInput = input(
                 id: 'PaswordInput', message: 'Let\'s promote?', parameters: [
                 [$class: 'TextParameterDefinition', defaultValue: 't', description: 'Environment', name: 'password'],
                ])

                try{

                    def result = sh script:"java -jar $jenkins-cli.jar -s "+jenkins_url+ " groovy AddUserPwdCred.groovy"+
                     " ${vault_response_username}+'123' 'Jenkins credentials for ${vault_response_username}' ' ${vault_response_username} ' '${vault_response_username}@123' --username "+jenkins_username+" --password " +jenkins_password
                     credentialId = '${vault_response_username}123'

                }
                catch (exc){
                    error "Error in adding credentails to jenkins store"

                }

            }else{

                echo "Credentials with the username: ${vault_response_username} already in the Jenkins Store"
                credentialId = sh script: 'echo ${output} | awk {print$1}', returnStdout: true

            }

            }
        }
    }


}
}
