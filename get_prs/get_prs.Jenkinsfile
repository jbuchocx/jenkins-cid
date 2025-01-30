/* groovylint-disable NestedBlockDepth */
library('common_lib')

def REPOSITORIES = [
    'intel-innersource/applications.compilers.ide.vsnet.common',
    'intel-innersource/applications.compilers.ide.vsnet.cpp',
    'intel-innersource/applications.compilers.ide.vsnet.dpcpp',
    'intel-innersource/applications.compilers.ide.vsnet.dpcppct',
    'intel-innersource/applications.compilers.ide.vsnet.fortran',
    'intel-innersource/frameworks.ide.visual-studio.extensions.clang-cl-hwpgo',
    'intel-innersource/applications.compilers.ide.vsnet.testing'
]

pipeline {
    agent none

    stages {
        stage('Fetch Open PRs') {
            steps {
                script {
                    String emailMessage = '<h3>Open PRs Report:<br>-----------------------</h3>'

                    REPOSITORIES.each { repo ->
                        println "Fetching open PRs for: ${repo}"

                        def response = gitlib.githubRequest(url: "repos/${repo}/pulls", mode: 'GET')
                        def jsonResponse = readJSON text: response.content

                        String repoMessage = "<b>Repository: ${repo}</b><br>"

                        if (jsonResponse.size() > 0) {
                            String activePrsMsg = ''
                            String draftsMsg = ''

                            jsonResponse.each { pr ->
                                if (!pr.draft) {
                                    if (activePrsMsg.isEmpty()) {
                                        activePrsMsg = 'Open PRs: <br>'
                                    }
                                    echo "PR: #${pr.number} - ${pr.title} \n(${pr.html_url})"
                                    activePrsMsg += "#${pr.number} - ${pr.title} <br>(${pr.html_url})<br>"
                                } else {
                                    if (draftsMsg.isEmpty()) {
                                        draftsMsg = 'Drafts:<br>'
                                    }
                                    draftsMsg += "#${pr.number} - ${pr.title} <br>(${pr.html_url})<br>"
                                }
                            }

                            if (!activePrsMsg.isEmpty() && !draftsMsg.isEmpty()) {
                                repoMessage += activePrsMsg + '<br>' + draftsMsg
                            } else if (!activePrsMsg.isEmpty()) {
                                repoMessage += activePrsMsg
                            } else if (!draftsMsg.isEmpty()) {
                                repoMessage += draftsMsg
                            }
                        } else {
                            echo "No open PRs found for ${repo}"
                            repoMessage += 'No open PRs.<br>'
                        }

                        emailMessage += repoMessage + '<br>'
                    }

                    println "Email Message:\n${emailMessage}"
                    sendEmailWithPRs(emailMessage)
                }
            }
        }
    }
}

void sendEmailWithPRs( String message) {
    def dateToday = new Date().format('dd-MM-yyyy')
    def subject = "Daily Open PRs Report: ${dateToday}"

    try{
        emailext(
            subject: subject,
            body: message,
            from: 'cid-support@intel.com',
            to: 'jakubx.buchocki@intel.com'
        )
    } catch (Exception e) {
        println 'FAILED to send an email.'
    }
}
