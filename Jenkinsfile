pipeline {
  agent any
    stages {
      stage('ERP_B-1_RestoreNuget') {
        steps {
          powershell '''
$workspace = pwd
. $workspace\\build\\nuget.exe restore $workspace\\GCRADC.sln
          '''
        }
      }
    }
  }
}
