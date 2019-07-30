---
title: チュートリアル:Azure Database Migration Service を使用して SQL Server から Azure SQL Database の単一データベース/プールされたデータベースにオフラインで移行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの SQL Server から Azure SQL Database の単一データベースまたはプールされたデータベースにオフラインで移行する方法について学習します。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/09/2019
ms.openlocfilehash: c4d2e70d0f200f3a31948cfaa43def8e1b699878
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706879"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>チュートリアル:DMS を使用して SQL Server を Azure SQL Database の単一データベースまたはプールされたデータベースにオフラインで移行する

Azure Database Migration Service を使用して、オンプレミスの SQL Server インスタンスから [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) にデータベースを移行することができます。 このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server 2016 (以降) のオンプレミス インスタンスに復元された **Adventureworks2012** データベースを Azure SQL Database の単一データベースまたはプールされたデータベースに移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> - Data Migration Assistant を使用して、オンプレミス データベースを評価する。
> - Data Migration Assistant を使用して、サンプル スキーマを移行する。
> - Azure Database Migration Service のインスタンスを作成する。
> - Azure Database Migration Service を使用して移行プロジェクトを作成する。
> - 移行を実行する。
> - 移行を監視する。
> - 移行レポートをダウンロードする。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、SQL Server から Azure SQL Database の単一データベースまたはプールされたデータベースへのオフライン移行について説明します。 オンライン移行については、「[DMS を使用して SQL Server を Azure SQL Database にオンラインで移行する](tutorial-sql-server-azure-sql-online.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

- [SQL Server 2016 以降](https://www.microsoft.com/sql-server/sql-server-downloads)をダウンロードしてインストールします。
- SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。
- 「[Azure portal を使用して Azure SQL Database で単一データベースを作成する](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started)」の詳細な手順に従って、Azure SQL Database の単一 (またはプールされた) データベースを作成します。

    > [!NOTE]
    > SQL Server Integration Services (SSIS) を使用していて、SSIS プロジェクト/パッケージ (SSISDB) のカタログ データベースを SQL Server から Azure SQL Database に移行する場合は、SSIS を Azure Data Factory (ADF) にプロビジョニングしたときに移行先 SSISDB が自動的に作成および管理されます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。
  
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 以降をダウンロードしてインストールします。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > VNet のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    > - ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > - ストレージ エンドポイント
    > - サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

- VNet ネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートが確実にブロックされないようにします:443、53、9354、445、12000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事を参照してください。
- [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- Azure SQL Database サーバーのサーバーレベルの IP [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNet のサブネット範囲を指定します。
- ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含めます。
- ターゲットの Azure SQL データベース インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL データベースに対する CONTROL DATABASE アクセス許可を含めます。

## <a name="assess-your-on-premises-database"></a>オンプレミス データベースを評価する

オンプレミスの SQL Server インスタンスから Azure SQL Database の単一データベースまたはプールされたデータベースにデータを移行する前に、SQL Server データベースを評価して、移行を妨げる問題がないかどうかを確認する必要があります。 Data Migration Assistant v3.3 以降を使用し、[SQL Server の移行評価の実行](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)に関する記事の手順に従って、オンプレミス データベースの評価を完了してください。 必要な手順の概要は次のとおりです。

1. Data Migration Assistant で、新規 (+) アイコンを選択し、 **[評価]** プロジェクト タイプを選択します。
2. プロジェクト名を指定し、 **[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server**、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択した後、 **[作成]** を選択してプロジェクトを作成します。

    Azure SQL Database の単一データベースまたはプールされたデータベースに移行するソース SQL Server データベースを評価する際には、次のいずれかまたは両方の評価レポート タイプを選択できます。

   - データベース互換性をチェックする
   - 機能の類似性をチェックする

    どちらのレポート タイプも、既定で選択されています。

3. Data Migration Assistant の **[オプション]** 画面で、 **[次へ]** を選択します。
4. **[ソースの選択]** 画面の **[サーバーへの接続]** ダイアログ ボックスで、SQL Server への接続詳細を入力し、 **[接続]** を選択します。
5. **[Add sources]\(ソースの追加\)** ダイアログ ボックスで、**AdventureWorks2012**、 **[追加]** 、 **[Start Assessment]\(評価の開始\)** の順に選択します。

    > [!NOTE]
    > SSIS を使用する場合、現在、DMA ではソース SSISDB の評価はサポートされていません。 ただし、SSIS プロジェクト/パッケージは、Azure SQL Database によってホストされている移行先 SSISDB に再デプロイされるため、評価/検証されます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

    評価が完了すると、次のグラフィックに結果が表示されます。

    ![データ移行の評価](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    評価では、Azure SQL Database の単一データベースまたはプールされたデータベースについて、単一データベースまたはプールされたデータベースへのデプロイでの機能パリティの問題と移行の障害となっている問題が特定されます。

    - **SQL Server の機能類似性**カテゴリでは、幅広い推奨事項や、Azure で利用できる代替アプローチ、および移行プロジェクトの計画に役立つ移行手順を確認できます。
    - **互換性問題**カテゴリでは、部分的にサポートされている機能やサポートされていない機能を確認できます。この情報は、オンプレミスの SQL Server データベースから Azure SQL Database への移行を妨げる可能性がある互換性の問題を反映しています。 また、それらの問題への対処に役立つ推奨事項も確認できます。

6. 特定のオプションを選択して、移行を妨げる問題と機能の類似性の問題についての評価結果をレビューします。

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

評価結果をレビューし、選択したデータベースが Azure SQL Database の単一データベースまたはプールされたデータベースに移行可能であることを確認したら、DMA を使用してスキーマを Azure SQL Database に移行します。

> [!NOTE]
> Data Migration Assistant で移行プロジェクトを作成する前に、Azure SQL データベースが前提要件での説明に従って既にプロビジョニングされていることを確認してください。 このチュートリアルでは、Azure SQL Database の名前を **AdventureWorksAzure** としていますが、任意の名前を指定することもできます。
> [!IMPORTANT]
> SSIS を使用する場合、現在 DMA ではソース SSISDB の移行はサポートされていませんが、Azure SQL Database によってホストされている移行先 SSISDB に SSIS プロジェクト/パッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

**AdventureWorks2012** スキーマを Azure SQL Database の単一データベースまたはプールされたデータベースに移行するには、次の手順を実行します。

1. Data Migration Assistant で、新規 (+) アイコンを選択し、 **[プロジェクト タイプ]** で **[移行]** を選択します。
2. プロジェクト名を指定し、 **[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。
3. **[移行スコープ]** で、 **[Schema only]\(スキーマのみ\)** を選択します。

    上記の手順を実行すると、Data Migration Assistant のインターフェイスの表示が次の図のようになります。

    ![Data Migration Assistant プロジェクトを作成する](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. **[作成]** を選択してプロジェクトを作成します。
5. Data Migration Assistant で、SQL Server のソース接続詳細を指定し、 **[接続]** を選択した後、**AdventureWorks2012** データベースを選択します。

    ![Data Migration Assistant のソース接続詳細](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. **[次へ]** を選択し、 **[Connect to target server]\(対象サーバーへの接続\)** で Azure SQL データベースのターゲット接続の詳細を指定し、 **[接続]** を選択し、Azure SQL データベースで事前プロビジョニングした **AdventureWorksAzure** データベースを選択します。

    ![Data Migration Assistant のターゲット接続詳細](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. **[次へ]** を選択して **[オブジェクトの選択]** 画面に進みます。この画面では、Azure SQL Database にデプロイする必要がある **AdventureWorks2012** データベース内のスキーマ オブジェクトを指定できます。

    既定では、すべてのオブジェクトが選択されています。

    ![SQL スクリプトを生成する](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. **[SQL スクリプトの生成]** を選択して SQL スクリプトを作成し、スクリプトにエラーがないかを確認します。

    ![スキーマ スクリプト](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. **[Deploy schema]\(スキーマのデプロイ\)** を選択して Azure SQL データベースにスキーマをデプロイし、スキーマがデプロイされたら、ターゲット サーバーに異常がないかをチェックします。

    ![スキーマをデプロイする](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、 **[リソース プロバイダー]** を選択します。

    ![リソース プロバイダーの表示](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。

5. 既存の VNet を選択するか、新しいものを作成します。

    この VNet が Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL データベース インスタンスへのアクセスを提供します。

    Azure portal で VNet を作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)」を参照してください。

6. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

     ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

    ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

3. **[+ 新しい移行プロジェクト]** を選択します。
4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、 **[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **[SQL Server]** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **[Azure SQL Database]** を選択し、 **[アクティビティの種類を選択します]** で **[オフライン データの移行]** を選択します。 

    ![Database Migration Service プロジェクトを作成する](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[移行ソースの詳細]** 画面で、ソース SQL Server インスタンスの接続の詳細を指定します。

    ソース SQL Server インスタンス名には、必ず完全修飾ドメイン名 (FQDN) を使用してください。 DNS の名前解決ができない場合は、IP アドレスを使用することもできます。

2. 信頼できる証明書をソース サーバーにインストールしていない場合は、 **[Trust server certificate]\(サーバー証明書を信頼する\)** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書を使用して暗号化されている SSL 接続のセキュリティは強力ではありません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 運用環境や、インターネットに接続されているサーバーでは、自己署名証明書を使用した SSL を信頼しないでください。

   ![ソースの詳細](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > SSIS を使用する場合、現在 DMS ではソース SSISDB の移行はサポートされていませんが、Azure SQL Database によってホストされている移行先 SSISDB に SSIS プロジェクト/パッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[保存]** を選択し、 **[移行のターゲットの詳細]** 画面でターゲット Azure SQL Database Server の接続の詳細を指定します。これは、Data Migration Assistant を使用して **AdventureWorks2012** スキーマをデプロイした、事前プロビジョニング済みの Azure SQL Database です。

    ![ターゲットを選択する](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service は既定でターゲット データベースを選択します。

    ![ターゲット データベースにマップする](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. **[保存]** を選択し、 **[テーブルの選択]** 画面でテーブルの一覧を展開して、影響を受けるフィールドの一覧を確認します。

    Azure Database Migration Service では、ターゲット Azure SQL データベース インスタンスに存在する空のソース テーブルがすべて自動的に選択されます。 データが既に含まれているテーブルを再移行する場合は、このブレードでテーブルを明示的に選択する必要があります。

    ![テーブルを選択する](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. **[保存]** を選択し、 **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** テキスト ボックスで、移行アクティビティの名前を指定します。

5. **[検証オプション]** セクションを展開して **[検証オプションの選択]** 画面を表示し、移行したデータベースで**スキーマ比較**、**データの整合性**、**クエリの正確さ**を検証するかどうかを指定します。

    ![検証オプションを選択する](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. **[保存]** を選択し、概要をレビューして、ソースとターゲットの詳細が先ほど指定した内容に一致していることを確認します。

    ![移行の概要](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>移行を実行する

- **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示され、アクティビティの **[状態]** は **[Pending]\(保留\)** になっています。

    ![アクティビティの状態](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

    ![完了したアクティビティの状態](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. 移行が完了したら、 **[レポートのダウンロード]** を選択して、移行プロセスに関連する詳細情報を一覧表示したレポートを取得します。

3. ターゲットの Azure SQL Database サーバーにターゲット データベースがあることを確認します。

### <a name="additional-resources"></a>その他のリソース

- [Azure Data Migration Service (DMS) を使用した SQL 移行](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587)に関するハンズオン ラボ。
- Azure SQL Database へのオンライン移行を実行する際の既知の問題と制限事項については、[Azure SQL Database のオンライン移行に伴う既知の問題と回避策](known-issues-azure-sql-online.md)に関する記事を参照してください。
- Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](https://docs.microsoft.com/azure/dms/dms-overview)」を参照してください。
- Azure SQL Database については、「[Azure SQL Database サービスとは](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)」を参照してください。
