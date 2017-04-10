---
title: "PowerShell を使用した Azure CDN の管理 | Microsoft Docs"
description: "Azure PowerShell コマンドレットを使用して、Azure CDN を管理する方法について説明します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 109ca4a4672d21969096af26a094390673de25d9
ms.openlocfilehash: 9b0912edc5adf804e7a3e6dcc6b440fc165d3673


---
# <a name="manage-azure-cdn-with-powershell"></a>PowerShell を使用した Azure CDN の管理
PowerShell により、Azure CDN プロファイルとエンドポイントを柔軟に管理することができます。  この PowerShell は対話的に使用することも、スクリプトを記述して、管理タスクを自動化することもできます。  このチュートリアルでは、Azure CDN のプロファイルとエンドポイントを管理するために、PowerShell で実行できる一般的なタスクをいくつか紹介します。

## <a name="prerequisites"></a>前提条件
PowerShell を使用して Azure CDN のプロファイルとエンドポイントを管理するには、Azure PowerShell モジュールをインストールする必要があります。  `Login-AzureRmAccount` コマンドレットを使用して Azure PowerShell をインストールし、Azure に接続する方法については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

> [!IMPORTANT]
> Azure PowerShell コマンドレットを実行する前に、`Login-AzureRmAccount` でログインする必要があります。
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Azure CDN コマンドレットの一覧表示
Azure CDN コマンドレットの一覧を表示するには、 `Get-Command` コマンドレットを使用します。

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>ヘルプの表示
ヘルプを表示するには、このコマンドレットのいずれかで `Get-Help` コマンドレットを使用します。  `Get-Help` を実行すると、利用状況と構文のほか、必要に応じて例を表示することができます。

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>既存の Azure CDN プロファイルを一覧表示
パラメーターを指定せずに `Get-AzureRmCdnProfile` コマンドレットを実行すると、既存の CDN プロファイルすべてを取得できます。

```powershell
Get-AzureRmCdnProfile
```

この出力は、列挙のコマンドレットにパイプ処理できます。

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

また、プロファイル名とリソース グループを指定して、1 つのプロファイルを返すこともできます。

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> 複数のリソース グループがある場合は、それぞれのグループに同じ名前の CDN プロファイルが存在する可能性があります。  `ResourceGroupName` パラメーターを省略すると、名前が一致するすべてのプロファイルが返されます。
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>既存の CDN エンドポイントの一覧表示
`Get-AzureRmCdnEndpoint` では、エンドポイントを個別に取得することも、プロファイルのすべてのエンドポイントを取得することもできます。  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの作成
`New-AzureRmCdnProfile` および `New-AzureRmCdnEndpoint` は、CDN プロファイルとエンドポイントを作成するときに使用します。

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>エンドポイント名の可用性の確認
`Get-AzureRmCdnEndpointNameAvailability` は、エンドポイント名を使用できるかどうかを示すオブジェクトを返します。

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>カスタム ドメインの追加
`New-AzureRmCdnCustomDomain` は、カスタム ドメイン名を既存のエンドポイントに追加します。

> [!IMPORTANT]
> 「[Content Delivery Network (CDN) エンドポイントにカスタム ドメインをマップする方法](cdn-map-content-to-custom-domain.md)」の説明に従って、DNS プロバイダーで CNAME を設定する必要があります。  エンドポイントを変更する前に、 `Test-AzureRmCdnCustomDomain`を使用してマッピングをテストできます。
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>エンドポイントの変更
`Set-AzureRmCdnEndpoint` で既存のエンドポイントを変更します。

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>CDN 資産の消去/事前読み込み
`Unpublish-AzureRmCdnEndpointContent` は、キャッシュされた資産を消去します。また、`Publish-AzureRmCdnEndpointContent` は、サポートされているエンドポイントで、資産を事前に読み込みます。

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>CDN エンドポイントの開始/停止
`Start-AzureRmCdnEndpoint` および `Stop-AzureRmCdnEndpoint` を使用すると、個別のエンドポイントまたはエンドポイント グループを開始および停止できます。

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>CDN リソースの削除
`Remove-AzureRmCdnProfile` および `Remove-AzureRmCdnEndpoint` を使用すると、プロファイルとエンドポイントを削除できます。

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>次のステップ
[.NET](cdn-app-dev-net.md) または [Node.js](cdn-app-dev-node.md) を使用して Azure CDN を自動化する方法について学習します。

CDN 機能の詳細については、[CDN の概要](cdn-overview.md)に関するページをご覧ください。




<!--HONumber=Dec16_HO2-->


