# Bulk git mv

Wenn ein größerer struktureller Umbau einer Solution ansteht, möchte man trotzdem hinterher auf die Git-Historie zurückgreifen können. Um ganze Projekte im Repository zu verschieben, habe ich folgendes Script geschrieben. Dabei kann das Array beliebig um Projekte erweitert werden.

```powershell
$table = @(
    @{sourceFolder = "versionservice"      ; targetFolder = "src"    ; csproj = "MelanieEibl.WebApp.VersionService" }
)

function gitmove {
    param (
        [Parameter()]
        [psobject]
        $file,
        [Parameter()]
        [psobject]
        $item
    )

    $sourceFolder = $item.sourceFolder
    $targetFolder = $item.targetFolder
    $csproj = $item.csproj

    $source = $file.FullName

    $targetDir = $file.Directory.FullName.Replace("\$sourceFolder", "\$targetFolder\$csproj")

    # $target = $file.FullName.Replace("\$sourceFolder", "\$targetFolder\$csproj")

    If (!(Test-Path -PathType container $targetDir)) {
        New-Item -ItemType Directory -Path $targetDir
    }

    if ($file.FullName.EndsWith(".csproj")) {
        # $target = $file.Directory.FullName + "\" + $csproj + ".csproj"
        # $file.Name
        $target = $targetDir + "\" + $csproj + ".csproj"
    }
    else {
        $target = $file.FullName.Replace("\$sourceFolder\", "\$targetFolder\$csproj\")
        # $target = $targetDir + "\" + $file.FullName
    }
        
    # Write-Host "$source            $target"
    git mv -v $source $target
}

try {
    foreach ($item in $table) {
        $sourceFolder = $item.sourceFolder
        foreach ($file in Get-ChildItem .\$sourceFolder -Recurse -File) { 
            gitmove -file $file -item $item
        }
    }

    # (Get-ChildItem -r | Where-Object {$_.PSIsContainer -eq $True}) | Where-Object {$_.GetFiles().Count -eq 0} | Select-Object FullName

    foreach ($folder in $((Get-ChildItem -r | Where-Object { $_.PSIsContainer -eq $True }))) {
        #         | Where-Object { $_.GetFiles().Count -eq 0 } | Select-Object FullName))
        # Write-Host $folder.FullName
        if (Test-Path -Path $folder.FullName) {
            if ($(Get-ChildItem $folder.FullName -File -Recurse).Count -eq 0) {
                # if ($(Get-ChildItem $folder.FullName -File -Recurse) -eq 0) {
                Write-Host "to delete $folder"
                # }    
                Remove-Item $folder.FullName -Recurse
            }
        }
    }
}
catch {
    Write-Host $file
    Write-Host $_
}
```