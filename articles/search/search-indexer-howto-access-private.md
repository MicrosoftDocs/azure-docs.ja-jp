---
title: プライベート エンドポイントを経由したインデクサー接続
titleSuffix: Azure Cognitive Search
description: インデクサー接続を構成して、プライベート エンドポイント経由で保護されている他の Azure リソースからコンテンツにアクセスできるようにします。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b30cb1767e733861d8418ea29e564bc90a5bc70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676512"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>プライベート エンドポイントを経由したインデクサー接続の作成

Azure ストレージ アカウントなどの多くの Azure リソースは、仮想ネットワークの一覧からの接続を受け入れ、パブリック ネットワークからの外部接続を拒否するように構成できます。 インデクサーを使用して Azure Cognitive Search のデータにインデックスを作成し、データ ソースがプライベート ネットワーク上にある場合は、データに到達するために発信[プライベート エンドポイント接続](../private-link/private-endpoint-overview.md)を作成できます。

このインデクサーの接続方法には、次の 2 つの要件があります。

+ コンテンツまたはコードを提供する Azure リソースは、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)に事前登録されている必要があります。

+ Azure Cognitive Search サービスは、Basic レベル以上である必要があります。 この機能は、Free レベルでは使用できません。 また、インデクサーにスキルセットがある場合、レベルは Standard 2 (S2) 以上である必要があります。 詳細については、[サービスの制限](search-limits-quotas-capacity.md#shared-private-link-resource-limits)に関する記事を参照してください。

## <a name="shared-private-link-resources-management-apis"></a>共有プライベート リンク リソース管理 API

Azure Cognitive Search API によって作成された、セキュリティで保護されたリソースのプライベート エンドポイントは、"*共有プライベート リンク リソース*" と呼ばれます。 これは、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)と統合されているストレージ アカウントなどのリソースへのアクセスを "共有" しているためです。

Azure Cognitive Search では、その管理 REST API を通じて、Azure Cognitive Search インデクサーからのアクセスを構成するために使用できる [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) 操作が提供されます。

一部のリソースへのプライベート エンドポイント接続を作成できるのは、検索管理 API のプレビュー バージョン (バージョン *2020-08-01-preview* 以降) を使用する場合のみです。これは、次の表で "*プレビュー*" として示されています。 "*プレビュー*" の指定のないリソースは、プレビューまたは一般提供されている API バージョン (*2020-08-01* 以降) を使用して作成できます。

次の表は、Azure Cognitive Search から発信プライベート エンドポイントを作成できる Azure リソースを示しています。 共有プライベート リンク リソースを作成するには、API に記述されているとおりに、**グループ ID** 値を入力します。 値は大文字と小文字が区別されます。

| Azure リソース | グループ ID |
| --- | --- |
| Azure Storage - BLOB (または) ADLS Gen 2 | `blob`|
| Azure Storage - Tables | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Azure SQL データベース | `sqlServer`|
| Azure Database for MySQL (プレビュー) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (プレビュー) | `sites` |

発信プライベート エンドポイント接続がサポートされている Azure リソースは、[サポートされる API のリスト](/rest/api/searchmanagement/privatelinkresources/listsupported)を使って照会することもできます。

この記事の残りの部分では、[Azure CLI](/cli/azure/) (または必要に応じて [ARMClient](https://github.com/projectkudu/ARMClient)) と [Postman](https://www.postman.com/) (または、必要に応じて [curl](https://curl.se/) などの他の HTTP クライアント) を組み合わせて使用して、REST API の呼び出しをデモンストレーションします。

> [!NOTE]
> この記事の例は、次の前提に基づいています。
> * 検索サービスの名前は _contoso-search_ です。これは、サブスクリプション ID _00000000-0000-0000-0000-000000000000_ を持つサブスクリプションの _contoso_ リソース グループに存在します。 
> * この検索サービスのリソース ID は、 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_ です。

以降の例では、_contoso-search_ サービスを構成して、そのインデクサーがセキュリティで保護されたストレージ アカウント _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage_ からデータにアクセスできるようにする方法を示しています。

## <a name="secure-your-storage-account"></a>ストレージ アカウントのセキュリティ保護

[特定のサブネットからのアクセスのみを許可する](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)ように、ストレージ アカウントを構成します。 Azure portal で、このオプションをオンにして設定を空のままにすると、仮想ネットワークからのトラフィックは許可されないことを意味します。

   ![[ファイアウォールと仮想ネットワーク] ウィンドウのスクリーンショット。選択したネットワークへのアクセスを許可するオプションが表示されています。 ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> [信頼された Microsoft サービス アプローチ](../storage/common/storage-network-security.md#trusted-microsoft-services)を使って、ストレージ アカウントの仮想ネットワークまたは IP 制限を回避できます。 また、検索サービスでストレージ アカウントのデータにアクセスできるようにすることもできます。 これを行うには、[信頼されたサービスの例外を使用した Microsoft Azure Storage アカウントのデータへの安全なアクセス](search-indexer-howto-access-trusted-service-exception.md)に関する記事を参照してください。 
>
> ただし、このアプローチを使用する場合、Azure Cognitive Search とストレージ アカウント間の通信は、セキュリティで保護された Microsoft バックボーン ネットワークで、ストレージ アカウントのパブリック IP アドレスを介して行われます。

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>手順 1:ストレージ アカウントに対して共有プライベート リンク リソースを作成する

ストレージ アカウントへの発信プライベート エンドポイント接続を作成するように Azure Cognitive Search に要求するには、たとえば [Azure CLI](/cli/azure/) で、次の API 呼び出しを実行します。 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

[ARMClient](https://github.com/projectkudu/ARMClient) を使用する場合は、次のようにします。

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

API への要求本文を表す *create-pe.json* ファイルの内容は次のとおりです。

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

成功した場合、`202 Accepted` 応答が返されます。 発信プライベート エンドポイントを作成するプロセスは長時間実行される (非同期の) 操作です。 これには、次のリソースのデプロイが含まれます。

* プライベート IP アドレスが割り当てられた、`"Pending"` 状態のプライベート エンドポイント。 このプライベート IP アドレスは、検索サービス固有のプライベート インデクサー実行環境の仮想ネットワークに割り当てられたアドレス空間から取得されます。 プライベート エンドポイントが承認されると、Azure Cognitive Search からストレージ アカウントへのすべての通信は、プライベート IP アドレスとセキュリティで保護されたプライベート リンク チャネルから行われます。

* `groupId` に基づく、リソースの種類のプライベート DNS ゾーン。 このリソースをデプロイすると、プライベート リソースへの DNS 参照で、プライベート エンドポイントに関連付けられた IP アドレスが使用されるようになります。

プライベート エンドポイントを作成するリソースの種類に対して必ず正しい `groupId` を指定します。 不一致があると、失敗を示す応答メッセージが返されます。

すべての非同期 Azure 操作と同様に、`PUT` の呼び出しにより、次のような `Azure-AsyncOperation` ヘッダー値が返されます。

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

この URI を定期的にポーリングして、操作の状態を取得できます。 続行する前に、共有プライベート リンク リソースの操作が終了の状態 (つまり、操作の状態が "*成功*") になるまで待機することをお勧めします。

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

あるいは ARMClient を使用して、次のようにします。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>手順 2a:ストレージ アカウントのプライベート エンドポイント接続を承認する

> [!NOTE]
> このセクションでは、Azure portal を使用して、ストレージに対するプライベート エンドポイントの承認フローを順を追って説明します。 または、ストレージ リソース プロバイダーを介して利用可能な [REST API](/rest/api/storagerp/privateendpointconnections) を使うこともできます。
>
> Azure Cosmos DB や Azure SQL Server などの他のプロバイダーは、プライベート エンドポイント接続を管理するための同様のストレージ リソース プロバイダー API を提供します。

1. Azure portal でストレージ アカウントの **[プライベート エンドポイント接続]** タブを選択します。 非同期操作が成功した後に、前の API 呼び出しからの要求メッセージを使用して、プライベート エンドポイント接続の要求を行う必要があります。

   ![Azure portal のスクリーンショット。[プライベート エンドポイント接続] ウィンドウが表示されています。](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Azure Cognitive Search によって作成されたプライベート エンドポイントを選択します。 **[プライベート エンドポイント]** 列で、前の API で指定した名前でプライベート エンドポイント接続を特定し、 **[承認]** を選択して、適切なメッセージを入力します。 メッセージの内容は重要ではありません。 

   プライベート エンドポイント接続が次のスクリーンショットに示すように表示されていることを確認します。 ポータルで状態が更新されるまでに 1 から 2 分かかる場合があります。

   ![Azure portal のスクリーンショット。[プライベート エンドポイント接続] ウィンドウに [承認済み] の状態が表示されています。](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

プライベート エンドポイント接続要求が承認されると、トラフィックがプライベート エンドポイントを介してフロー "*できる*" ようになります。 プライベート エンドポイントが承認されると、Azure Cognitive Search によって、それに対して作成された DNS ゾーンに必要な DNS ゾーン マッピングが作成されます。

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>手順 2b:共有プライベート リンク リソースの状態を照会する

共有プライベート リンク リソースが承認後に更新されたことを確認するために、[GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get) を使用してその状態を取得します。

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

あるいは ARMClient を使用して、次のようにします。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

リソースの `properties.provisioningState` が `Succeeded` で、`properties.status` が `Approved` である場合、共有プライベート リンク リソースが機能しており、プライベート エンドポイントを介して通信するようにインデクサーを構成できます。

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>手順 3:プライベート環境で実行されるようにインデクサーを構成する

> [!NOTE]
> この手順は、プライベート エンドポイント接続が承認される前に実行できます。 プライベート エンドポイント接続が承認されるまで、セキュリティで保護されたリソース (ストレージ アカウントなど) との通信を試行するすべてのインデクサーは、一時的なエラー状態になります。 新しいインデクサーの作成は失敗します。 プライベート エンドポイント接続が承認されるとすぐに、インデクサーはプライベート ストレージ アカウントにアクセスできます。

1. セキュリティで保護されたストレージ アカウントと、ストレージ アカウント内の適切なコンテナーを指す[データ ソース](/rest/api/searchservice/create-data-source)を作成します。 次のスクリーンショットは、Postman でのこの要求を示しています。

   ![Postman ユーザー インターフェイスでのデータソースの作成を示すスクリーンショット。](media\search-indexer-howto-secure-access\create-ds.png )

1. 同様に、[インデックスを作成](/rest/api/searchservice/create-index)し、必要に応じて REST API を使用して[スキルセットを作成](/rest/api/searchservice/create-skillset)します。

1. 前の手順で作成したデータ ソース、インデックス、スキルセットを指す[インデクサーを作成](/rest/api/searchservice/create-indexer)します。 また、インデクサーの構成プロパティ `executionEnvironment` を `private` に設定して、インデクサーがプライベート実行環境で実行されるように強制します。

   ![Postman ユーザー インターフェイスでのインデクサーの作成を示すスクリーンショット。](media\search-indexer-howto-secure-access\create-idr.png)

   インデクサーが正しく作成されると、プライベート エンドポイント接続を介してストレージ アカウントからのコンテンツのインデックス作成が開始されます。 インデクサーの状態は、[インデクサーの状態 API](/rest/api/searchservice/get-indexer-status) を使用して監視できます。

> [!NOTE]
> 既存のインデクサーがある場合は、[PUT API](/rest/api/searchservice/create-indexer) で `executionEnvironment` を `private` に設定してそれらを更新できます。

## <a name="troubleshooting"></a>トラブルシューティング

- インデクサーの作成が、"データ ソースの資格情報が無効です" という内容のエラー メッセージが表示されて失敗した場合は、プライベート エンドポイント接続がまだ "*承認*" されていないか、接続が機能していないことを意味します。 この問題を解決するには、次のようにします。 
  * [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get) を使用して、共有プライベート リンク リソースの状態を取得します。 状態が "*承認済み*" の場合は、リソースの `properties.provisioningState` を確認します。 ここの状態が `Incomplete` の場合、リソースの基になる依存関係の一部がセットアップされなかったことを意味します。 共有プライベート リンク リソースを再作成するために `PUT` 要求を再発行すると、問題が解決されます。 再承認が必要になる場合があります。 リソースの状態を再確認して、問題が解決されたことを確認します。

- `executionEnvironment` プロパティを設定せずにインデクサーを作成した場合、そのインデクサーの作成は成功する場合もありますが、その実行履歴には、インデクサーの実行が失敗したことが示されます。 この問題を解決するには、次のようにします。
   * [インデクサーを更新](/rest/api/searchservice/update-indexer)して、実行環境を指定します。

- `executionEnvironment` プロパティを設定せずにインデクサーを作成し、そのインデクサーが正常に実行された場合、Azure Cognitive Search によってその実行環境が検索サービス固有の "*プライベート*" 環境であると判断されたことを意味します。 これは、インデクサーによって消費されるリソース、検索サービスの負荷、その他の要因などによって変わることがあり、後で失敗する可能性があります。 この問題を解決するには、次のようにします。
  * `executionEnvironment` プロパティを `private` に設定して、今後エラーが発生しないようにすることを強くお勧めします。

[クォータと制限](search-limits-quotas-capacity.md)によって、作成できる共有プライベート リンク リソースの数が決まります。クォータと制限は、検索サービスの SKU に基づきます。

## <a name="next-steps"></a>次のステップ

以下で、プライベート エンドポイントについて詳しく学習してください。

- [プライベート エンドポイントとは](../private-link/private-endpoint-overview.md)
- [プライベート エンドポイントに必要な DNS 構成](../private-link/private-endpoint-dns.md)