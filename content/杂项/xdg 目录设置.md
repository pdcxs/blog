---
tag:
  - xdg
---

## 设置脚本 (make_xdg.ps1)

```powershell
Add-Type -AssemblyName System.Windows.Forms

# select xdg path
$dialog = New-Object System.Windows.Forms.FolderBrowserDialog
$dialog.Description = "请选择用于创建XDG目录的根路径"
$dialog.ShowNewFolderButton = $true

if ($dialog.ShowDialog() -ne [System.Windows.Forms.DialogResult]::OK)
{
  [void][System.Windows.Forms.MessageBox]::Show("请选择一个目录！", "错误", "OK", "Error")
  exit 1
}

$basePath = $dialog.SelectedPath

$folders = @("bin", "cache", "config", "config_share", "data", "data_share", "runtime", "state")

foreach ($folder in $folders)
{
  $fullPath = Join-Path -Path $basePath -ChildPath $folder
  if (-not (Test-Path $fullPath))
  {
    New-Item -ItemType Directory -Path $fullPath | Out-Null
    Write-Host "已创建目录: $fullPath"
  } else
  {
    Write-Host "目录已存在: $fullPath"
  }
}

Write-Output "正在设置环境变量"

Write-Output "将 XDG_BIN_HOME 设置为 $(Join-Path $basePath 'bin')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_BIN_HOME' -Value (Join-Path $basePath 'bin')
Write-Output "设置完成`n将 XDG_CACHE_HOME 设置为 $(Join-Path $basePath 'cache')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_CACHE_HOME' -Value (Join-Path $basePath 'cache')
Write-Output "设置完成`n将 XDG_CONFIG_HOME 设置为 $(Join-Path $basePath 'config')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_CONFIG_HOME' -Value (Join-Path $basePath 'config')
Write-Output "设置完成`n将 XDG_CONFIG_DIR 设置为 $(Join-Path $basePath 'config_share')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_CONFIG_DIRS' -Value (Join-Path $basePath 'config_share')
Write-Output "设置完成`n将 XDG_DATA_HOME 设置为 $(Join-Path $basePath 'data')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_DATA_HOME' -Value (Join-Path $basePath 'data')
Write-Output "设置完成`n将 XDG_DATA_DIR 设置为 $(Join-Path $basePath 'data_share')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_DATA_DIR' -Value (Join-Path $basePath 'data_share')
Write-Output "设置完成`n将 XDG_RUNTIME_DIR 设置为 $(Join-Path $basePath 'runtime')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_RUNTIME_DIR' -Value (Join-Path $basePath 'runtime')
Write-Output "设置完成`n将 XDG_STATE_HOME 设置为 $(Join-Path $basePath 'state')"
Set-ItemProperty -Path HKCU:\Environment -Name 'XDG_STATE_HOME' -Value (Join-Path $basePath 'state')
Write-Output "设置完成`n"

$dialog.Dispose()
```
