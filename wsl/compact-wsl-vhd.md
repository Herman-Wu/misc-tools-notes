### Compact WSL VHD 

#### Step 1: Clean files in Ubuntu 

- Disk Usage Analysis 
```bash
sudo apt install baobab
baobab
```
- Clean disk cache: _apt-get & conda_
```bash
sudo apt-get autoclean
sudo apt-get autoremove

pip cache purge


conda clean --all

```

- Clean Disk Tool
```bash
sudo apt-get install bleachbit
sudo bleachbit
```

#### Step 2: Compact VHD

Option 1. DiskPart 
Source: [How to Shrink a WSL2 Virtual Disk](https://stephenreescarter.net/how-to-shrink-a-wsl2-virtual-disk/)
- Use DiskPart to compact VHD
1. Shutdown WSL
```cmd
wsl --shutdown
```
1. RUN diskpart 
2. use following command in diskpart 
```bash
select vdisk file="c:\Users\<user>\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\ext4.vhdx"
compact vdisk
```

Opetion 2. Optimize-VHD
Source: [How do I get back unused disk space from ubuntu on wsl2?](https://superuser.com/questions/1606213/how-do-i-get-back-unused-disk-space-from-ubuntu-on-wsl2)
```bash
# Self-elevate the script if required
if (-Not ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] 'Administrator')) {
    if ([int](Get-CimInstance -Class Win32_OperatingSystem | Select-Object -ExpandProperty BuildNumber) -ge 6000) {
        $CommandLine = "-File `"" + $MyInvocation.MyCommand.Path + "`" " + $MyInvocation.UnboundArguments
        Start-Process -FilePath PowerShell.exe -Verb Runas -ArgumentList $CommandLine
        Exit
    }
}

#Full Path of vhdx file
$d1 = #Ex: "E:\Docker\wsl\data\ext4.vhdx"
$d2 = ""
$d3 = ""
$d4 = ""
$d5 = ""
$d6 = ""

$paths = $d1, $d2, $d3, $d4, $d5, $d6

foreach ($file in $paths) {
    echo ""
    echo "Befor Shrinking File sizes in MB"
    Get-ChildItem -Path $file | Select-Object FullName, @{Name = "Size"; E = { $_.Length / 1MB } }
    echo ""
    echo "---------"
    echo "Shrinking $file"
    echo "---------"

    wsl --shutdown
    Optimize-VHD -Path $file -Mode Full

    echo ""
    echo "After Shrinking File sizes in MB"
    Get-ChildItem -Path $file | Select-Object FullName, @{Name = "Size"; E = { $_.Length / 1MB } }
    echo ""
    echo ""
    echo ""
    echo ""
}

Read-Host -Prompt "Press Enter to exit"

```


