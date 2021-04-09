---
title: Azure Cosmos DB 分析ストアのプライベート エンドポイントを構成する
description: ネットワーク アクセスを制限するために Azure Cosmos DB 分析ストアのマネージド プライベート エンドポイントを設定する方法について説明します。
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 2f15b397fbceb9e097d94080ba03fba50a96ed06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048507"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析ストアのプライベート エンドポイントを構成する
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

この記事では、Azure Cosmos DB 分析ストアのマネージド プライベート エンドポイントを設定する方法について説明します。 トランザクション ストアを使用している場合は、[トランザクション ストアのプライベート エンドポイント](how-to-configure-private-endpoints.md)に関する記事を参照してください。 マネージド プライベート エンドポイントを使用して、Azure Cosmos DB 分析ストアのネットワーク アクセスを Azure Synapse のマネージド仮想ネットワークに制限することができます。 マネージド プライベート エンドポイントによって、分析ストアへのプライベート リンクが確立されます。

## <a name="enable-private-endpoint-for-the-analytical-store"></a>分析ストアのプライベート エンドポイントを有効にする

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>マネージド仮想ネットワークを使用して Azure Synapse Analytics ワークスペースを設定する

[Azure Synapse Analytics でデータ流出を有効にしたワークスペースを作成します。](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) [データ流出を防止](../synapse-analytics/security/workspace-data-exfiltration-protection.md)することで、悪意のあるユーザーが Azure リソースから組織の範囲外の場所にデータをコピーまたは転送できないようにすることができます。

Azure Synapse Analytics ワークスペースでデータ流出防止を有効にした場合、次のアクセス制限が適用されます。

* Azure Synapse Analytics 用の Azure Spark を使用している場合、アクセスは Azure Cosmos DB 分析ストアの承認されたマネージド プライベート エンドポイントにのみ許可されます。

* Synapse サーバーレス SQL プールを使用している場合は、Azure Synapse Link を使用して任意の Azure Cosmos DB アカウントに対するクエリを実行できます。 ただし、[外部テーブルを select として作成する (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) 書き込み要求は、ワークスペース仮想ネットワーク内の承認されたマネージド プライベート エンドポイントに対してのみ許可されます。

> [!NOTE]
> ワークスペースの作成後に、マネージド仮想ネットワークとデータ流出防止の構成を変更することはできません。

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析ストアのマネージド プライベート エンドポイントを追加する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. Azure portal から、Synapse Analytics ワークスペースに移動し、 **[概要]** ウィンドウを開きます。

1. **[はじめに]** ペインに移動し、 **[Synapse Studio を開く]** で **[開く]** を選択して、Synapse Studio を起動します。

1. Synapse Studio で、 **[管理]** タブを開きます。

1. **[マネージド プライベート エンドポイント]** に移動し、 **[新規]** を選択します。

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="分析ストアの新しいプライベート エンドポイントを作成します。" border="true":::

1. アカウントの種類として **[Azure Cosmos DB (SQL API)]** 、次に **[続行]** を選択します。

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="[SQL API Azure Cosmos DB SQL API] を選択して、プライベート エンドポイントを作成します。" border="true":::

1. 次の詳細を使用して、 **[新しいマネージド プライベート エンドポイント]** フォームに入力します。

   * **[名前]** -マネージド プライベート エンドポイントの名前。 この名前は、作成後に更新することはできません。
   * **[説明]** -プライベート エンドポイントを識別するためのわかりやすい説明を入力します。
   * **[Azure サブスクリプション]** - Azure サブスクリプションで使用可能なアカウントの一覧から Azure Cosmos DB アカウントを選択します。
   * **[Azure Cosmos DB アカウント名]** - 種類が SQL または MongoDB である既存の Azure Cosmos DB アカウントを選択します。
   * **[ターゲット サブリソース]** - 次のオプションのいずれかを選択します。 **[分析]** : Azure Cosmos DB 分析ストアのプライベート エンドポイントを追加する場合。
     **[Sql]** (または **[MongoDB]** ): OLTP またはトランザクション アカウント エンドポイントを追加する場合。

   > [!NOTE]
   > Azure Synapse Analytics ワークスペース内で、トランザクション ストアと分析ストアの両方のプライベート エンドポイントを同じ Azure Cosmos DB アカウントに追加できます。 実行するのが分析クエリのみであれば、マップする必要があるのは、分析のプライベート エンドポイントのみです。

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="ターゲット サブリソースとして [分析] を選択します。" border="true":::

1. 作成したら、プライベート エンドポイント名にアクセスし、 **[Azure portal で承認を管理する]** を選択します。

1. Azure Cosmos DB アカウントに移動し、プライベート エンドポイントを選択して、 **[承認]** を選択します。

1. Synapse Analytics ワークスペースに戻り、 **[マネージド プライベート エンドポイント]** ペインで **[最新の情報に更新]** をクリックします。 プライベート エンドポイントが **[承認済み]** 状態であることを確認します。

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="プライベート エンドポイントが承認済みであることを確認します。" border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Azure Synapse Analytics 用の Apache Spark を使用する

データ流出防止が有効になっている Azure Synapse ワークスペースを作成した場合、既定では、Synapse Spark から Azure Cosmos DB アカウントへの発信アクセスはブロックされます。 また、Azure Cosmos DB に既存のプライベート エンドポイントが存在する場合、Synapse Spark はそのエンドポイントへのアクセスがブロックされます。

Azure Cosmos DB データへのアクセスを許可するには:

* Azure Synapse Link を使用して Azure Cosmos DB データのクエリを実行する場合は、Azure Cosmos DB アカウントのマネージド **分析** プライベート エンドポイントを追加します。

* トランザクション ストアへのバッチ書き込み/読み取りおよびストリーミング書き込み/読み取りを使用する場合は、Azure Cosmos DB アカウントのマネージド *SQL* または *MongoDB* プライベート エンドポイントを追加します。 さらに、次のコード スニペットに示すように、*connectionMode* を *Gateway* に設定する必要もあります。

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Synapse サーバーレス SQL プールを使用する

Synapse サーバーレス SQL プールでは、マネージド仮想ネットワークにデプロイされていないマルチテナント機能を使用します。 Azure Cosmos DB アカウントに既存のプライベート エンドポイントがある場合、Azure Cosmos DB アカウントに対するネットワーク分離チェックにより、Synapse サーバーレス SQL プールはアカウントへのアクセスがブロックされます。

Synapse ワークスペースからこのアカウントのネットワーク分離を構成するには:

1. アカウントに `NetworkAclBypassResourceId` 設定を指定して、Synapse ワークスペースが Azure Cosmos DB アカウントにアクセスできるようにします。

   **PowerShell の使用**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Azure CLI の使用**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB アカウントと Azure Synapse Analytics ワークスペースは同じ Azure Active Directory (AD) テナントにある必要があります。

2. これで、Azure Synapse Link 経由の T-SQL クエリを使用して、サーバーレス SQL プールからアカウントにアクセスできるようになりました。 ただし、分析ストア内のデータのネットワーク分離を確保するには、このアカウントに対して **分析** 用のマネージド プライベート エンドポイントを追加する必要があります。 そうしないと、分析ストア内のデータがパブリック アクセスからブロックされません。

> [!IMPORTANT]
> Azure Synapse Link を使用していて、分析ストア内のデータのネットワーク分離が必要な場合は、**分析** 用のマネージド プライベート エンドポイントを使用して、Azure Cosmos DB アカウントを Synapse ワークスペースにマップする必要があります。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Spark を使用した分析ストアのクエリ](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)の概要
* [Azure Synapse サーバーレス SQL プールを使用した分析ストアのクエリ](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)の概要
