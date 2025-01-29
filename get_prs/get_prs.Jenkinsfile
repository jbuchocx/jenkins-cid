library("complierSharedLib@" + scm.branches[0].name)
library("common_lib")

def REPOSITORIES = [
'intel-innersource/applications.compilers.ide.vsnet.testing',
'intel-innersource/applications.compilers.ide.vsnet.cpp'
]

pipeline {
    agent any

    stages {
        stage('Fetch Open PRs') {
            steps {
                script {
                    REPOSITORIES.each { repo ->
                        println "Fetching open PRs for: ${repo}"

                        def response = githubRequest(url: "repos/${repo}/pulls", mode: "GET")
                        def jsonResponse = readJSON(text: response)

                        if (jsonResponse.size() > 0) {
                            jsonResponse.each { pr ->
                                println "PR: #${pr.number} - ${pr.title} (${pr.html_url})"
                            }
                        } else {
                            println "No open PRs found for ${repo}"
                        }
                    }
                }
            }
        }
    }
}