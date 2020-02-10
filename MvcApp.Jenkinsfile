pipeline
{
	agent
	{
		node
		{
			label 'master'
		}
	}
	
	environment
	{
		Major_Version= 1
		Minor_version= 0
		repo= "https://github.com/anshand19/MvcApp-1"
		
	}
	
	stages
	{
		stage('Setup')
		{
			steps
			{
				script
				{
					PackageVersion= "${env.Major_Version}" + "." + "${env.Minor_version}" + "." + "{env.BUILD_NUMBER}"
					currentBuild.displayName= PackageVersion
				}
			}
		}
		
		stage('Checkout')
		{
			steps
			{
				git credentialsId: 'AnandShivaSingh', url: "${repo}" , branch: 'master'
			}
		}
		
		stage('Nugget Restore')
		{
			steps
			{
				script
				{
					bat label: '' ,script: nuget.exe restore MvcApp.sln
				}
			}
		}
		
		stage('Set Artifact Build number')
		{
			steps
			{
				script
				{
					powershell """
					write-host Update file
					(Get-Content MvcApp\\MvcApp.nuspec) -replace 'name', '$PackageVersion' | Out-File MvcApp\\MvcApp.nuspec 
					"""
				}
			}
		}
		
		stage('Build')
		{
			steps
			{
				script
				{
					bat "\"${tool 'MSBuild'}\" $WORKSPACE\\MvcApp.sln /p:Configuration=Release /p:DeployonBuild=true /p:PublishProfile=MvcApp
				}
			}
		}
		
		stage('Package')
		{
			steps
			{
				script
				{
					powershell """
					Move-Item -Path "Deployments\\MvcApp\\" -Destination "MvcApp\\"
					"""
					filename = "MvcApp." + currentBuild.displayName + ".nupkg"	
				}
			}
		}
		
	}
	
	post
	{
		always
        {
            echo "========== ALWAYS POST BUILD =========="
        }

        success
        {
			script
			{
				currentBuild.result = 'SUCCESS'
				emailext body: 'You will receive this email if the build is successful for MvcApp', subject: 'Build', to: 'anshikatyagi19@gmail.com, anandshivasingh@gmail.com'
			}
		}
		 failure
        {
            script
			{
                currentBuild.result = 'FAILURE'
                echo "========== BUILD FAILED =========="
            }
         }
    }
  
	}
	
}