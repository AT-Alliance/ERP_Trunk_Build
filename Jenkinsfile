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
              powershell '''
$DirectoryToPurge="$($env:DirectoryToPurgeEnv)"
#$DirectoryToPurge = "C:\\LivrablesAll_dotnet\\Common"
$ExcludeFolder="$($env:ExcludeFolderEnv)"
#$excludeFolder = "SvnFolderForDelivery"
$count=0

Try {
    if ( Test-Path $($DirectoryToPurge) ) {
	    $getAllFilesLivrableDirectory=(gci $DirectoryToPurge | Where-Object { $_.Name -ne "$($ExcludeFolder)" })
	    $getAllFilesLivrableDirectory |%{
		    Remove-Item $($_.Fullname) -Recurse -Force
		    "Item \'$($_.Fullname)\' deleted"
		    $count++
	    }
	    "---"
	    "$($count) item(s) removed in \'$($DirectoryToPurge)\'"
        if ( $ExcludeFolder -ne $null ) {
            "Folder \'$ExcludeFolder\' exluded !!"
        }
	    "---"
        "Purge \'$DirectoryToPurge\' success!!"
        
    }
} catch {
    "Purge \'$DirectoryToPurge\' failed: $_"
}
'''
            }
          }
        }
	     
      }
    }
  }
}
