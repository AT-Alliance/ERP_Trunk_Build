pipeline {
    agent any
        stages {
            stage('ERP_B-2_GetLastCommit') {
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
$SvnRepositoryUrl="$($env:SvnRepositoryUrlEnv)"
#$SvnRepositoryUrl = "https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk"
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
    
    try {
      $svn_lastest_commit = "svn_lastest_commit.txt"
      . "$($SvnBin)" info "$($SvnRepositoryUrl)" --username atjenkins --password atjenkins --trust-server-cert-failures="other,unknown-ca,cn-mismatch,expired" | Out-File "$($DestinationDirectory)\\$($svn_lastest_commit)" 
        "Last Commit in \'$($DestinationDirectory)\\$($svn_lastest_commit)\'"
    } catch {
        " Get Last Commit failed: $_"
    }
}
'''
    }
  }
}
    }
}
