---
title: Azure Cosmos DB のグローバルな分散
description: Azure Portal で Azure Cosmos DB を使用してデータをグローバルにレプリケートする方法について説明します
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 751571820d52c003a7e740bd63af8c9d9e071c7a
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796115"
---
## <a id="addregion"></a>Azure Portal を使用したグローバル データベース リージョンの追加
Azure Cosmos DB は世界中のすべての [Azure リージョン][azureregions]で利用できます。 データベース アカウントの既定の一貫性レベルを選択すると、選択した既定の一貫性レベルとグローバル配信の必要性に応じて、1 つまたは複数のリージョンを関連付けることができます。

1. [Azure Portal](https://portal.azure.com/) で、左側のバーの **[Azure Cosmos DB]** をクリックします。
2. **[Azure Cosmos DB]** ページで、変更するデータベース アカウントを選びます。
3. アカウントのページで、メニューから **[データをグローバルにレプリケートする]** をクリックします。
4. **[データをグローバルにレプリケートする]** ページで、マップ内のリージョンをクリックして、追加または削除するリージョンを選択し、**[保存]** をクリックします。 リージョンを追加するには費用が必要になります。詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)または「[Azure Cosmos DB を使用したデータのグローバル分散](../articles/cosmos-db/distribute-data-globally.md)」の記事を参照してください。
   
    ![地図でリージョンをクリックして、リージョンを追加又は削除する][1]
    
2 番目のリージョンを追加すると、ポータルの **[データをグローバルにレプリケートする]** ページで **[手動フェールオーバー]** オプションが有効になります。 このオプションは、フェールオーバー プロセスをテストしたり、プライマリ書き込みリージョンを変更したりするために使用できます。 3 番目のリージョンを追加すると、同じページの **[フェールオーバーの優先度]** オプションが有効になります。これで、読み取りのフェールオーバーの順序を変更できるようになります。  

### <a name="selecting-global-database-regions"></a>グローバル データベース リージョンの選択
複数のリージョンを構成する場合、2 つの一般的なシナリオがあります。

1. エンドユーザーが世界中のどこにいても関係なく、データへの待ち時間の短いアクセスを実現する
2. リージョンのビジネス継続性とディザスター リカバリー (BCDR) の回復性を追加する

エンド ユーザーの短い待ち時間の実現のため、アプリケーションのユーザーが存在する場所に対応するリージョンに、アプリケーションと Azure Cosmos DB の両方をデプロイすることをお勧めします。

BCDR のため、リージョン ペアに基づいてリージョンを追加することをお勧めします。リージョン ペアについては、「[ビジネス継続性とディザスター リカバリー (BCDR):Azure のペアになっているリージョン][bcdr]」をご覧ください。

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
