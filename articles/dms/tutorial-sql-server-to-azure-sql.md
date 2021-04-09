---
title: 'チュートリアル: SQL Server を Azure SQL Database にオフラインで移行する'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、SQL Server から Azure SQL Database にオフラインで移行する方法について学習します。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 9c3fa0d8ac4540495e8580fd208507a2c1aaa7ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180689"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>チュートリアル: DMS を使用して SQL Server を Azure SQL Database に移行する

Azure Database Migration Service を使用して、SQL Server インスタンスから [Azure SQL Database](/azure/sql-database/) にデータベースを移行することができます。 このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server 2016 (以降) のオンプレミス インスタンスに復元された [Adventureworks2016](/sql/samples/adventureworks-install-configure#download-backup-files) データベースを、Azure SQL Database の単一データベースまたはプールされたデータベースに移行します。

学習内容:
> [!div class="checklist"]
>
> - Data Migration Assistant を使用して、障害となっている問題についてオンプレミス データベースを評価する。
> - Data Migration Assistant を使用して、データベース サンプル スキーマを移行する。 
> - Azure DataMigration リソース プロバイダーを登録する。
> - Azure Database Migration Service のインスタンスを作成する。
> - Azure Database Migration Service を使用して移行プロジェクトを作成する。
> - 移行を実行する。
> - 移行を監視する。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

- [SQL Server 2016 以降](https://www.microsoft.com/sql-server/sql-server-downloads)をダウンロードしてインストールします。
- SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。
- [Azure portal を使用した Azure SQL Database 内のデータベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関する記事の詳細に従って、Azure SQL Database 内にデータベースを作成します。 このチュートリアルでは、Azure SQL Database の名前を **AdventureWorksAzure** としていますが、任意の名前を指定することもできます。

    > [!NOTE]
    > SQL Server Integration Services (SSIS) を使用していて、SSIS プロジェクト/パッケージ (SSISDB) のカタログ データベースを SQL Server から Azure SQL Database に移行する場合は、SSIS を Azure Data Factory (ADF) にプロビジョニングしたときに移行先 SSISDB が自動的に作成および管理されます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](./how-to-migrate-ssis-packages.md)」を参照してください。
  
- 最新バージョンの [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) をダウンロードしてインストールします。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure Virtual Network を作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続が確立されます。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    >
    > - ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > - ストレージ エンドポイント
    > - サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。
    >
    >オンプレミス ネットワークと Azure の間にサイト間接続がない場合、またはサイト間接続の帯域幅が制限されている場合は、Azure Database Migration Service をハイブリッド モード (プレビュー) で使用することを検討してください。 ハイブリッド モードでは、オンプレミスの移行 worker と、クラウドで実行されている Azure Database Migration Service のインスタンスを利用します。 ハイブリッド モードで Azure Database Migration Service のインスタンスを作成するには、[Azure portal を使用してハイブリッド モードで Azure Database Migration Service のインスタンスを作成する方法](./quickstart-create-data-migration-service-hybrid-portal.md)に関する記事を参照してください。

- 仮想ネットワークのネットワーク セキュリティ グループの送信セキュリティ規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 Azure 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
- [データベース エンジン アクセスのために Windows ファイアウォール](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure Database Migration Service でソース SQL Server にアクセスできるように Windows ファイアウォールを開放します。既定では TCP ポート 1433 が使用されます。 使用している既定のインスタンスが他のポートでリッスンしている場合は、それをファイアウォールに追加してください。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- Azure SQL Database のサーバー レベル IP [ファイアウォール規則](../azure-sql/database/firewall-configure.md)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。
- ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含めます。
- ターゲットの Azure SQL Database インスタンスへの接続に使用される資格情報に、ターゲット データベースに対する [CONTROL DATABASE](/sql/t-sql/statements/grant-database-permissions-transact-sql) アクセス許可を含めます。

## <a name="assess-your-on-premises-database"></a>オンプレミス データベースを評価する

SQL Server インスタンスから Azure SQL Database の単一データベースまたはプールされたデータベースにデータを移行する前に、SQL Server データベースを評価して、移行を妨げる問題がないかどうかを確認する必要があります。 Data Migration Assistant を使用し、[SQL Server の移行評価の実行](/sql/dma/dma-assesssqlonprem)に関する記事の手順に従って、オンプレミス データベースの評価を実行します。 必要な手順の概要は次のとおりです。

1. Data Migration Assistant で、新規 (+) アイコンを選択し、 **[評価]** プロジェクト タイプを選択します。
2. プロジェクト名を指定します。 **[評価の種類]** ドロップダウン リストで、 **[データベース エンジン]** を選択し、 **[ソース サーバーの種類]** ボックスで **[SQL Server]** 、 **[ターゲット サーバーの種類]** ボックスで **[Azure SQL Database]** を選択します。次に、 **[作成]** を選択してプロジェクトを作成します。

    Azure SQL Database の単一データベースまたはプールされたデータベースに移行するソース SQL Server データベースを評価する際には、次のいずれかまたは両方の評価レポート タイプを選択できます。

   - データベース互換性をチェックする
   - 機能の類似性をチェックする

    どちらのレポート タイプも、既定で選択されています。

3. Data Migration Assistant の **[オプション]** 画面で、 **[次へ]** を選択します。
4. **[ソースの選択]** 画面の **[サーバーへの接続]** ダイアログ ボックスで、SQL Server への接続詳細を入力し、 **[接続]** を選択します。
5. **[ソースの追加]** ダイアログ ボックスで、 **[Adventureworks2016]** 、 **[追加]** 、 **[評価の開始]** の順に選択します。

    > [!NOTE]
    > SSIS を使用する場合、現在、DMA ではソース SSISDB の評価はサポートされていません。 ただし、SSIS プロジェクト/パッケージは、Azure SQL Database によってホストされている移行先 SSISDB に再デプロイされるため、評価/検証されます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](./how-to-migrate-ssis-packages.md)」を参照してください。

    評価が完了すると、次のグラフィックに結果が表示されます。

    ![データ移行の評価](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Azure SQL Database のデータベースの場合、この評価では、単一データベースまたはプールされたデータベースへのデプロイで、機能パリティの問題と移行の障害となっている問題が特定されます。

    - **SQL Server の機能類似性** カテゴリでは、幅広い推奨事項や、Azure で利用できる代替アプローチ、および移行プロジェクトの計画に役立つ移行手順を確認できます。
    - **互換性問題** カテゴリでは、部分的にサポートされている機能やサポートされていない機能を確認できます。この情報は、SQL Server データベースから Azure SQL Database への移行を妨げる可能性がある互換性の問題を反映しています。 また、それらの問題への対処に役立つ推奨事項も確認できます。

6. 特定のオプションを選択して、移行を妨げる問題と機能の類似性の問題についての評価結果をレビューします。

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

評価結果をレビューし、選択したデータベースが Azure SQL Database の単一データベースまたはプールされたデータベースに移行可能であることを確認したら、DMA を使用してスキーマを Azure SQL Database に移行します。

> [!NOTE]
> Data Migration Assistant で移行プロジェクトを作成する前に、Azure 内のデータベースが、前提要件で言及されたように既にプロビジョニングされていることを確認します。 

> [!IMPORTANT]
> SSIS を使用する場合、現在 DMA ではソース SSISDB の移行はサポートされていませんが、Azure SQL Database によってホストされている移行先 SSISDB に SSIS プロジェクト/パッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](./how-to-migrate-ssis-packages.md)」を参照してください。

**Adventureworks2016** スキーマを Azure SQL Database の単一データベースまたはプールされたデータベースに移行するには、次の手順を実行します。

1. Data Migration Assistant で、新規 (+) アイコンを選択し、 **[プロジェクト タイプ]** で **[移行]** を選択します。
2. プロジェクト名を指定し、 **[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。
3. **[移行スコープ]** で、 **[Schema only]\(スキーマのみ\)** を選択します。

    上記の手順を実行すると、Data Migration Assistant のインターフェイスの表示が次の図のようになります。

    ![Data Migration Assistant プロジェクトを作成する](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. **[作成]** を選択してプロジェクトを作成します。
5. Data Migration Assistant で、SQL Server のソース接続の詳細を指定し、 **[接続]** を選択して、**Adventureworks2016** データベースを選択します。

    ![Data Migration Assistant のソース接続詳細](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. **[次へ]** を選択し、 **[Connect to target server]\(対象サーバーへの接続\)** で Azure SQL Database のターゲット接続の詳細を指定し、 **[接続]** を選択し、Azure SQL Database で事前プロビジョニングした **AdventureWorksAzure** データベースを選択します。

    ![Data Migration Assistant のターゲット接続詳細](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. **[次へ]** を選択して **[オブジェクトの選択]** 画面に進みます。この画面では、Azure SQL Database にデプロイする必要がある **Adventureworks2016** データベース内のスキーマ オブジェクトを指定できます。

    既定では、すべてのオブジェクトが選択されています。

    ![SQL スクリプトを生成する](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. **[SQL スクリプトの生成]** を選択して SQL スクリプトを作成し、スクリプトにエラーがないかを確認します。

    ![スキーマ スクリプト](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. **[Deploy schema]\(スキーマのデプロイ\)** を選択して Azure SQL Database にスキーマをデプロイし、スキーマがデプロイされたら、ターゲット サーバーに異常がないかをチェックします。

    ![スキーマをデプロイする](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインします。 **サブスクリプション** を検索して選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-sql-server-to-azure-sql/portal-select-subscription-1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーの表示](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. 移行を検索してから、 **[Microsoft.DataMigration]** の **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>インスタンスを作成する

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。 **[Azure Database Migration Service]** を検索して選択します。

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. **[移行サービスの作成]** の [基本] 画面で、次の手順を実行します。

     - サブスクリプションを選択します。
     - 新しいリソース グループを作成するか、既存のリソース グループを選択します。
     - Azure Database Migration Service のインスタンスの名前を指定します。
     - Azure Database Migration Service のインスタンスを作成する場所を選択します。
     - サービス モードとして **[Azure]** を選択します。
     - 価格レベルを選択します。 コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンスの基本設定を構成する](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - **[次へ: ネットワーク]** を選択します。

4. **[移行サービスの作成]** の [ネットワーク] 画面で、次の手順を実行します。

    - 既存の仮想ネットワークを選択するか、新しく作成します。 この仮想ネットワークが Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL Database インスタンスへのアクセスを提供します。 Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。

    ![Azure Database Migration Service インスタンスのネットワーク設定を構成する](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - **[確認および作成]** を選択してサービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure portal のメニューで、 **[すべてのサービス]** を選択します。 **[Azure Database Migration Service]** を検索して選択します。

     ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. **[Azure Database Migration Service]** 画面で、作成した Azure Database Migration Service インスタンスを選択します。

3. **[新しい移行プロジェクト]** を選択します。

     ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[SQL Server]** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **[Azure SQL Database]** を選択し、**[Choose Migration activity type]\(移行アクティビティの種類を選択\)** で **[データ移行]** を選択します。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの選択]** 画面で、ソース SQL Server インスタンスの接続の詳細を指定します。

    ソース SQL Server インスタンス名には、必ず完全修飾ドメイン名 (FQDN) を使用してください。 DNS の名前解決ができない場合は、IP アドレスを使用することもできます。

2. 信頼できる証明書をソース サーバーにインストールしていない場合は、 **[Trust server certificate]\(サーバー証明書を信頼する\)** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書を使用して暗号化される TLS 接続では、強固なセキュリティが提供されません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 実稼働環境やインターネットに接続しているサーバーでは、自己署名証明書を使用した TLS 接続は使用しないでください。

    > [!IMPORTANT]
    > SSIS を使用する場合、現在 DMS ではソース SSISDB の移行はサポートされていませんが、Azure SQL Database によってホストされている移行先 SSISDB に SSIS プロジェクト/パッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](./how-to-migrate-ssis-packages.md)」を参照してください。

   ![ソースの詳細](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. **[次へ: データベースの選択]** を選択します。

## <a name="select-databases-for-migration"></a>移行するデータベースを選択する

Azure SQL Database に移行するすべてのデータベースまたは特定のデータベースを選択します。 DMS によって、選択したデータベースの移行にかかる予測時間が示されます。 移行によるダウンタイムを許容できる場合は、移行を続行します。 移行によるダウンタイムが許容できない場合は、[ダウンタイムがほぼない SQL Managed Instance](tutorial-sql-server-managed-instance-online.md) に移行するか、他のオプションについて [DMS チーム](mailto:DMSFeedback@microsoft.com) に問い合わせることを検討してください。 

1. 使用可能なデータベースの一覧から、移行するデータベースを選択します。 
1. 予想されるダウンタイムを確認します。 許容できる場合は、 **[次へ: ターゲットの選択 >>]** を選択します。

   ![ソース データベース](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[ターゲットの選択]** 画面で、Azure SQL Database に対する認証設定を指定します。 

   ![ターゲット デバイスの選択](media/tutorial-sql-server-to-azure-sql/select-target.png)

1. **Next:ターゲット データベースへマッピング** を選択し、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    ![ターゲット データベースにマップする](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. **Next:Configuration migration settings\(次へ: 構成の移行設定\)** を選択し、テーブルの一覧を展開して、影響を受けるフィールドの一覧を確認します。

    Azure Database Migration Service では、ターゲット Azure SQL Database インスタンスに存在する空のソース テーブルがすべて自動的に選択されます。 データが既に含まれているテーブルを再移行する場合は、このブレードでテーブルを明示的に選択する必要があります。

    ![テーブルを選択する](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. **Next:概要** を選択し、移行構成を確認します。**アクティビティ名** ボックスに、移行アクティビティの名前を指定します。

    ![検証オプションを選択する](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>移行を実行する

- **[移行の開始]** を選択します。

    移行アクティビティ ウィンドウが表示され、アクティビティの **[状態]** は **[Pending]\(保留\)** になっています。

    ![アクティビティの状態](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

    ![完了したアクティビティの状態](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. ターゲットの **Azure SQL Database** にターゲット データベースがあることを確認します。

### <a name="additional-resources"></a>その他のリソース

- Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
- Azure SQL Database については、「[Azure SQL Database サービスとは](../azure-sql/database/sql-database-paas-overview.md)」を参照してください。
