---
title: "仮想マシン拡張機能でアプリケーションのデプロイを自動化する | Microsoft Docs"
description: "Azure Virtual Machines DotNet Core チュートリアル"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 79c91304-6c1b-4354-a185-fecc129b139d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 2d60af167b8d7805e6f01264de84fb1351d85f98
ms.lasthandoff: 03/16/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-windows-vms"></a>Windows VM での Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ

すべての Azure インフラストラクチャの要件を特定し、デプロイ テンプレートに変換したら、実際のアプリケーションのデプロイに対処する必要があります。 ここでは、アプリケーションのデプロイは、実際のアプリケーション バイナリを Azure リソースにインストールすることを指します。 ミュージック ストア サンプルでは、.Net Core と IIS を各仮想マシンにインストールして構成する必要があります。 ミュージック ストアのバイナリを仮想マシンにインストールし、ミュージック ストア データベースを事前に作成する必要があります。

このドキュメントでは、仮想マシン拡張機能で Azure 仮想マシンへのアプリケーションのデプロイと構成を自動化する方法について説明します。 すべての依存関係と固有の構成に焦点を当てます。 最善の結果を得るために、ソリューションのインスタンスを Azure サブスクリプションに事前にデプロイし、Azure Resource Manager テンプレートを手元に用意して取り組んでください。 完全なテンプレートは、こちら ([Windows のミュージック ストア デプロイ](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)) にあります。

## <a name="configuration-script"></a>構成スクリプト
仮想マシン拡張機能は、仮想マシンに対して実行される特殊なプログラムで、構成を自動化します。 拡張機能は、ウイルス対策、ログの構成、Docker の構成など、特定のさまざまな目的に使うことができます。 カスタム スクリプト拡張機能を使うと、仮想マシンに対して任意のスクリプトを実行できます。 ミュージック ストア サンプルでは、カスタム スクリプト拡張機能によって、Windows 仮想マシンが構成され、ミュージック ストア アプリケーションがインストールされます。

Azure Resource Manager テンプレートで仮想マシン拡張機能を宣言する方法を説明する前に、実行されるスクリプトを確認します。 このスクリプトでは、ミュージック ストア アプリケーションをホストする Windows 仮想マシンを構成します。 スクリプトを実行すると、必要なソフトウェアがすべてインストールされ、ソース管理からミュージック ストア アプリケーションがインストールされ、データベースが準備されます。 

> このサンプルは、デモンストレーション用です。

```PowerShell
<#
    .SYNOPSIS
        Downloads and configures .Net Core Music Store application sample across IIS and Azure SQL DB.
#>

Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# Firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# Folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# Install iis
Install-WindowsFeature web-server -IncludeManagementTools

# Install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# Download music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music

# Azure SQL connection sting in environment variable
[Environment]::SetEnvironmentVariable("Data:DefaultConnection:ConnectionString", "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30",[EnvironmentVariableTarget]::Machine)

# Pre-create database
$env:Data:DefaultConnection:ConnectionString = "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30"
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# Configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>VM スクリプト拡張機能
VM 拡張機能をビルド時に仮想マシンに対して実行するには、拡張機能リソースを Azure Resource Manager テンプレートに含めます。 拡張機能を追加するには、Visual Studio のリソースの追加ウィザードを使うか、有効な JSON をテンプレートに挿入します。 スクリプト拡張機能リソースは仮想マシン リソース内に入れ子になっています。これは、次の例で確認できます。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [VM スクリプト拡張機能](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339)) をご覧ください。 

以下の JSON では、スクリプトが GitHub に格納されていることに注目してください。 このスクリプトは、Azure Blob Storage に格納することもできます。 また、Azure Resource Manager テンプレートでは、スクリプト実行文字列を作成して、テンプレート パラメーター値をスクリプト実行のパラメーターとして使うこともできます。 その場合は、テンプレートのデプロイ時にデータを指定し、スクリプトの実行時にこれらの値を使うことができます。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

前述のように、カスタム スクリプトは Azure Blob Storage に格納することもできます。 スクリプト リソースを Blob Storage に格納する場合、2 つの方法があります。1 つは、コンテナー/スクリプトをパブリックにし、上記の方法に従います。もう 1 つの方法として、プライベートの Blob Storage を保持することもできます。この場合、CustomScriptExtension リソース定義に storageAccountName と storageAccountKey を指定する必要があります。

次の例では、もう一歩踏み込んでいます。 ストレージ アカウント名とストレージ アカウント キーは、デプロイ時にパラメーターまたは変数として指定できますが、Resource Manager テンプレートには、ストレージ アカウント キーをプログラムで取得し、デプロイ時にテンプレートに自動的に挿入する `listKeys` 関数が用意されています。

次の CustomScriptExtension リソース定義の例では、`mysa999rgname` という別のリソース グループに存在する `mystorageaccount9999` という Azure ストレージ アカウントにカスタム スクリプトが既にアップロードされています。 このリソースを含むテンプレートをデプロイすると、`listKeys` 関数によって、リソース グループ `mysa999rgname` に存在するストレージ アカウント `mystorageaccount9999` のストレージ アカウント キーがプログラムで取得され、テンプレートに自動的に挿入されます。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://mystorageaccount9999.blob.core.windows.net/container/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]",
      "storageAccountName": "mystorageaccount9999",
      "storageAccountKey": "[listKeys(resourceId('mysa999rgname','Microsoft.Storage/storageAccounts', mystorageaccount9999), '2015-06-15').key1]"
    }
  }
}
```

この方法の主な利点は、ストレージ アカウント キーが変更された場合に、テンプレートやデプロイ パラメーターを変更する必要がないことです。

カスタム スクリプト拡張機能の使用について詳しくは、 [Resource Manager テンプレートでのカスタム スクリプト拡張機能](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

## <a name="next-step"></a>次のステップ
<hr>

[その他の Azure Resource Manager テンプレートを確認する](https://github.com/Azure/azure-quickstart-templates)


