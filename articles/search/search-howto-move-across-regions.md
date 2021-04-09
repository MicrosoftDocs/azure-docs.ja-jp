---
title: サービス リソースをリージョン間で移動する方法
titleSuffix: Azure Cognitive Search
description: この記事では、Azure クラウドで Azure Cognitive Search リソースをあるリージョンから別のリージョンに移動する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/10/2020
ms.openlocfilehash: a5050958f01743ff3c6fdcdecfee3067b1d2073e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934264"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Cognitive Search サービスを別の Azure リージョンに移動する

まれに、検索サービスを別のリージョンに移動できないかとお客様から問い合わせをいただくことがあります。 現時点では、そのような作業を支援する組み込みのメカニズムやツールはありませんが、この記事では、同じ結果を手動で実現するための手順をわかりやすく説明しています。

> [!NOTE]
> Azure portal では、すべてのサービスに **Export template** コマンドがあります。 Azure Cognitive Search の場合、このコマンドを実行すると、サービスの基本定義 (名前、場所、レベル、レプリカ、およびパーティション数) が生成されますが、サービスのコンテンツは認識されず、キー、ロール、またはログにも引き継がれません。 コマンドは存在しますが、検索サービスの移動には使用しないことをお勧めします。

## <a name="prerequisites"></a>前提条件

+ お使いのアカウントで使用されるサービスと機能が、ターゲット リージョンでサポートされていることを確認してください。

+ プレビュー機能については、お使いのサブスクリプションがターゲット リージョンに対して承認されていることを確認してください。

## <a name="prepare-and-move"></a>準備と移動

1. Azure Cognitive Search 以外にも移動すべきサービスがあることも考えられます。そのような場合に備え、サービスを再配置した場合の影響の全容を把握するために、依存関係や関連するサービスを特定します。

   Azure Storage は、ログ、ナレッジ ストアの作成に使用されるほか、AI エンリッチメントやインデックス作成用の外部データ ソースとしてもよく使用されます。 Cognitive Services は、AI エンリッチメントの依存関係です。 AI エンリッチメントを使用している場合は、Cognitive Services と検索サービスの両方を同じリージョンに置く必要があります。

1. インデックス、同意語マップ、インデクサー、データ ソース、スキルセットなど、何を移動すべきかがわかるよう、サービスのすべてのオブジェクトのインベントリを作成します。 ログを有効にした場合は、履歴レコードに必要な可能性のあるレポートを作成してアーカイブします。

1. 新しいリージョンにおける価格と利用の可否を確認して、Azure Cognitive Search とその関連サービスが新しいリージョンで確実に利用できることを確かめます。 大部分の機能はすべてのリージョンで提供されていますが、一部のプレビュー機能は、利用が制限されている場合があります。

1. 新しいリージョンにサービスを作成し、既存のインデックス、同意語マップ、インデクサー、データ ソース、スキルセットをソース コードから再発行します。 サービス名は一意である必要があるため、既存の名前を再利用することはできません。 同一リージョン要件の観点から各スキルセットをチェックして、Cognitive Services への接続が引き続き有効であるかどうかを確認します。 また、ナレッジ ストアが作成される場合、異なるサービスを使用している場合には、Azure Storage の接続文字列をチェックしてください。

1. 該当する場合は、インデックスとナレッジ ストアを再度読み込みます。 アプリケーション コードを使用して JSON データをインデックスにプッシュするか、インデクサーを再度実行して外部ソースからドキュメントをプルします。 

1. ログ記録を有効にし、それらを使用している場合はセキュリティ ロールを再度作成します。

1. クライアント アプリケーションとテスト スイートを更新し、新しいサービス名と API キーを使用してから、すべてのアプリケーションをテストします。

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ

新しいサービスが完全にテストされ、運用可能になったら、古いサービスを削除します。 サービスを削除すると、そのサービスに関連付けられているすべてのコンテンツが自動的に削除されます。

## <a name="next-steps"></a>次の手順

前述の手順を実施するにあたっては、次のリンクを参考にしてください。より詳しい情報を入手できます。

+ [Azure Cognitive Search の価格とリージョン](https://azure.microsoft.com/pricing/details/search/)
+ [レベルの選択](search-sku-tier.md)
+ [Search Service の作成](search-create-service-portal.md)
+ [検索ドキュメントの読み込み](search-what-is-data-import.md)
+ [ログの有効化](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is allowlisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->