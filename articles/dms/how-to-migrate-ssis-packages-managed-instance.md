---
title: SQL Server Integration Services パッケージを Azure SQL Database マネージド インスタンスに移行する | Microsoft Docs
description: SQL Server Integration Services パッケージを Azure SQL Database マネージド インスタンスに移行する方法について説明します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082184"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>SQL Server Integration Services パッケージを Azure SQL Database マネージド インスタンスに移行する
SQL Server Integration Services (SSIS) を使用していて、その SSIS プロジェクト/パッケージを、SQL Server によってホストされている配置元の SSISDB から、Azure SQL Database マネージド インスタンスによってホストされている配置先の SSISDB に移行する場合は、Azure Database Migration Service を使用できます。

使用する SSIS のバージョンが 2012 より前の場合、または SSIS 以外のパッケージ ストアの種類を使用する場合は、SSIS プロジェクト/パッケージを移行する前に、Integration Services プロジェクトの変換ウィザード (これも SSMS から起動できます) を使用して変換する必要があります。 詳細については、[プロジェクト デプロイ モデルへのプロジェクトの変換](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)に関する記事を参照してください。

> [!NOTE]
> 現在、Azure Database Migration Service (DMS) は Azure SQL Database をターゲット移行先としてサポートしていません。 SSIS プロジェクト/パッケージを Azure SQL Database に再デプロイするには、記事「[SQL Server Integration Services パッケージを Azure SQL Database に再デプロイする](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

この記事では、次のことについて説明します。
> [!div class="checklist"]
>
> * 配置元の SSIS プロジェクト/パッケージを評価する。
> * SSIS プロジェクト/パッケージを Azure に移行する。

## <a name="prerequisites"></a>前提条件

これらの手順を完了するには、以下が必要です。

* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成すること。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 詳細については、記事「[Azure Database Migration Service を使用して Azure SQL Database Managed Instance を移行するためのネットワーク トポロジ]( https://aka.ms/dmsnetworkformi)」を参照してください。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。
* VNet ネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートが確実にブロックされないようにします。443、53、9354、445、12000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)に関する記事を参照してください。
* [ソース データベース エンジンへのアクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)を構成すること。
* Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開くこと。既定では TCP ポート 1433 が使用されます。
* 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスし、SMB ポート 445 経由でファイルにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* SSISDB をホストする Azure SQL Database マネージド インスタンス。 作成する必要がある場合は、記事「[Azure SQL Database マネージド インスタンスの作成](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)」を参照してください。
* ソース SQL Server への接続と、ターゲットのマネージド インスタンスに使用するログインが、sysadmin サーバー ロールのメンバーであることを確認すること。
* 配置先の SSISDB が Azure SQL Database マネージド インスタンスでホストされている場合は、Azure-SSIS Integration Runtime (IR) を含む Azure Data Factory (ADF) に SSIS がプロビジョニングされていることを確認すること (記事「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)」に記載されています)。

## <a name="assess-source-ssis-projectspackages"></a>配置元の SSIS プロジェクト/パッケージを評価する

配置元の SSISDB の評価が Database Migration Assistant (DMA) にまだ統合されていない場合でも、ご利用の SSIS プロジェクト/パッケージは、Azure SQL Database マネージド インスタンス上でホストされている配置先の SSISDB に再デプロイされるときに評価/検証されます。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

    ![ポータルのサブスクリプションの表示](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーの表示](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、**Azure Database Migration Service** を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. DMS のインスタンスの作成先となる場所を選択します。

5. 既存の VNet を選択するか、新規に作成します。

    この VNet によって、Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL Database マネージド インスタンスへのアクセスが提供されます。

    Azure portal で VNet を作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)に関する記事を参照してください。

    その他の詳細については、記事「[Azure Database Migration Service を使用して Azure SQL DB Managed Instance を移行するためのネットワーク トポロジ](https://aka.ms/dmsnetworkformi)」を参照してください。

6. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![DMS サービスを作成する](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスのインスタンスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. **[Azure Database Migration Service]** 画面で、作成したインスタンスの名前を検索して、そのインスタンスを選択します。

3. **[+ 新しい移行プロジェクト]** を選択します。

4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、 **[Source server type]\(ソース サーバーの種類\)** ボックスで **[SQL Server]** を選択した後、 **[ターゲット サーバーの種類]** ボックスで **[Azure SQL Database Managed Instance]** を選択し、 **[アクティビティの種類を選択します]** で **[SSIS パッケージ移行]** を選択します。

   ![DMS のプロジェクトを作成する](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. **[作成]** を選択してプロジェクトを作成します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[移行ソースの詳細]** 画面で、ソース SQL Server の接続の詳細を指定します。

2. 信頼できる証明書をサーバーにインストールしていない場合は、 **[サーバー証明書を信頼する]** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書を使用して暗号化されている SSL 接続のセキュリティは強力ではありません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 運用環境や、インターネットに接続されているサーバーでは、自己署名証明書を使用した SSL を信頼しないでください。

   ![ソースの詳細](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[移行のターゲットの詳細]** 画面で、ターゲットの接続の詳細を指定します。

     ![ターゲットの詳細](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. **[保存]** を選択します。

## <a name="review-the-migration-summary"></a>移行の概要を確認する

1. **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** ボックスに移行アクティビティの名前を指定します。

2. **[SSIS project(s) and environment(s) overwrite option]\(SSIS プロジェクトと環境上書きオプション\)** に、既存の SSIS プロジェクトと環境を上書きするか無視するかを指定します。

    ![移行プロジェクトの概要](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 移行プロジェクトに関連付けられた詳細を確認します。

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

## <a name="next-steps"></a>次の手順

* 「[Microsoft Database Migration Guide](https://datamigration.microsoft.com/)」にある移行ガイドを確認する。
