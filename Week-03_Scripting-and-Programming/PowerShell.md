
# PowerShell for Pentesters

## Table of Contents
- [Introduction](#introduction)
- [First Script](#first-script)
- [Second Script (Conditions)](#second-script-conditions)
- [Third Script (Ping Sweep)](#third-script-ping-sweep)
- [Fourth Script (Port Scanner)](#fourth-script-port-scanner)
- [Fifth Script (Working with Web)](#fifth-script-working-with-web)
- [Reminder: Running Scripts](#reminder-running-scripts)
- [Benefits of Windows PowerShell ISE](#benefits-of-windows-powershell-ise)

---

## Introduction

Basic PowerShell concepts for pentesters and automation on Windows systems.

```powershell
echo "Desec Security"             # Writes "Desec Security" on screen
Write-Host "Desec Security"       # Same effect
Write-Output "Desec Security"     # Same effect

notepad script.ps1                # Opens notepad to create a .ps1 script
```

---

## First Script

Basic PowerShell script example using parameters and user input.

```powershell
# My First Script
param(
    [string]$ip = "192.168.0.1"
)

echo "Current Directory: $(Get-Location)"
echo "Current User: $(whoami)"
echo "Scanning Host: $ip"

$mensagem = Read-Host "Type a message"
echo "Your message: $mensagem"
```

---

## Second Script (Conditions)

Examples of if-else and simple decision making.

```powershell
# Conditions
$age = Read-Host "What is your age?"
if ($age -ge 18) {
    echo "You are an adult"
} else {
    echo "You are underage"
}

$color = Read-Host "What's the traffic light color?"
if ($color -eq "green") {
    echo "GO!"
} elseif ($color -eq "yellow") {
    echo "WAIT!"
} else {
    echo "STOP!"
}
```

---

## Third Script (Ping Sweep)

Simple ping sweep using parameterized network base.

```powershell
# Ping Sweep
param($p1)
if (!$p1) {
    echo "Desec Security"
    echo "Usage Example: .\script.ps1 192.168.0"
} else {
    foreach ($ip in 1..254) {
        try {
            $resp = ping -n 1 "$p1.$ip" | Select-String "bytes=32"
            $resp.Line.split(' ')[2] -replace ":",""
        } catch {}
    }
}
```

---

## Fourth Script (Port Scanner)

Quick port scanner for top common ports.

```powershell
# Port Scanner
param($ip)
if (!$ip) {
    echo "DESEC SECURITY - PORTSCAN"
    echo "Usage Example: .\portscan.ps1 192.168.0.1"
}

$topports = 21,22,3306,80,443
try {
    foreach ($port in $topports) {
        if (Test-NetConnection $ip -Port $port -WarningAction SilentlyContinue -InformationLevel Quiet) {
            echo "Port $port is OPEN"
        } else {
            echo "Port $port is CLOSED"
        }
    }
} catch {}
```

---

## Fifth Script (Working with Web)

Making HTTP requests, discovering server technologies and parsing HTML links.

```powershell
# Working with WEB
$site = Read-Host "Type the website URL"
$web = Invoke-WebRequest -Uri "$site" -Method Options

echo "Server technology:"
$web.Headers.Server

echo "Allowed HTTP methods:"
$web.Headers.Allow

echo ""
echo "Links found on the page:"
$web2 = Invoke-WebRequest -Uri "$site"
$web2.Links.Href | Select-String "http://"
```

---

## Reminder: Running Scripts

Run scripts using:
```powershell
.\scriptname.ps1
```

---

## Benefits of Windows PowerShell ISE

- Syntax highlighting and script debugging
- Ability to run line by line or the full script
- Multi-tab interface for editing multiple scripts
- Integrated console for immediate feedback
- Much easier for editing and testing than notepad

---
