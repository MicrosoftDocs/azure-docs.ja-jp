---
title: PowerShell を使用した Azure CDN の管理 | Microsoft Docs
description: Azure PowerShell コマンドレットを使用して、Azure CDN を管理する方法について説明します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 5dc05b08e84decf958481d19b9cfb820947fae78
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237046"
---
# <a name="manage-azure-cdn-with-powershell"></a>PowerShell を使用した Azure CDN の管理
PowerShell により、Azure CDN プロファイルとエンドポイントを柔軟に管理することができます。  この PowerShell は対話的に使用することも、スクリプトを記述して、管理タスクを自動化することもできます。  このチュートリアルでは、Azure CDN のプロファイルとエンドポイントを管理するために、PowerShell で実行できる一般的なタスクをいくつか紹介します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用して Azure CDN のプロファイルとエンドポイントを管理するには、Azure PowerShell モジュールをインストールする必要があります。  `Connect-AzAccount` コマンドレットを使用して Azure PowerShell をインストールし、Azure に接続する方法については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

> [!IMPORTANT]
> Azure PowerShell コマンドレットを実行する前に、`Connect-AzAccount` でログインする必要があります。
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Azure CDN コマンドレットの一覧表示
Azure CDN コマンドレットの一覧を表示するには、 `Get-Command` コマンドレットを使用します。

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>ヘルプの表示
ヘルプを表示するには、このコマンドレットのいずれかで `Get-Help` コマンドレットを使用します。  `Get-Help` を実行すると、利用状況と構文のほか、必要に応じて例を表示することができます。

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>既存の Azure CDN プロファイルを一覧表示
パラメーターを指定せずに `Get-AzCdnProfile` コマンドレットを実行すると、既存の CDN プロファイルすべてを取得できます。

```powershell
Get-AzCdnProfile
```

この出力は、列挙のコマンドレットにパイプ処理できます。

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

また、プロファイル名とリソース グループを指定して、1 つのプロファイルを返すこともできます。

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> 複数のリソース グループがある場合は、それぞれのグループに同じ名前の CDN プロファイルが存在する可能性があります。  `ResourceGroupName` パラメーターを省略すると、名前が一致するすべてのプロファイルが返されます。
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>既存の CDN エンドポイントの一覧表示
`Get-AzCdnEndpoint` では、エンドポイントを個別に取得することも、プロファイルのすべてのエンドポイントを取得することもできます。  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの作成
`New-AzCdnProfile` および `New-AzCdnEndpoint` は、CDN プロファイルとエンドポイントを作成するときに使用します。 次の SKU がサポートされています。
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>エンドポイント名の可用性の確認
`Get-AzCdnEndpointNameAvailability` は、エンドポイント名を使用できるかどうかを示すオブジェクトを返します。

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>カスタム ドメインの追加
`New-AzCdnCustomDomain` は、カスタム ドメイン名を既存のエンドポイントに追加します。

> [!IMPORTANT]
> 「[Content Delivery Network (CDN) エンドポイントにカスタム ドメインをマップする方法](cdn-map-content-to-custom-domain.md)」の説明に従って、DNS プロバイダーで CNAME を設定する必要があります。  エンドポイントを変更する前に、 `Test-AzCdnCustomDomain`を使用してマッピングをテストできます。
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>エンドポイントの変更
`Set-AzCdnEndpoint` で既存のエンドポイントを変更します。

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>CDN 資産の消去/事前読み込み
`Unpublish-AzCdnEndpointContent` は、キャッシュされた資産を消去します。また、`Publish-AzCdnEndpointContent` は、サポートされているエンドポイントで、資産を事前に読み込みます。

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>CDN エンドポイントの開始/停止
`Start-AzCdnEndpoint` および `Stop-AzCdnEndpoint` を使用すると、個別のエンドポイントまたはエンドポイント グループを開始および停止できます。

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>CDN リソースの削除
`Remove-AzCdnProfile` および `Remove-AzCdnEndpoint` を使用すると、プロファイルとエンドポイントを削除できます。

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>次の手順
[.NET](cdn-app-dev-net.md) または [Node.js](cdn-app-dev-node.md) を使用して Azure CDN を自動化する方法について学習します。

CDN 機能の詳細については、[CDN の概要](cdn-overview.md)に関するページをご覧ください。

