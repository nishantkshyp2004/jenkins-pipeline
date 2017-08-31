#!groovy

pipeline{

environment{
    def vault_cred_postdata = '{"key_path": "secret/049db2ac-12d5-47a8-b6b2-ff154bcc7c73"}'
    def sc_postdata = '{"path": "source_code_tool_type/github"}'
}

agent any

stages{
    stage("Get Source Control Tool command"){
        steps{
           script{
           sc_tool_response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: sc_postdata, url: 'http://127.0.0.1:8000/get_node_data/'
           println("Status: "+sc_tool_response.status)
           println("Content: "+sc_tool_response.content)
           }
        }
    }
    stage("Get Credentials from Vault"){
        steps{
            script{
                println("sc_tool_response : "+"${sc_tool_response.content}")
                def vault_response = httpRequest acceptType:'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: vault_cred_postdata, url: 'http://ec2-34-196-246-23.compute-1.amazonaws.com:8150/api/viewsecret/'
                println("Status: "+vault_response.status)
                jsondata = evaluate(vault_response.content)

            }
        }
    }
}

}
