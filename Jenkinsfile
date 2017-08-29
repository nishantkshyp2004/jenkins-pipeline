#!groovy

pipeline{

environment{
    def get_valut_cred_postdata = '{"key_path": "secret/049db2ac-12d5-47a8-b6b2-ff154bcc7c73"}'
    def sc_postdata = '{"path": "source_code_tool_type/github"}'
}

agent any

stages{
    stage("Get Source Control Tool command"){
        steps{
           script{
           def source_control_command = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: sc_postdata, responseHandle:'STRING', url: 'http://127.0.0.1:8000/get_node_data/'
           echo source_control_command
           }
        }
    }
    stage("Get Credentials from Vault"){
        steps{
            script{
                def response = httpRequest acceptType:'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: get_valut_cred_postdata, responseHandle:'LEAVE_OPEN', url: 'http://ec2-34-196-246-23.compute-1.amazonaws.com:8150/api/viewsecret/'
                println("Status: "+response.status)
                println("Content: "+response.content)

            }
        }
    }
}

}
