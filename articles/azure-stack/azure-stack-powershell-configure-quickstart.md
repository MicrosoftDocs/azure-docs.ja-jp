---
title: "PowerShell for Azure Stack のインストールと構成のクイックスタート | Microsoft Docs"
description: "PowerShell for Azure Stack のインストールと構成について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 6e5c420ed80127213e38849ac1999bba199e36c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Azure Stack での PowerShell を使用した準備と実行

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

これは、PowerShell を使用して Azure Stack 環境のインストールと構成を行うクイック スタートの記事です。 この記事で提供するスクリプトは、**Azure Stack オペレーター**のみを対象としています。

この記事は、「[PowerShell をインストールする]( azure-stack-powershell-install.md)」、「[ツールのダウンロード]( azure-stack-powershell-download.md)」、「[Azure Stack オペレーターの PowerShell 環境の構成]( azure-stack-powershell-configure-admin.md)」の各記事で説明した手順の要約バージョンです。 このトピックのスクリプトを使用すると、Azure Active Directory または Active Directory フェデレーション サービス (AD FS) によってデプロイされた Azure Stack 環境のために PowerShell を設定できます。  


## <a name="set-up-powershell-for-aad-based-deployments"></a>AAD ベースのデプロイに向けた PowerShell の設定

Azure Stack Development Kit にサインインするか、VPN 経由で接続している場合は Windows ベースの外部クライアントにサインインします。 管理者特権で PowerShell ISE セッションを開き、次のスクリプトを実行します (環境の構成に従って、必ず TenantName、ArmEndpoint、GraphAudience 変数を更新してください)。

```powershell
# Specify Azure Active Directory tenant name
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $TenantName `
  -EnvironmentName AzureStackAdmin

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>AD FS ベースのデプロイに向けた PowerShell の設定 

Azure Stack Development Kit にサインインするか、VPN 経由で接続している場合は Windows ベースの外部クライアントにサインインします。 管理者特権で PowerShell ISE セッションを開き、次のスクリプトを実行します (環境の構成に従って、必ず ArmEndpoint、GraphAudience 変数を更新してください)。

```powershell

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the cloud administrator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience `
  -EnableAdfsAuthentication:$true

$TenantID = Get-AzsDirectoryTenantId `
  -ADFS `
  -EnvironmentName "AzureStackAdmin"

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="test-the-connectivity"></a>接続のテスト

PowerShell を構成したので、リソース グループを作成して構成をテストすることができます。

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

リソース グループが作成されると、コマンドレット出力のプロビジョニング状態プロパティは ”成功” に設定されています。

## <a name="next-steps"></a>次のステップ

* [CLI のインストールと構成](azure-stack-connect-cli.md)

* [テンプレートの開発](user/azure-stack-develop-templates.md)







