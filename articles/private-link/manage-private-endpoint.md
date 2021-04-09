---
title: Azure でプライベート エンドポイント接続を管理する
description: Azure でプライベート エンドポイント接続を管理する方法について学習します
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531374"
---
# <a name="manage-a-private-endpoint-connection"></a>プライベート エンドポイント接続を管理する
Azure Private Link は、承認呼び出しフロー モデルで機能します。このモデルでは、Private Link サービスのコンシューマーがサービスを使用するためにサービス プロバイダーへの接続を要求できます。 次に、サービス プロバイダーは、コンシューマーに接続を許可するかどうかを決定できます。 サービス プロバイダーは、Azure Private Link を使用して、自分のリソース上のプライベート エンドポイント接続を管理できます。 この記事では、プライベート エンドポイント接続を管理する方法について説明します。

![プライベート エンドポイントを管理する](media/manage-private-endpoint/manage-private-endpoint.png)

Private Link サービスのコンシューマーは、次の 2 つの接続承認方法から選択できます。
- **自動**: サービスのコンシューマーにサービス プロバイダーのリソースに対する Azure RBAC アクセス許可がある場合、コンシューマーは自動の承認方法を選択できます。 この場合、要求がサービス プロバイダーのリソースに到達したときにサービス プロバイダーからのアクションは必要なく、接続が自動的に承認されます。 
- **手動**: 一方、サービスのコンシューマーにサービス プロバイダーのリソースに対する Azure RBAC アクセス許可がない場合、コンシューマーは手動の承認方法を選択できます。 この場合、接続要求はサービスのリソース上で **[Pending]\(保留中\)** として表示されます。 サービス プロバイダーは、接続を確立する前に、手動で要求を承認する必要があります。 手動の場合、サービスのコンシューマーは、要求と共にメッセージを指定して、サービス プロバイダーにより多くのコンテキストを提供することもできます。 サービス プロバイダーは、すべてのプライベート エンドポイント接続に対して次のいずれかのオプションを選択できます: **Approved**、**Reject**、**Remove**。

次の表に、サービス プロバイダーのさまざまなアクションと、その結果であるプライベート エンドポイントの接続状態を示します。  サービス プロバイダーは、コンシューマーの介入なしに、プライベート エンドポイント接続の接続状態を後で変更することもできます。 このアクションにより、コンシューマー側のエンドポイントの状態が更新されます。 


|サービス プロバイダーのアクション   |サービスのコンシューマーのプライベート エンドポイントの状態   |説明   |
|---------|---------|---------|
|なし    |    保留中     |    接続が手動で作成されており、Private Link リソースの所有者による承認を待っています。       |
|承認    |  Approved       |  接続が自動または手動で承認され、使用する準備が整っています。     |
|Reject     | 拒否        | プライベート リンク リソースの所有者によって接続が拒否されました。        |
|[削除]    |  [Disconnected]\(切断済み\)       | プライベート リンク リソースの所有者によって接続が削除されました。プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Azure PaaS リソース上のプライベート エンドポイント接続を管理する
ポータルは、Azure PaaS リソース上のプライベート エンドポイント接続を管理するための推奨手段です。 現時点では、Azure PaaS リソース上の接続を管理するための PowerShell または CLI のサポートはありません。
1. Azure Portal ( https://portal.azure.com ) にサインインします。
2. プライベート リンク センターに移動します。
3. **[リソース]** で、プライベート エンドポイント接続を管理するリソースの種類を選択します。
4. リソースの種類ごとに、それに関連付けられているプライベート エンドポイント接続の数を確認できます。 必要に応じて、リソースをフィルター処理できます。
5. プライベート エンドポイント接続を選択します。  一覧表示された接続の下で、管理する接続を選択します。 
6. 上部のオプションから選択することにより、接続の状態を変更できます。

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>顧客またはパートナーが所有する Private Link サービス上のプライベート エンドポイント接続を管理する

Azure PowerShell および Azure CLI は、Microsoft Partner Services または顧客が所有するサービス上のプライベート エンドポイント接続を管理するための推奨手段です。 現時点では、Private Link サービス上の接続を管理するためのポータルのサポートはありません。  
 
### <a name="powershell"></a>PowerShell 
  
次の PowerShell コマンドを使用して、プライベート エンドポイント接続を管理できます。  
#### <a name="get-private-link-connection-states"></a>Private Link の接続状態を取得する 
`Get-AzPrivateLinkService` コマンドレットを使用すると、プライベート エンドポイント接続とその状態を取得できます。  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>プライベート エンドポイント接続を承認する 
 
`Approve-AzPrivateEndpointConnection` コマンドレットを使用すると、プライベート エンドポイント接続を承認できます。 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>プライベート エンドポイント接続を拒否する 
 
`Deny-AzPrivateEndpointConnection` コマンドレットを使用すると、プライベート エンドポイント接続を拒否できます。 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>プライベート エンドポイント接続を削除する 
 
`Remove-AzPrivateEndpointConnection` コマンドレットを使用すると、プライベート エンドポイント接続を削除できます。 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
`az network private-link-service update` を使用して、対象のプライベート エンドポイント接続を管理できます。 接続状態は、```azurecli connection-status``` パラメーターを使用して指定します。 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>次のステップ
- [プライベート エンドポイントについて学習します](private-endpoint-overview.md)
 
