# hotspot-sh

 
1. Open PowerShell and run:
```
New-Item -ItemType Directory -Path "C:\Scripts" -Force
notepad "C:\Scripts\StartHotspot.ps1"
```

# 2. paste in notepad
```
# Wait for Windows networking to initialize
Start-Sleep -Seconds 20

try {
    # Get the current internet connection
    $profile = [Windows.Networking.Connectivity.NetworkInformation,
        Windows.Networking.Connectivity,
        ContentType=WindowsRuntime]::GetInternetConnectionProfile()

    if ($null -eq $profile) {
        exit 1
    }

    # Create the Mobile Hotspot manager
    $tethering = [Windows.Networking.NetworkOperators.NetworkOperatorTetheringManager,
        Windows.Networking.NetworkOperators,
        ContentType=WindowsRuntime]::CreateFromConnectionProfile($profile)

    # Start hotspot only if it isn't already running
    if ($tethering.TetheringOperationalState -ne "On") {
        $operation = $tethering.StartTetheringAsync()

        # Wait for the asynchronous operation to finish
        $result = $operation.GetAwaiter().GetResult()

        # Log the result
        "$(Get-Date) - Hotspot start result: $($result.Status)" |
            Out-File "C:\Scripts\hotspot.log" -Append
    }
    else {
        "$(Get-Date) - Hotspot already running" |
            Out-File "C:\Scripts\hotspot.log" -Append
    }
}
catch {
    "$(Get-Date) - ERROR: $($_.Exception.Message)" |
        Out-File "C:\Scripts\hotspot.log" -Append
}
```

# 3. test manually 
```
powershell.exe -ExecutionPolicy Bypass -File "C:\Scripts\StartHotspot.ps1"
```

# 4. check log
```
Get-Content "C:\Scripts\hotspot.log"
```

# 5. task.mc
