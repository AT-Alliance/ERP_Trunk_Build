pipeline {
  agent any
  stages {
    stage('ERP_A_TrunkCheckout') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }
    
    stage('ParallelStage_1') {
      
      parallel {
        stage('ERP_B-1_RestoreNuget') {
          steps {
            bat '"%WORKSPACE%\\build\\nuget.exe" restore "%WORKSPACE%\\GCRADC.sln"'
          }
        }
        
        stage('ERP_B-2_PurgeLivrables') {
	        environment {
            DirectoryToPurgeEnv = 'C:\\Livrables\\All_dotnet'
            ExcludeFolderEnv = 'SvnFolderForDelivery'
          }
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
            "Folder \'$ExcludeFolder\\\' exluded !!"
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
	      
        stage('ERP_B-3_GetLastCommit') {
          environment {
            SvnBinEnv = 'C:\\Program Files\\TortoiseSVN\\bin\\svn'
            SvnRepositoryUrlEnv = 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk'
            BaseOutputRootDirectoryEnv = 'C:\\Livrables'
            BaseOutputDirectoryEnv = 'All_dotnet'
            DestinationDirectoryNameEnv = 'SvnFolderForDelivery'
          }
          steps {
            script {
              powershell '''
$SvnBin="$($env:SvnBinEnv)"
#$SvnBin = "C:\\Program Files\\TortoiseSVN\\bin\\svn"
#$SvnRepositoryUrl="$($env:SvnRepositoryUrlEnv)"
$SvnRepositoryUrl = "https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk"
$BaseOutputRootDirectory="$($env:BaseOutputRootDirectoryEnv)"
#$BaseOutputRootDirectory = "C:\\Livrables"
$BaseOutputRootDirectory="$($env:BaseOutputRootDirectoryEnv)"
#$BaseOutputRootDirectory = "C:\\Livrables"
$BaseOutputDirectory="$($env:BaseOutputDirectoryEnv)"
#$BaseOutputDirectory = "All_dotnet"
$DestinationDirectoryName="$($env:DestinationDirectoryNameEnv)"
#$DestinationDirectoryName = "SvnFolderForDelivery"

$DestinationDirectory = "$($BaseOutputRootDirectory)\\$($BaseOutputDirectory)"

if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
    "`nLe repertoire \'$($DestinationDirectoryName)\' inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
    "-------------------------"
}

$DestinationDirectory = "$($DestinationDirectory)\\$($DestinationDirectoryName)"

if ( Test-Path $($DestinationDirectory) ) {
    
    #try {
    	$svn_lastest_commit = "svn_lastest_commit.txt"
        . "$($SvnBin)" info "$($SvnRepositoryUrl)" --non-interactive --trust-server-cert --username atjenkins --password atjenkins | Out-File "$($DestinationDirectory)\\$($svn_lastest_commit)"
        "Last Commit in \'$($DestinationDirectory)\\$($svn_lastest_commit)\'"
    #} catch {
     #   " Get Last Commit failed: $_"
    #}
}'''
            }
          }
        }
        
        
      }
    }
  }
}
