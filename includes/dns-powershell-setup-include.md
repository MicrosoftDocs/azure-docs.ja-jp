---
title: Azure DNS 用の PowerShell のインクルード ファイル
description: Azure DNS 用の PowerShell のインクルード ファイル
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 32c516ccee3a9f4f7604a3e330285703a776b47d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133124"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Azure DNS 用に Azure PowerShell をセットアップする

### <a name="before-you-begin"></a>開始する前に

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

構成を開始する前に、以下がそろっていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレットの最新版をインストールする必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

さらに、Private Zones (パブリック プレビュー) を使用するには、以下の PowerShell モジュールとバージョンを用意する必要があります。 
* AzureRM.Dns - [バージョン 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) 以上
* AzureRM.Network - [バージョン 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) 以上

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
上記のコマンドの出力で、AzureRM.Dns のバージョンが 4.1.0 以上、AzureRM.Network のバージョンが 5.4.0 以上であることが示される必要があります。  

システムに以前のバージョンがある場合、Azure PowerShell の最新バージョンをインストールできます。または、モジュール バージョンの横にある上記のリンクを使用して、PowerShell ギャラリーから上記のモジュールをダウンロードしてインストールできます。 次に、以下のコマンドを使用してこれらをインストールできます。 どちらのモジュールも必要で、完全な下位互換性があります。 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

PowerShell コンソールを開き、アカウントに接続します。 詳細については、[AzureRM を使用したサインイン](/powershell/azure/azurerm/authenticate-azureps)に関するページを参照してください。

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>サブスクリプションの選択
 
アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

使用する Azure サブスクリプションを選択します。

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>リソース プロバイダーの登録

Azure DNS サービスは Microsoft.Network リソース プロバイダーによって管理されます。 Azure DNS を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。 この操作は、サブスクリプションごとに 1 回だけ実行します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
