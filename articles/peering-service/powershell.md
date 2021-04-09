---
title: 'Peering Service 接続を登録する - Azure PowerShell '
description: このチュートリアルでは、PowerShell を使用して Peering Service 接続を登録する方法について説明します。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: a12bfa042c8741814cedc70f1dcb67dedbfd331e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400436"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Peering Service 接続を登録する

このチュートリアルでは、Azure PowerShell を使用して Peering Service 接続を登録する方法について説明します。

Azure Peering Service は、Microsoft 365、Dynamics 365、サービスとしてのソフトウェア (SaaS) サービス、Azure、パブリック インターネット経由でアクセス可能な Microsoft サービスなど、Microsoft クラウド サービスへのお客様の接続を強化するネットワーク サービスです。 この記事では、Azure PowerShell を使用して Peering Service 接続を登録する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、すぐに[アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに PowerShell をインストールしてローカルで使用する場合、このクイック スタートでは Azure PowerShell モジュール バージョン 1.0.0 以降を使用する必要があります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 インストールおよびアップグレードについては、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

最後に、PowerShell をローカルで実行している場合は、`Connect-AzAccount` も実行する必要があります。 このコマンドで、Azure との接続が作成されます。

Azure PowerShell モジュールを使用して、Peering Service を登録および管理します。 Peering Service は、PowerShell コマンド ラインまたはスクリプトから登録または管理できます。


## <a name="prerequisites"></a>前提条件  
次のものが必要です。

### <a name="azure-account"></a>Azure アカウント

有効かつアクティブな Microsoft Azure アカウントが必要です。 Peering Service 接続を設定するには、このアカウントが必須です。 Peering Service は、Azure サブスクリプション内のリソースです。

### <a name="connectivity-provider"></a>接続プロバイダー

インターネット サービス プロバイダーまたはインターネット交換パートナーと協力して Peering Service を取得することで、お使いのネットワークを Microsoft ネットワークに接続することができます。

接続プロバイダーが Microsoft と提携していることを確認してください。

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>サブスクリプションをリソースプロバイダーと機能フラグに登録する

Peering Service を登録する手順に進む前に、Azure PowerShell を使用して、お使いのサブスクリプションをリソースプロバイダーと機能フラグに登録します。 Azure PowerShell のコマンドは、次のように指定します。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>場所とサービス プロバイダーを取得する 

Azure PowerShell で次のコマンドを実行して、Peering Service を有効にする場所とサービス プロバイダーを取得します。 

次のように、Peering Service の場所を取得します。

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

次のように、Peering Service プロバイダーを取得します。
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Peering Service 接続を登録する

Azure PowerShell で次のコマンド セットを使用して、Peering Service 接続を登録します。 この例では、myPeeringService という名前の Peering Service を登録します。

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Peering Service のプレフィックスを登録する

Azure PowerShell を使用して次のコマンドを実行し、接続プロバイダーから提供されるプレフィックスを登録します。 この例では、myPrefix という名前のプレフィックスを登録します。

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>すべての Peering Service 接続を一覧表示する

すべての Peering Service の一覧を表示するには、次のコマンドを実行します。

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>すべての Peering Service のプレフィックスを一覧表示する

すべての Peering Service のプレフィックスの一覧を表示するには、次のコマンドを実行します。

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Peering Service のプレフィックスを削除する

Peering Service のプレフィックスを削除するには、次のコマンドを実行します。

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>次のステップ

- Peering Service 接続の詳細については、「[Peering Service 接続](connection.md)」を参照してください。
- Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。
- Azure portal を使用して Peering Service 接続を登録する方法については、[Azure portal を使用した Peering Service 接続の登録](azure-portal.md)に関するページを参照してください。
- Azure CLI を使用して Peering Service 接続を登録する方法については、[Azure CLI を使用した Peering Service 接続の登録](cli.md)に関するページを参照してください。
