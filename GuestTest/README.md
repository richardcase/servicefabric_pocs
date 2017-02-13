Sample shows deploying a .NET Core application as a guest executable.


# Deploy initial build
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath SFGuestTest.WebAPIType1.0.0 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -ApplicationPackagePathInImageStore "SFGuestTest\_V1"
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "SFGuestTest\_V1"
New-ServiceFabricApplication fabric:/SFGuestTest.SFHost SFGuestTest.SFHostType 1.0.0

# Deploy updated version
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath SFGuestTest.WebAPIType2.0.0  -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))   -ApplicationPackagePathInImageStore "SFGuestTest\_V2"
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "SFGuestTest\_V2"

# Start upgrade
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/SFGuestTest.SFHost -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 1320   -FailureAction Rollback -Monitored -Force

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/SFGuestTest.SFHost -ApplicationTypeVersion 2.0.0 -FailureAction Rollback -UpgradeReplicaSetCheckTimeout 1  -Monitored -Force


# Get status of upgrade
Get-ServiceFabricApplicationUpgrade fabric:/SFGuestTest.SFHost