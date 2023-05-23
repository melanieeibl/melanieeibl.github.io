# Automatisches Laden von PowerShell Modulen mit PowerShell Profilen

Wer PowerShell häufig interaktiv auf der Konsole verwendet, kennt es wahrscheinlich: Immer wieder muss man Module mit `Import-Module` laden, um sie anschließend verwenden zu können. Durch den Eintrag des `Import-Module` Statements in ein PowerShell Profil, können Module automatisch importiert werden.

Entwickler, die git verwenden, kennen dies häufig schon von posh-git. Genau so funktioniert das aber auch mit selbst geschriebenen Script- und Binary-Modulen.

Man muss sich nun überlegen, in welchem Kontext man Module verwenden möchte: Nur für den aktuellen Benutzer, oder für alle Benutzer des Computers? Nur für einen bestimmten PowerShell Host, wie VS Code oder PowerShell-Konsole oder für alle Anwendungen, die PowerShell hosten? Entsprechend der gewählten Option wird die zugehörige Profildatei angepasst.

Um die Pfade zu den Profildateien zu ermitteln, gibt man Folgendes ein:

All Users, All Hosts
```powershell
$Profile.AllUsersAllHosts
```

All Users, Current Host
```powershell
$Profile.AllUsersCurrentHost
```

Current User, All Hosts
```powershell
$Profile.CurrentUserAllHosts
```

Current user, Current Host
```powershell
$Profile.CurrentUserCurrentHost
```

Um sich die Inhalte aller vom aktuellen Host referenzierten Profile anzeigen zu lassen, kann man folgendes Script verwenden:

```powershell
if (Test-Path -Path $profile)
{Get-Content -Path $profile -Force}
if (Test-Path -Path $Profile.CurrentUserCurrentHost)
{Get-Content -Path $Profile.CurrentUserCurrentHost -Force}
if (Test-Path -Path $Profile.CurrentUserAllHosts)
{Get-Content -Path $Profile.CurrentUserAllHosts -Force}
if (Test-Path -Path $Profile.AllUsersCurrentHost)
{Get-Content -Path $Profile.AllUsersCurrentHost -Force}
if (Test-Path -Path $Profile.AllUsersAllHosts)
{Get-Content -Path $Profile.AllUsersAllHosts -Force}
```

## Pester

Beispiel: Setzen der Pester Output-Verbosity in `$Profile.CurrentUserCurrentHost`:

```powershell
if (!(Test-Path -Path $Profile.CurrentUserCurrentHost)) {
    New-Item -ItemType File -Path $Profile.CurrentUserCurrentHost -Force
}

notepad $Profile.CurrentUserCurrentHost
```

```powershell
Import-Module posh-git
Import-Module oh-my-posh
Import-Module Pester
$PesterPreference = [PesterConfiguration]::Default
$PesterPreference.Output.Verbosity = 'Detailed'
```