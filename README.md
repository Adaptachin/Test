# Test

import groovy.json.JsonSlurper
import groovy.json.JsonOutput

// GitHub repository details
def owner = "Adaptachin"
def repo = "Test"
def branch = "main" // or the branch where the README is located
def readmePath = "README.md" // or the path to your README file

// GitHub API URL to fetch the README file
def apiUrl = "https://api.github.com/repos/${owner}/${repo}/contents/${readmePath}?ref=${branch}"

// GitHub personal access token (if required)
def githubToken = "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"

// Making the API request
def connection = new URL(apiUrl).openConnection()
connection.setRequestProperty("Authorization", "token ${githubToken}")
connection.setRequestProperty("Accept", "application/vnd.github.v3+json")

if (connection.responseCode == 200) {
    def response = connection.inputStream.text
    def json = new JsonSlurper().parseText(response)
    
    // Decode the base64 content
    def readmeContent = new String(json.content.decodeBase64())
    
    // Render the README content as HTML
    def renderedContent = """
    <div class="github-readme">
        ${readmeContent.replaceAll("\n", "<br/>")}
    </div>
    """
    
    // Output the rendered content
    renderedContent
} else {
    "Failed to fetch README file. HTTP response code: ${connection.responseCode}"
}
