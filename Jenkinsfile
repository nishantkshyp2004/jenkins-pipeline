#!groovy

pipeline{

environment{
    def postdata = '{"key_path": "secret/049db2ac-12d5-47a8-b6b2-ff154bcc7c73"}'

}

agent any
stages{
    stage("Get Credentials from Vault"){
        steps{
            script{
                def response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: postdata, responseHandle:'NONE', url: 'http://ec2-34-196-246-23.compute-1.amazonaws.com:8150/api/viewsecret/'
                println("Status: "+response.status)
                println("Content: "+response.content)

            }
        }
    }
}

}