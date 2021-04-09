---
title: Azure 仮想ネットワーク サービス エンドポイント ポリシー | Microsoft Docs
description: サービス エンドポイント ポリシーを使用して、Azure サービス リソースへの仮想ネットワーク トラフィックをフィルター処理する方法について説明します
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 9766379807e6d2708fd6935dd2ffbd7660f9988f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98216650"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Azure Storage の仮想ネットワーク サービス エンドポイント ポリシー

Virtual Network (VNet) のサービス エンドポイント ポリシーを使用すると、サービス エンドポイント経由の Azure Storage アカウントの仮想ネットワーク エグレス トラフィックをフィルター処理し、データ流出を特定の Azure Storage アカウントのみに許可することができます。 エンドポイン トポリシーでは、サービス エンドポイント経由で接続する際に、Azure Storage への仮想ネットワーク トラフィックに対する詳細なアクセスが制御されます。

![Azure Storage アカウントへの仮想ネットワークの送信トラフィックをセキュリティで保護する](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

この機能は、 __すべてのグローバル Azure リージョン__ の __Azure Storage__ で一般公開されています。

## <a name="key-benefits"></a>主な利点

仮想ネットワーク サービス エンドポイント ポリシーには、次の利点があります。

- __Azure Storage への仮想ネットワーク トラフィックのセキュリティ強化__

  [ネットワーク セキュリティ グループ用の Azure サービス タグ](./network-security-groups-overview.md) を使用することで、特定の Azure Storage リージョンへの仮想ネットワーク送信トラフィックを制限できます。 ただし、これにより、選択した Azure Storage リージョン内の任意のアカウントにトラフィックが送信されます。
  
  エンドポイント ポリシーを使用すると、仮想ネットワークの送信アクセスを許可される Azure Storage アカウントを指定し、他のすべてのストレージ アカウントへのアクセスを制限することができます。 これにより、仮想ネットワークで流出したデータを保護するためのセキュリティ制御がさらに詳細になります。

- __Azure サービス トラフィックをフィルター処理するスケーラブルな高可用性ポリシー__

   エンドポイント ポリシーでは、サービス エンドポイント経由の、仮想ネットワークからの Azure サービス トラフィックをフィルター処理するためのスケーラブルな高可用性ソリューションが提供されます。 仮想ネットワークでこのトラフィック用のセントラル ネットワーク アプライアンスを維持するために、追加のオーバーヘッドは必要ありません。

## <a name="json-object-for-service-endpoint-policies"></a>サービス エンドポイント ポリシーの JSON オブジェクト
サービス エンドポイント ポリシー オブジェクトを簡単に見てみましょう。

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>構成

-   特定の Azure Storage アカウントへの仮想ネットワークのトラフィックを制限するエンドポイント ポリシーを構成することができます。
-   エンドポイント ポリシーは仮想ネットワーク内のサブネット上で構成されます。 Azure Storage に対してポリシーを適用するには、サブネット上でサービス エンドポイントを有効にする必要があります。
-   エンドポイント ポリシーでは、resourceID 形式を使用して、許可リストに特定の Azure Storage アカウントを追加することができます。 アクセスを以下に制限できます。
    - サブスクリプションのすべてのストレージ アカウント<br>
      `E.g. /subscriptions/subscriptionId`

    - リソース グループのストレージ アカウント<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - 対応する Azure Resource Manager resourceId を記載した個別のストレージ アカウント。 これには、BLOB、テーブル、キュー、ファイルおよび Azure Data Lake Storage Gen2 へのトラフィックが含まれます。 <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   既定では、サブネットにサービス エンドポイント ポリシーが指定されていない場合、サービス内のすべてのストレージ アカウントにアクセスできます。 ポリシーがそのサブネット上に構成された後、そのサブネットのコンピューティング インスタンスからはポリシーに指定されているリソースのみにアクセスできます。 他のすべてのストレージ アカウントへのアクセスは拒否されます。
-   サブネットにサービス エンドポイント ポリシーを適用すると、Azure Storage の *サービス エンドポイントのスコープ* は、地域から **グローバル** にアップグレードされます。 これは、それ以降、Azure Storage へのすべてのトラフィックが、サービス エンドポイントを介してセキュリティで保護されることを意味します。 サービス エンドポイント ポリシーもグローバルに適用されるため、明示的に許可されていないストレージ アカウントはアクセスを拒否されます。 
-   サブネットには複数のポリシーを適用できます。 サブネットに複数のポリシーが関連付けられている場合は、これらのポリシー全体に指定されているリソースへの仮想ネットワーク トラフィックが許可されます。 どのポリシーにも指定されていない、他のすべてのサービス リソースへのアクセスは拒否されます。

    > [!NOTE]  
    > サービス エンドポイント ポリシーは **許可ポリシー** のため、指定されたリソースを除いて、他のすべてのリソースが制限されます。 ご利用のアプリケーションが持つすべてのサービス リソースの依存関係を明らかにして、ポリシーに列挙済みであることをご確認ください。

- エンドポイント ポリシーで指定できるのは、Azure Resource Model を使用するストレージ アカウントのみです。 クラシック Azure Storage アカウントは、Azure サービス エンドポイント ポリシーをサポートしていません。
- プライマリ アカウントがリストされている場合、RA-GRS のセカンダリ アクセスが自動的に許可されます。
- ストレージ アカウントは、仮想ネットワークと同じまたは異なるサブスクリプションにある場合、あるいは Azure Active Directory テナントにある場合があります。

## <a name="scenarios"></a>シナリオ

- **ピアリングされているか、接続されているか、あるいは複数の仮想ネットワーク**:ピアリングされた仮想ネットワーク内のトラフィックをフィルター処理するには、これらの仮想ネットワークに個別にエンドポイント ポリシーを適用する必要があります。
- **ネットワーク アプライアンスまたは Azure Firewall でのインターネット トラフィックのフィルター処理**:ポリシーを使用してサービス エンドポイント経由の Azure サービス トラフィックをフィルター処理し、残りのインターネットまたは Azure トラフィックはアプライアンスまたは Azure Firewall 経由でフィルター処理します。
- **Virtual Network にデプロイされている Azure サービスでのトラフィックのフィルター処理**:現時点では、Azure サービス エンドポイント ポリシーは、ご利用の仮想ネットワークにデプロイされている Azure マネージド サービスのいずれでもサポートされていません。 
- **オンプレミスから Azure サービスへのトラフィックのフィルター処理**:サービス エンドポイント ポリシーのみが、そのポリシーに関連付けられているサブネットからのトラフィックに適用されます。 オンプレミスから特定の Azure サービス リソースへのアクセスを許可するには、ネットワーク仮想アプライアンスまたはファイアウォールを使用してトラフィックをフィルター処理する必要があります。

## <a name="logging-and-troubleshooting"></a>ロギングおよびトラブルシューティング
サービス エンドポイント ポリシーでは、集中ログ記録を使用することはできません。 サービスのリソース ログについては、[サービス エンドポイントのログ記録](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting)に関するページを参照してください。

### <a name="troubleshooting-scenarios"></a>トラブルシューティングのシナリオ
- プレビューで動作していたストレージ アカウントへのアクセスが拒否されました (geo ペア リージョンでは使用できません)
  - グローバル サービス タグを使用できるように Azure Storage アップグレードすると、サービス エンドポイントのスコープとサービス エンドポイントのポリシーがグローバルになります。 そのため、Azure Storage へのトラフィックはサービス エンドポイント経由で暗号化され、ポリシーに明示的に列挙されているストレージ アカウントのみがアクセスを許可されます。
  - アクセスを復元するために必要なすべてのストレージ アカウントのリストを明示的に許可します。  
  - Azure サポートに問い合わせます。
- エンドポイント ポリシーにリストされているアカウントに対するアクセスが拒否される
  - ネットワーク セキュリティ グループまたはファイアウォールのフィルター処理でアクセスがブロックされている可能性があります
  - ポリシーの削除または再適用によって接続が失われる場合:
    - エンドポイント経由の仮想ネットワークからのアクセスを許可するように Azure サービスが構成されているかどうか、またはリソースの既定のポリシーが *[すべて許可]* に設定されていることを確認します。
    - サービス診断でエンドポイント経由のトラフィックが表示されることを確認します。
    - ネットワーク セキュリティ グループのフロー ログにアクセスが表示されているかどうかと、ストレージ ログにサービス エンドポイント経由のアクセスが期待どおりに表示されることを確認します。
    - Azure サポートに問い合わせます。
- サービス エンドポイント ポリシーにリストされていないアカウントに対するアクセスが拒否される
  - エンドポイント経由の仮想ネットワークからのアクセスを許可するように Azure Storage が構成されているかどうか、またはリソースの既定のポリシーが *[すべて許可]* に設定されているかどうかを確認します。
  - アカウントが、サブネット上のサービス エンドポイント ポリシーと **クラシック ストレージ アカウント** ではないことを確認します。
- マネージド Azure サービスが、サブネット上でサービス エンドポイント ポリシーを適用した後に動作を停止した場合
  - 現時点では、管理サービスはサービス エンドポイント ポリシーでサポートされていません。 *最新情報はこのページでお伝えしますのでしばらくお待ちください*。

## <a name="provisioning"></a>プロビジョニング

サービス エンドポイント ポリシーは、仮想ネットワークへの書き込みアクセス権を持つユーザーがサブネット上で構成できます。 Azure の[組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)と、特定のアクセス許可を[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法の詳細をご覧ください。

仮想ネットワークと Azure Storage アカウントは、同じまたは異なるサブスクリプションにある場合、あるいは Azure Active Directory テナントにある場合があります。

## <a name="limitations"></a>制限事項

- サービス エンドポイント ポリシーをデプロイできるのは、Azure Resource Manager デプロイ モデルを使ってデプロイされた仮想ネットワークのみとなります。
- 仮想ネットワークは、サービス エンドポイント ポリシーと同じリージョンにある必要があります。
- ポリシーにリストされている Azure サービスに対してサービス エンドポイントが構成されている場合、サービス エンドポイント ポリシーはサブネット上でのみ適用できます。
- オンプレミス ネットワークから Azure サービスへのトラフィックに対して、サービス エンドポイント ポリシーを使用することはできません。
- 現在、Azure 管理サービスではエンドポイント ポリシーはサポートされていません。 これには、共有サブネットにデプロイされた管理サービス (例: "*Azure Batch、Azure ADDS、Azure Application Gateway、Azure VPN Gateway、Azure Firewall*") または専用サブネット (例: "*Azure App Service Environment、Azure Redis Cache、Azure API Management、Azure SQL MI、クラシック管理サービス*") が含まれます。

 > [!WARNING]
 > Azure HDInsight など、仮想ネットワークにデプロイされている Azure サービスは、インフラストラクチャ要件のため、Azure Storage などのその他のサービスにアクセスします。 特定のリソースにエンドポイント ポリシーを制限すると、ご利用の仮想ネットワークにデプロイされている Azure サービス用のこれらのインフラストラクチャ リソースへのアクセスが中断される可能性があります。

- エンドポイント ポリシーでは従来のストレージ アカウントはサポートされていません。 既定では、ポリシーで従来のすべてのストレージ アカウントへのアクセスが拒否されます。 アプリケーションから Azure Resource Manager および従来のストレージ アカウントにアクセスする必要がある場合は、このトラフィックに対してエンドポイント ポリシーを使用しないでください。

## <a name="pricing-and-limits"></a>料金と制限

サービス エンドポイント ポリシーの使用に追加料金はかかりません。 現時点では、Azure サービス (Azure Storage など) の現在の価格設定モデルは、サービス エンドポイントを介して適用されます。

サービス エンドポイント ポリシーでは、次の制限が適用されます。 

 |リソース | 既定の制限 |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>次の手順

- [仮想ネットワーク サービス エンドポイント ポリシーを構成する方法](virtual-network-service-endpoint-policies-portal.md)を学習する
- [仮想ネットワーク サービス エンドポイント](virtual-network-service-endpoints-overview.md)の詳細を確認する