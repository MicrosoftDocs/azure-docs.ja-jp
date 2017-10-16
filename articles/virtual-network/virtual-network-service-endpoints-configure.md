---
title: "Azure Virtual Network サービス エンドポイントの構成 | Microsoft Docs"
description: "仮想ネットワークからサービス エンドポイントを有効または無効にする方法について"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: 9c953e697d5d1c6e53df8eaf72c4e4fbd83ca286
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-virtual-network-service-endpoints"></a>仮想ネットワーク サービス エンドポイントの構成

仮想ネットワーク (VNet) サービス エンドポイントを使用すると、Azure サービスのリソースを Azure Virtual Network に結び付け、これらのリソースへのインターネット アクセスを完全に除去できます。 サービス エンドポイントは、仮想ネットワークから Azure サービスへの直接接続を提供し、VNet のプライベート アドレス空間を使用して Azure サービスにアクセスできるようにします。 サービス エンドポイントを介して Azure サービスに送信されるトラフィックは、常に Microsoft Azure のバックボーン ネットワーク上に留まります。 仮想ネットワーク サービス エンドポイントの詳細については、[こちら](virtual-network-service-endpoints-overview.md)をご覧ください

この記事では、サービス エンドポイントを有効または無効にする手順を説明します。 エンドポイントが Azure サービスへのサブネットで有効にされたら、特定のサービス リソースを仮想ネットワークに結び付けることができます。

サービス エンドポイントは、[Azure Portal](#azure-portal)、[Azure PowerShell](#azure-powershell)、[Azure コマンド ライン インターフェイス](#azure-cli)、または Azure Resource Manager [テンプレート](#resource-manager-template)を使用して構成できます。

>[!NOTE]
プレビューでは、Vnet サービス エンドポイント機能は特定のリージョンに対してサポートされます。 サポートされるリージョンの一覧については、[Azure Virtual Network の更新情報](https://azure.microsoft.com/updates/?product=virtual-network)のページをご覧ください。

## <a name="service-endpoint-configuration-overview"></a>サービス エンドポイント構成の概要

- サービス エンドポイントは、Azure Resource Manager デプロイメント モデルを通じてデプロイされた Vnet でのみ構成できます。

- サービス エンドポイントは、VNet のサブネットごとに設定されます。

- サブネットの場合、サービスには 1 つのサービス エンドポイントのみを構成できます。 さまざまなサービス (たとえば、Azure Storage や Azure SQL など) に複数のサービス エンドポイントを構成することができます。

- 新規または既存のサブネット上でエンドポイントを有効にすることができます。

- 場所はエンドポイントに対して自動的に構成されます。 既定では、サービス エンドポイントは、VNet のリージョンに対して構成されます。 Azure Storage の場合、リージョン フェールオーバーのシナリオをサポートするために、エンドポイントは [Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)に対して自動的に構成されます。

  >[!NOTE]
  VNet/サブネットのサイズによっては、サービス エンドポイントの有効化が完了するまで時間がかかる場合があります。 サービス エンドポイントを有効にする場合は、重要なタスクが進行中でないことをご確認ください。 サービス エンドポイントは、サブネット内のすべての NIC 上のルートを切り替え、開いている TCP 接続を終了する場合があります。 

- サービス エンドポイント呼び出しは、サブネット内のすべての NIC のサービスへのトラフィック フローが Vnet のプライベート IP アドレスに切り替えられた後に "成功" を返します。

- エンドポイント構成を検証するための有効なルート:

   サービス エンドポイントが正しく構成されているかどうかを検証するために、サブネット内の任意の NIC の "有効なルート" には、サービスごと、リージョンごとに nextHopType: VirtualNetworkServiceEndpoint の新しい "既定" のルートが表示されます。 有効なルートのトラブルシューティングの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)をご覧ください

   >[!NOTE]
   有効なルートは、1 つ以上のネットワーク インターフェイス (NIC) が構成され、サブネットで実行中の仮想マシンに関連付けられている場合にのみ表示できます。

## <a name="azure-portal"></a>Azure Portal

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>VNet の作成時にサブネット上にサービス エンドポイントを設定する

1. [Azure Portal](https://portal.azure.com/) を開きます。
Azure アカウントを使用して Azure にログインします。 Azure アカウントを持っていない場合は、無料試用版でサインアップできます。 アカウントには、仮想ネットワークとサービス エンドポイントを作成するための[アクセス許可](#provisioning)が必要です。
2. 新規 > ネットワーク > 仮想ネットワーク > +追加をクリックします。
3. 仮想ネットワークの作成 で、次の値を入力して 作成をクリックします。

設定 | 値
------- | -----
名前    | myVnet
アドレス空間 | 10.0.0.0/16
サブネット名|mySubnet
サブネットのアドレス範囲|10.0.0.0/24
リソース グループ|[新規作成] を選択して、名前を入力します。
場所|サポートされているリージョン (たとえば、オーストラリア東部など)
[サブスクリプション]|サブスクリプションを選択します。
__ServiceEndpoints__|有効
__サービス__ | 1 つまたはすべての利用可能なサービスを選択します。 プレビュー時点でサポートされているサービスは __"Microsoft.Storage"、"Microsoft.Sql"__ です。

エンドポイントのサービスの選択: ![サービス エンドポイントのサービスの選択](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. すべての設定が正しいことを検証し、[作成] をクリックします。

![サービス エンドポイントの設定](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. VNet への Azure サービス リソースの結び付けを完了するには、「[次のステップ](#Next%20Steps)」のサービス ドキュメントをクリックします。


### <a name="validating-service-endpoint-configuration"></a>サービス エンドポイント構成の検証

次の手順を使用して、サービス エンドポイントが構成されていることを確認します。

- リソースで、[仮想ネットワーク] をクリックします。 VNet を検索します。
- Vnet 名をクリックし、[サービス エンドポイント] に移動します
- 構成されたエンドポイントは "成功" と表示されます。 自動構成された場所も表示されます

![サービス エンドポイント構成の確認](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>エンドポイント構成を検証するための有効なルート

サブネットのネットワーク インターフェイス (NIC) で有効なルートを表示するには、そのサブネット内の任意の NIC をクリックします。 [サポート + トラブルシューティング] で、[有効なルート] をクリックします。 エンドポイントが構成されている場合は、サービスのアドレス プレフィックスを持つ新しい "既定" のルートが宛先として表示され、nextHopType として "VirtualNetworkServiceEndpoint" が表示されます。

![サービス エンドポイントの有効なルート](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>VNet 内の既存のサブネットのサービス エンドポイントの設定

1. リソースで、[仮想ネットワーク] をクリックし、既存の VNet を検索します
2. VNet 名をクリックし、[サービス エンドポイント] に移動します
3. [追加] をクリックします。 [サービス] を選択します。 一度に 1 つのサービスにのみエンドポイントを作成できます。 
4. エンドポイントを適用するすべてのサブネットを選択します。 [追加] をクリックします

![サブネットのサービス エンドポイント構成](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>サービス エンドポイントの削除
1. リソースで、[仮想ネットワーク] をクリックします。 VNet 名でフィルタリングして、既存の VNet を検索します。
2. Vnet 名をクリックし、[サービス エンドポイント] に移動します
3. サービス名をクリックし、サービス エンドポイントのエントリを右クリックします
4. [削除] を選択します

![サービス エンドポイントの削除](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

前提条件の設定:

- PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
- PowerShell セッションを開始するには、**[開始]** に移動して「**powershell**」と入力し、**[PowerShell]** をクリックします。
- PowerShell で、`login-azurermaccount` コマンドを入力して Azure にログインします。 アカウントには、仮想ネットワークとサービス エンドポイントを作成するための[アクセス許可](#provisioning)が必要です。

### <a name="get-available-service-endpoints-for-azure-region"></a>Azure リージョンで利用可能なサービス エンドポイントの取得

次のコマンドを使用して、Azure リージョンのエンドポイントに対してサポートされているサービスの一覧を取得します。
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

出力: 
名前 | ID | 型
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>仮想ネットワーク *myVNet* の作成時に、サブネット *mySubnet* に Azure Storage サービス エンドポイントを追加する

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
複数のサービスを有効にするには、サービス名のコンマ区切りリストを使用します。
例: "Microsoft.Storage"、"Microsoft.Sql"

予想される出力:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

VNet への Azure サービス リソースの結び付けを完了するには、「[次のステップ](#Next%20Steps)」のサービス ドキュメントをクリックします。

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>既存のサブネットに複数のサービス エンドポイントを追加する

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

予想される出力: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>サブネットで構成されているサービス エンドポイントを表示する

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
出力:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>サブネット上のサービス エンドポイントを削除する
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

前提条件の設定:
- [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 ログインの詳細については、「[Azure CLI 2.0 を使ってみる](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)」を参照してください。
 - アカウントには、仮想ネットワークとサービス エンドポイントを作成するための[アクセス許可](#provisioning)が必要です。

 仮想ネットワーク用のコマンドの一覧については、[Azure CLI 仮想ネットワーク コマンド](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)に関する記事をご覧ください

### <a name="get-available-service-endpoints-for-azure-region"></a>Azure リージョンで利用可能なサービス エンドポイントの取得

次のコマンドを使用して、"EastUS" などの Azure リージョンのエンドポイントに対してサポートされているサービスの一覧を取得します。
```azure-cli
az network vnet list-endpoint-services -l eastus
```
出力:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>仮想ネットワーク *myVNet* の作成時に、サブネット *mySubnet* に Azure Storage サービス エンドポイントを追加する

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

複数のエンドポイントを追加するには: --service-endpoints Microsoft.Storage Microsoft.Sql

出力:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

VNet への Azure サービス リソースの結び付けを完了するには、「[次のステップ](#Next%20Steps)」のサービス ドキュメントをクリックします。

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>既存のサブネットに複数のサービス エンドポイントを追加する

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

予想される出力:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>サブネットで構成されているサービス エンドポイントを表示する

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>サブネット上のサービス エンドポイントを削除する
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

出力: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Resource Manager テンプレート

### <a name="securing-azure-service-resources-to-vnets"></a>VNet への Azure サービス リソースの結び付け

サービス エンドポイントを介して仮想ネットワークに特定の Azure リソースを結び付けることができます。

サンプルの [Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)をダウンロードして、VNet 内のサブネットにストレージ アカウントを結び付けます。

テンプレートでは、2 つのサブネットがある VNet を作成し、各サブネットに NIC を備えた VM を作成します。 1 つのサブネット上でエンドポイントを有効にし、そのサブネットにストレージ アカウントを結び付けます。

テンプレートをダウンロードし、自分のシナリオに合わせて一部を変更できます。

このテンプレートには、Azure Portal、PowerShell、Azure CLI を使用したデプロイ手順が記載されています。 エンドポイントの設定とアカウントの結び付けに必要な[アクセス許可](#provisioning)があることを確認します。

Azure リソースをサブネットに結び付けるには:

- サービス エンドポイントは、そのサブネット上に構成されている必要があります。
- リソースに仮想ネットワーク ルールを追加して、vnet にリソースを結び付ける必要があります。

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>サブネットに結び付けられたリソースがあるサービス エンドポイントの削除
Azure サービス リソースがサブネットに結び付けられ、サービス エンドポイントが削除された場合は、サブネットからリソースにアクセスできなくなります。
エンドポイントのみをもう一度有効にしても、以前にサブネットに結び付けられたリソースへのアクセスは復元されません。

このサブネットにサービス リソースを結び付けるには、次の操作が必要です。

- エンドポイントをもう一度有効にする
- リソース上の古い vnet ルールを削除する
- サブネットにリソースを結び付ける新しいルールを追加する

## <a name="provisioning"></a>プロビジョニング

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。

Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "Microsoft.Network/JoinServicetoaSubnet" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)と、特定のアクセス許可を[カスタム ロール](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)に割り当てる方法の詳細をご覧ください。

Vnet と Azure サービス リソースは、同じまたは異なるサブスクリプションで使用できます。 このプレビュー時点では、異なるサブスクリプションの場合、リソースが同じ Active Directory (AD) テナントに存在する必要があります。

## <a name="next-steps"></a>次のステップ

VNet にサービス リソースを結び付ける詳細な手順については、以下のリンクをご覧ください。

[Azure Storage アカウントを仮想ネットワークに結び付ける](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Azure SQL を仮想ネットワークに結び付ける](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
