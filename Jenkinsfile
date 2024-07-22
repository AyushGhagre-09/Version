properties([
    parameters([
        string(name: 'RELEASE_VERSION', description: 'The release version to set')
    ])
])

node {

	
    try {

		if(params.RELEASE_VERSION.isEmpty())
		{
			echo 'Enter the release version '
			return 
		}
        stage("checkout") {
           
            bat 'git checkout main'
            bat 'git branch'
            bat 'git pull'
        }

        stage('Update Release Version') {
            def fileContent = readFile('_MasterProductVersion.tt')
            print("original file content : ${fileContent}")

            def updatedContent = fileContent.replaceAll(/const ushort ReleaseVersion = \d+;/, "const ushort ReleaseVersion = ${params.RELEASE_VERSION};")
            print("updated file content : ${updatedContent}")

            // Write the updated content back to the file
            writeFile(file: '_MasterProductVersion.tt', text: updatedContent)
        }

        stage('Commit and Push Changes') {
            bat "git add _MasterProductVersion.tt"
            bat "git commit -m \"bumped the release version to ${params.RELEASE_VERSION}\" || exit /B 0"
            
            withCredentials([string(credentialsId: 'git-pat', variable: 'GITHUB_TOKEN')]) {
                bat 'git push -u https://AyushGhagre-09:%GITHUB_TOKEN%@github.com/AyushGhagre-09/Version.git'
            }
        }

        stage('Trigger Downstream Job') {
            // Trigger the downstream job Agent/Synthetic/develop
            build job: 'Synthetic'
        }
    } catch (Exception e) {
        echo "Encountered the exception: ${e.toString()}"
       
    }
}
