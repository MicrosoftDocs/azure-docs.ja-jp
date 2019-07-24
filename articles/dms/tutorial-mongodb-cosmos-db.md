---
title: チュートリアル:Azure Database Migration Service を使用して MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの MongoDB から Azure Cosmos DB の MongoDB 用 API にオフラインで移行する方法について学習します。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 68cf4561d24a84c5aacefbe5fbd11bcd7e0576aa
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878555"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>チュートリアル:DMS を使用して MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する
Azure Database Migration Service を使用して、MongoDB のオンプレミスまたはクラウドのインスタンスから Azure Cosmos DB の MongoDB 用 API に、データベースのオフライン (1 回限り) の移行を実行できます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

このチュートリアルでは、Azure Database Migration Service を使用して、Azure 仮想マシンでホストされている MongoDB 内のデータセットを、Azure Cosmos DB の MongoDB 用 API に移行します。 MongoDB ソースをまだセットアップしていない場合は、記事「[Azure の Windows VM に MongoDB をインストールして構成する](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb)」をご覧ください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下を実行する必要があります。
- [Azure Cosmos DB の MongoDB 用 API アカウントを作成します](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNET) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。

    > [!NOTE]
    > VNET のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用した場合、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    > - ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > - ストレージ エンドポイント
    > - サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

- VNET のネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートがブロックされないことを確認します: 443、53、9354、445、12000。 Azure VNET NSG トラフィックのフィルター処理の詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)」を参照してください。
- Azure Database Migration Service がソース MongoDB サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 27017 が使用されています。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する
1. Azure portal にサインインし、**[すべてのサービス]** を選択し、**[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、**[リソース プロバイダー]** を選択します。
 
    ![リソース プロバイダーの表示](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。
 
    ![リソース プロバイダーの登録](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>インスタンスを作成する
1.  Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  **[Azure Database Migration Service]** 画面で、**[作成]** を選択します。
 
    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。 

5. 既存の仮想ネットワーク (VNET) を選択するか、新しい VNET を作成します。

    この VNET が Azure Database Migration Service に、ソース MongoDB インスタンスとターゲット Azure Cosmos DB アカウントへのアクセスを提供します。

    Azure portal で VNET を作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)に関する記事を参照してください。

6. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    適切な Azure Database Migration Service レベルの選択について不明な点がある場合は、[こちら](https://go.microsoft.com/fwlink/?linkid=861067)のブログ投稿の推奨事項を参照してください。  

     ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する
サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、**[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。
 
      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

3. **[+ 新しい移行プロジェクト]** を選択します。

4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、**[ソース サーバーの種類]** テキスト ボックスでは **[MongoDB]** を、**[ターゲット サーバーの種類]** テキスト ボックスでは **[CosmosDB (MongoDB API)]** を選択して、**[アクティビティの種類を選択します]** で **[オフライン データの移行]** を選択します。 

    ![Database Migration Service プロジェクトを作成する](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

## <a name="specify-source-details"></a>ソース詳細を指定する
1. **[ソースの詳細]** 画面で、ソース MongoDB サーバーの接続の詳細を指定します。
    
   また、接続文字列モードを使用し、移行するコレクション データをダンプした BLOB ストア ファイル コンテナーの場所を指定できます。

   > [!NOTE]
   > また、Azure Database Migration Service では、BSON ドキュメントまたは JSON ドキュメントを Azure Cosmos DB の MongoDB 用 API のコレクションに移行することもできます。
    
   DNS の名前解決ができない場合は、IP アドレスを使用することもできます。

   ![ソース詳細を指定する](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する
1. **[移行のターゲットの詳細]** 画面で、ターゲットの Azure Cosmos DB アカウントの接続の詳細を指定します。これは、MongoDB データの移行先である、事前プロビジョニング済みの Azure Cosmos DB の MongoDB 用 API アカウントです。

    ![ターゲット詳細を指定する](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **[保存]** を選択します。

## <a name="map-to-target-databases"></a>ターゲット データベースにマップする
1. **[Map to target databases]\(ターゲット データベースにマップ\)** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service は既定でターゲット データベースを選択します。

    データベース名の横に **[作成]** と表示される場合は、Azure Database Migration Service でターゲット データベースが見つからず、サービスによってデータベースが自動的に作成されることを示します。

    以降のこの時点で、[スループットをプロビジョニングする](https://docs.microsoft.com/azure/cosmos-db/set-throughput)ことができます。 Cosmos DB では、データベース レベルで、またはコレクションごとに個別に、スループットをプロビジョニングできます。 スループットは、[要求ユニット](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU) で測定されます。 [Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)の詳細を確認してください。

    ![ターゲット データベースにマップする](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **[保存]** を選択します。
3. **[コレクション設定]** 画面で、コレクションの一覧表示を展開し、移行されるコレクションの一覧を確認します。

    Azure Database Migration Service では、ソースの MongoDB インスタンスには存在していて、ターゲットの Azure Cosmos DB アカウントには存在していない、すべてのコレクションが自動的に選択されることに注意してください。 データが既に含まれているコレクションを再度移行する場合は、このブレードでコレクションを明示的に選択する必要があります。

    コレクションで使用する RU の量を指定することができます。 Azure Database Migration Service では、コレクションのサイズに基づいて適切な既定値が提案されます。

    また、最適なスケーラビリティのために、シャード キーを指定して [Azure Cosmos DB のパーティション分割](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)を利用することもできます。 [シャード/パーティション キーの選択に関するベスト プラクティス](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)を確認してください。

    ![コレクション テーブルを選択する](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **[保存]** を選択します。

5. **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** ボックスに移行アクティビティの名前を指定します。

    ![移行の概要](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する
- **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示され、アクティビティの **[状態]** は **[未開始]** になっています。

    ![アクティビティの状態](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>移行を監視する
- 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

   > [!NOTE]
   > アクティビティを選択して、データベース レベルおよびコレクション レベルの移行メトリックの詳細を取得できます。

    ![アクティビティの状態 - 完了](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DB でデータを確認する

- 移行が完了した後、Azure Cosmos DB アカウントを調べて、すべてのコレクションが正常に移行されたことを確認できます。

    ![アクティビティの状態 - 完了](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>その他のリソース

 * [Cosmos DB サービスの情報](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>次の手順
- Microsoft の「[Database Migration Guide](https://datamigration.microsoft.com/)」(データベース移行ガイド) で、他のシナリオの移行ガイダンスを確認する。
