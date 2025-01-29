library("common_lib")

def REPOSITORIES = [
'intel-innersource/applications.compilers.ide.vsnet.testing',
'intel-innersource/applications.compilers.ide.vsnet.cpp'
]

pipeline {
    agent none

    stages {
        stage('Fetch Open PRs') {
            steps {
                script {
                    REPOSITORIES.each { repo ->
                        println "Fetching open PRs for: ${repo}"

                        def response = gitlib.githubRequest(url: "repos/${repo}/pulls", mode: "GET")
                        def jsonResponse = readJSON text: response.content
                        
                        if (jsonResponse.size() > 0) {
                            jsonResponse.each { pr ->
                                if (!pr.draft) {
                                    echo "PR: #${pr.number} - ${pr.title} \n(${pr.html_url})"
                                }
                            }
                        } else {
                            echo "No open PRs found for ${repo}"
                        }
                    }
                }
            }
        }
    }
}