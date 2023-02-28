pipeline {
  agent any
  stages {
    stage('ERP_A_TrunkCheckout') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }
    
    stage('ParallelStage_1') {
      environment {
        DirectoryToPurgeEnv = 'C:\\Livrables\\All_dotnet'
	      ExcludeFolderEnv = 'SvnFolderForDelivery'
      }
      parallel {
        stage('ERP_B-1_RestoreNuget') {
          steps {
            bat '"%WORKSPACE%\\build\\nuget.exe" restore "%WORKSPACE%\\GCRADC.sln"'
          }
        }
      
        stage('ERP_B-2_PurgeLivrables') {
          steps {
            script {
              try {
                powershell '''
$DirectoryToPurgeEnv="$($env:DirectoryToPurgeEnv)"
$ExcludeFolderEnv="$($env:ExcludeFolderEnv)"
$count=0
#Creer le repertoire de base du livrable s\'il n\'existe pas
Try {
    if ( Test-Path $($DirectoryToPurge) ) {
	    $getAllFilesLivrableDirectory=(gci $DirectoryToPurge | Where-Object { $_.Name -ne "$($ExcludeFolder)" })
	    $getAllFilesLivrableDirectory |%{
		    Remove-Item $($_.Fullname) -Recurse -Force
		    "Item \'$($_.Fullname)\' deleted"
		    $count++
	    }
	    "---"
	    "$($count) item(s) purgés dans \'$($DirectoryToPurge)\'"
	    "---"		  
    }
} catch {
    "An error occurred: $_"
}
'''
                println "Purge \'$DirectoryToPurgeEnv\' success!!"
              } catch (err){
                println "Purge \'$DirectoryToPurgeEnv\' failed: ${err}!!"
              }
            }
          }
        }
	    
    }
  }
}
