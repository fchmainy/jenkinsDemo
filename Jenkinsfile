#!/usr/bin/env groovy

import groovy.json.JsonOutput

def slackNotificationChannel = "#tests"     

def notifySlack(text, channel, attachments) {
    def slackURL = "https://hooks.slack.com/services/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"
    def jenkinsIcon = 'https://wiki.jenkins-ci.org/download/attachments/2916393/logo.png'

    def payload = JsonOutput.toJson([text: text,
        channel: "#tests",
        username: "webhookbot",
        icon_url: jenkinsIcon
    ])

    sh "curl -X POST --data-urlencode \'payload=${payload}\' ${slackURL}"
}


node {
   stage('Preparation') { 
      // Get some code from a GitHub repository
      git 'https://github.com/fchmainy/jenkinsDemo.git'
   }
   stage('Testing') {
      //Run the tests
      //sh "/usr/local/bin/ansible-lint myLab.yaml"
      sh "/usr/local/bin/ansible-review myLab.yaml"
   }
   stage('Build') {
       //Ansible Playbook
       ansiblePlaybook(
         colorized: true, 
        // inventory: 'hosts.ini', 
         playbook: 'myLab.yaml', 
         sudoUser: null,
         extraVars: [
            username: 'admin',
            password: [value: 'admin', hidden: true],
            fqdn: params.fqdn,
            vsIP: params.vsIP,
            member1: params.member1,
            member2: params.member2
         ])
         
         
   }
   stage("Post to Slack") {
        notifySlack("A new service is deployed!", slackNotificationChannel, [])
   }
   
   
   stage('Approval') {
      //Gate the process and require approval
      input 'Proceed?'
   }
}
