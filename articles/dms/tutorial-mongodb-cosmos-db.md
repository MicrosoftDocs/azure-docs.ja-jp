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
ms.date: 07/04/2019
ms.openlocfilehash: 2ff5ebefbe379edda94dcf8ac066027398e2f3f4
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565538"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>チュートリアル:DMS を使用して MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する

Azure Database Migration Service を使用して、MongoDB のオンプレミスまたはクラウドのインスタンスから Azure Cosmos DB の MongoDB 用 API に、データベースのオフライン (1 回限り) の移行を実行できます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

このチュートリアルでは、Azure Database Migration Service を使用して、Azure 仮想マシンでホストされている MongoDB 内のデータセットを、Azure Cosmos DB の MongoDB 用 API に移行します。 MongoDB ソースをまだセットアップしていない場合は、記事「[Azure の Windows VM に MongoDB をインストールして構成する](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* スループットの見積もり、パーティション キーの選択、インデックス作成ポリシーなど、[移行前の手順を完了](../cosmos-db/mongodb-pre-migration.md)します。
* [Azure Cosmos DB の MongoDB 用 API アカウントを作成します](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーへのサイト間接続が提供されます。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > VNet のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* VNet のネットワーク セキュリティ グループ (NSG) の規則によって、以下の各通信ポートがブロックされていないことを確認します: 53、443、445、9354、および 10000 から 20000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事を参照してください。
* Azure Database Migration Service がソース MongoDB サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 27017 が使用されています。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、 **[リソース プロバイダー]** を選択します。

    ![リソース プロバイダーの表示](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。 

5. 既存の VNet を選択するか、新しいものを作成します。

    この VNet が Azure Database Migration Service に、ソース MongoDB インスタンスとターゲット Azure Cosmos DB アカウントへのアクセスを提供します。

    Azure portal で VNet を作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)」を参照してください。

6. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

3. **[+ 新しい移行プロジェクト]** を選択します。

4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスでは **[MongoDB]** を、 **[ターゲット サーバーの種類]** テキスト ボックスでは **[CosmosDB (MongoDB API)]** を選択して、 **[アクティビティの種類を選択します]** で **[オフライン データの移行]** を選択します。 

    ![Database Migration Service プロジェクトを作成する](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの詳細]** 画面で、ソース MongoDB サーバーの接続の詳細を指定します。

    ソースへの接続には、3 つのモードがあります。
   * **標準モード**: 完全修飾ドメイン名または IP アドレス、ポート番号、および接続の資格情報を受け取ります。
   * **接続文字列のモード**: MongoDB 接続文字列 (記事「[Connection String URI Format (接続文字列の URI 形式)](https://docs.mongodb.com/manual/reference/connection-string/)」で説明しています) を受け取ります。
   * **Azure Storage からのデータ**: BLOB コンテナーの SAS URL を受け取ります。 MongoDB [bsondump ツール](https://docs.mongodb.com/manual/reference/program/bsondump/)によって生成された BSON ダンプが BLOB コンテナーに含まれている場合は、 **[Blob には BSON ダンプが含まれています]** を選択します。JSON ファイルがコンテナーに含まれている場合は、その選択を解除します。

     このオプションを選択する場合は、ストレージ アカウントの接続文字列が次の形式で表示されていることを確認してください。

     ```
     https://blobnameurl/container?SASKEY
     ```

     また、Azure ストレージの種類のダンプ情報に基づいて、次の点に注意してください。

     * BSON ダンプの場合、データ ファイルが collection.bson 形式の包含データベースの名前が付けられたフォルダーに配置されるように、BLOB コンテナー内のデータは bsondump 形式にする必要があります。 メタデータ ファイルがある場合は、*collection*.metadata.json の形式で名前を付けます。

     * JSON ダンプの場合、BLOB コンテナー内のファイルは、包含データベースの名前が付けられたフォルダーに配置する必要があります。 各データベース フォルダー内で、データ ファイルは "data" というサブフォルダーに配置し、*collection*.json の形式で名前を付ける必要があります。 メタデータ ファイルがある場合は、"metadata" というサブフォルダーに配置し、同じ形式の *collection*.json を使用して名前を付ける必要があります。 メタデータ ファイルは、MongoDB bsondump ツールによって生成されたものと同じ形式にする必要があります。

    > [!IMPORTANT]
    > Mongo サーバー上で自己署名入りの証明書を使用することはお勧めできません。 ただし、使用する場合は、**接続文字列のモード**を使用してサーバーに接続し、確実に接続文字列に “” を付けるようにしてください。
    >
    >```
    >&sslVerifyCertificate=false
    >```

   DNS の名前解決ができない場合は、IP アドレスを使用することもできます。

   ![ソース詳細を指定する](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[移行のターゲットの詳細]** 画面で、ターゲットの Azure Cosmos DB アカウントの接続の詳細を指定します。これは、MongoDB データの移行先である、事前プロビジョニング済みの Azure Cosmos DB の MongoDB 用 API アカウントです。

    ![ターゲット詳細を指定する](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **[保存]** を選択します。

## <a name="map-to-target-databases"></a>ターゲット データベースにマップする

1. **[Map to target databases]\(ターゲット データベースにマップ\)** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    データベース名の横に **[作成]** と表示される場合は、Azure Database Migration Service でターゲット データベースが見つからず、サービスによってデータベースが自動的に作成されることを示します。

    以降のこの時点で、[スループットをプロビジョニングする](https://docs.microsoft.com/azure/cosmos-db/set-throughput)ことができます。 Cosmos DB では、データベース レベルで、またはコレクションごとに個別に、スループットをプロビジョニングできます。 スループットは、[要求ユニット](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU) で測定されます。 [Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)の詳細を確認してください。

    ![ターゲット データベースにマップする](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **[保存]** を選択します。
3. **[コレクション設定]** 画面で、コレクションの一覧表示を展開し、移行されるコレクションの一覧を確認します。

    Azure Database Migration Service では、ソースの MongoDB インスタンスには存在していて、ターゲットの Azure Cosmos DB アカウントには存在していない、すべてのコレクションが自動的に選択されます。 データが既に含まれているコレクションを再度移行する場合は、このブレードでコレクションを明示的に選択する必要があります。

    コレクションで使用する RU の量を指定することができます。 Azure Database Migration Service では、コレクションのサイズに基づいて適切な既定値が提案されます。

    > [!NOTE]
    > 実行を高速化するために、必要に応じて Azure Database Migration Service の複数のインスタンスを使用して、データベースの移行とコレクションを並列して実行します。

    また、最適なスケーラビリティのために、シャード キーを指定して [Azure Cosmos DB のパーティション分割](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)を利用することもできます。 [シャード/パーティション キーの選択に関するベスト プラクティス](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)を確認してください。

    ![コレクション テーブルを選択する](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **[保存]** を選択します。

5. **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** ボックスに移行アクティビティの名前を指定します。

    ![移行の概要](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示され、アクティビティの **[状態]** は **[未開始]** になっています。

    ![アクティビティの状態](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>移行を監視する

* 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

   > [!NOTE]
   > アクティビティを選択して、データベース レベルおよびコレクション レベルの移行メトリックの詳細を取得できます。

    ![アクティビティの状態 - 完了](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DB でデータを確認する

* 移行が完了した後、Azure Cosmos DB アカウントを調べて、すべてのコレクションが正常に移行されたことを確認できます。

    ![アクティビティの状態 - 完了](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>移行後の最適化

MongoDB データベースに格納されているデータを Azure Cosmos DB の MongoDB 用 API に移行した後、Azure Cosmos DB に接続してデータを管理できます。 また、インデックス作成ポリシーの最適化、既定の整合性レベルの更新、Azure Cosmos DB アカウントのグローバル分散の構成など、移行後の他の最適化手順を実行することもできます。 詳細については、「[移行後の最適化](../cosmos-db/mongodb-post-migration.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [Cosmos DB サービスの情報](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>次の手順

* Microsoft の「[Database Migration Guide](https://datamigration.microsoft.com/)」(データベース移行ガイド) で、他のシナリオの移行ガイダンスを確認する。
