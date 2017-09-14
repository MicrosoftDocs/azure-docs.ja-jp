---
title: "Cortana Intelligence ソリューション評価ツール | Microsoft Docs"
description: "Microsoft パートナーとして開発した Cortana Intelligence ソリューションを AppSource に公開するために従う必要のあるすべての手順を紹介します。"
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams;v-bruham;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: e79ca131bddc9e65dd24da109b9e8a08606f7bc6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

--- 
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Cortana Intelligence ソリューション評価ツール
## <a name="overview"></a>概要
Cortana Intelligence ソリューション評価ツールを使用して、開発した高度な分析ソリューションが Microsoft によって推奨されているベスト プラクティスに準拠しているかどうかを評価できます。 Microsoft は、パートナー各社 (ISV/SI) と協力して、高品質のソリューションを顧客、リセラー、および導入パートナーに提供できることを嬉しく思っています。 このガイドでは、開発したソリューションに対してこのソリューション評価ツールを使用する方法と、チェックの対象となる具体的なベスト プラクティスについて説明します。

## <a name="getting-started"></a>使用の開始
Cortana Intelligence ソリューション評価ツールを[ダウンロード](https://aka.ms/aa-evaluation-tool-download)してインストールしてください。

前提条件:
- Windows 10: [Windows 10 の公式サイト](https://www.microsoft.com/en-us/windows)
- Azure Powershell: [Azure PowerShell をインストールして構成します](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)。

## <a name="identifying-your-app"></a>アプリの識別
インストールが完了したら、ツールを開いて最初の評価を開始します。

![評価ツールを開く](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

ソリューションの識別情報を入力します。

![Azure サブスクリプションに接続する](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Azure サブスクリプションに接続し、アプリが含まれているリソース グループを指定します。

![リソースを選択する](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

リソース グループが読み込まれたら、ソリューションに含まれているリソースを選択し、データ リソースのアクセシビリティが次のいずれかを識別します。
- データの取り込み
- 消費
- 内部

この情報は、ソリューションがさまざまなコンポーネントをどのように利用するかを理解し、ユーザー向けのコンポーネントがベスト プラクティスと一致していることを保証するために使用されます。

### <a name="ingestion"></a>データの取り込み
ここでの取り込みとは、何らかのデータ ソースが利用され、ソリューションの外部からデータがプルされることを意味するか、ソリューションの外部のサービスにより何らかのデータ ソースが利用され、データがソリューションにプッシュされることを意味します。

### <a name="consumption"></a>使用量
ここでの使用とは、何らかのデータ ソースが利用され、直接的または間接的にエンド ユーザーにデータがプッシュされることを意味します。 For example:
- PowerBI からの直接クエリで使用されるデータセット。
- WebApp 内でクエリされるデータセット。

>[!NOTE]
特定のリソースが取り込みと消費の両方で使用される場合は、**[消費]** を選択してください。

### <a name="internal"></a>内部
アプリケーションの内部処理でのみ使用されるデータ リソースには、[内部] を使用します。

次に、前の手順で指定したデータベースの有効な資格情報を提供することを求められます。

![テストの前提条件を設定する](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>ソリューションのテスト ケース
ソリューション ツールは、自動化されたテストのコレクションをソリューションに対して実行します。

![テストの実行を設定する](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

テストが完了すると、ソリューションが要件に準拠していないことの説明または正当な理由を示すことを求められます。

![ビジネス上の正当な理由を示す](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

たとえば、ソリューションが Azure SQL DW に対して発行する場合、評価テストは、Azure Analysis Services にも発行することを要求します。 

ソリューションで、Azure Analysis Services ではなく SQL Server Analysis Services を実行する IaaS 仮想マシンを使用している場合があります。 これは、テスト失敗の許容可能な理由になります。
## <a name="packaging-your-evaluation-results"></a>評価結果のパッケージ化
テスト ケースが完了すると、評価パッケージが zip ファイルにエクスポートされ、評価ツールに関するフィードバックを提供することを求められます。 

AppSource に追加するための承認を得る前に、ソリューションを評価するためにこのテスト結果の zip ファイルを Microsoft と共有する必要があります。

![評価ツールを採点する](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

この記事の上のセクションはツールのさまざまな機能を取り上げました。ここからはこのツールで評価する際の各種ベスト プラクティスについて確認します。

## <a name="security-evaluation-considerations"></a>セキュリティの評価に関する考慮事項
### <a name="databases-should-use-azure-active-directory-authentication"></a>データベースは Azure Active Directory 認証を使用する必要があります
ソリューションの Azure SQL または Azure SQL DW リソースは、Azure Active Directory (AAD) 認証を使用して有効にする必要があります。 AAD は、すべての ID とロールを管理する単一の場所を提供します。

| 詳細情報 | 参照する記事 |
| --- | --- |
| SQL Database と SQL Data Warehouse で使用する AAD | [Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse を認証する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) |
| AAD の構成と管理 | [SQL Database または SQL Data Warehouse で Azure Active Directory 認証を構成して管理する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) |
| Azure WebApps 認証 | [Azure App Service での認証および承認](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-overview) |
| WebApps を AAD を使用して構成する | [Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](https://docs.microsoft.com/en-us/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>エンドユーザーがアクセスできるデータセットはロールベースのアクセス制御をサポートする必要があります
評価ツールの実行中に、レポート リソースまたは発行リソースを指定することを求められます。 これらのリソースは、開発者ではなく、エンドユーザーがアクセスすることを前提としています。 エンドユーザーが許可されたデータのみにアクセスできることを保証するために、これらのリソースにはロールベースのアクセス制御 (RBAC) を用意する必要があります。

具体的には、次の Azure リソースは RBAC を使用して構成でき、許容可能であるとみなされます。
- Secure HDInsight: [ドメイン参加済み HDInsight クラスターでの Hadoop セキュリティの概要](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)
- Azure SQL。[AAD 認証と Azure SQL]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)に関するページを参照してください。
- Azure Analysis Services。「[Manage database roles and users for Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users)」(Azure Analysis Services のデータベース ロールとユーザーの管理) を参照してください
- Azure SQL Data Warehouse (SQL DW は RBAC をサポートしないため、エンドユーザーによる直接アクセスは推奨されないことに注意してください)。

RBAC をサポートする別のリソースの種類を使用している場合は、その旨をテスト ケースの正当な理由として指定してください。

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store では保存データの暗号化を使用する必要があります
Azure Data Lake Store (ADLS) は、ADLS で管理される暗号化キーを使用した保存データの暗号化を既定でサポートしています。 Azure Key Vault を使用した暗号化を構成することもできます。

ADLS 暗号化設定を指定する方法については、「[Azure Data Lake Store アカウントを作成する](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account)」を参照してください。

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL と Azure SQL Data Warehouse では暗号化を使用する必要があります
Azure SQL と Azure SQL DW はどちらも、データとログ ファイルの両方をリアルタイムで暗号化および復号化する Transparent Data Encryption (TDE) をサポートしています。

| 詳細情報 | 参照する記事 |
| --- | --- |
| 透過的なデータ暗号化 (TDE) | [透過的なデータ暗号化](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL Data Warehouse と TDE | [SQL Data Warehouse の暗号化 TDE T-SQL](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql)に関するページ |
| Azure SQL を TDE を使用するように構成する | [Azure SQL Database での Transparent Data Encryption](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Azure SQL を Always Encrypted を使用するように構成する | [SQL Database の Always Encrypted と Azure Key Vault](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault) に関するページ|

TDE に加え、Azure SQL では、Always Encrypted もサポートしています。これは、保存時とクライアントとサーバー間の移動中だけデータが暗号化されるのではなく、サーバーでのコマンドの実行中にデータが使用されている間も暗号化されることを保証する新しいデータ暗号化テクノロジです。

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>すべての Virtual Machines は Azure Marketplace からデプロイする必要があります
AppSource 全体で一貫性のあるレベルのセキュリティを提供するために、Cortana Intelligence ソリューションの一部としてデプロイされるすべての仮想マシンは、認定を受けたうえで Azure Marketplace に公開することが要求されています。

現在の Azure Marketplace イメージの一覧を検索するには、[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute) を参照してください。

Azure Marketplace に仮想マシン イメージを公開する方法については、「[Azure Marketplace 向け仮想マシン イメージ作成ガイド](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)」を参照してください。

## <a name="scalability-evaluation-considerations"></a>スケーラビリティの評価に関する考慮事項
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Cortana Intelligence ソリューションにはスケーラブルなビッグ データ プラットフォームを含める必要があります
Cortana Intelligence ソリューションは、非常に大きなデータ サイズに対応できる必要があります。 Azure では、次の 2 つのペタバイト規模のデータ プラットフォームのいずれかを含める必要があることを意味します。
- Azure Data Lake Store
- Azure SQL Data Warehouse

ソリューションがこれらのデータ サイズをサポートする必要がない、または代替データ プラットフォームを使用している場合は、それをテスト ケースの正当な理由の中で説明してください。
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Cortana Intelligence ソリューションには専用の取り込みデータ環境を含める必要があります
Cortana Intelligence ソリューションは、通常は、リレーショナル データ ソースにデータを直接挿入することは避ける必要があります。 代わりに、構造化されていない環境に生データを格納し、Azure Data Factory を使用して、リレーショナル ストアにべき等挿入/更新を行う必要があります。

Azure Data Factory を使用してデータをコピーする方法の詳細については、「[チュートリアル: コピー アクティビティがあるパイプラインを Visual Studio で作成する](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-copy-activity-tutorial-using-visual-studio)」を参照してください。

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Azure SQL Data Warehouse では PolyBase を使用してデータを取り込む必要があります
Azure SQL DW は、スケーラブルな並列のデータ取り込みを行う PolyBase をサポートしています。 PolyBase によって、Azure Blob Storage または Azure Data Lake Store に格納されている外部データセットに対するクエリを Azure SQL DW で発行することができます。 これは、他の一括更新方法に比べ、優れたパフォーマンスを提供します。

PolyBase と Azure SQL DW の概要については、「[SQL Data Warehouse で PolyBase によってデータを読み込む](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase)」を参照してください。

PolyBase と Azure SQL DW のベスト プラクティスについては、「[SQL Data Warehouse で PolyBase を使用するためのガイド](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide)」を参照してください。

## <a name="availability-evaluation-considerations"></a>可用性の評価に関する考慮事項

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>エンドユーザーがアクセスできるデータセットは大量の同時実行ユーザーをサポートする必要があります
評価ツールの実行中に、レポート リソースまたは発行リソースを指定することを求められます。 これらのリソースは、開発者ではなく、エンドユーザーがアクセスすることを前提としています。 これらのリソースは、中程度から大量の数の同時実行ユーザーをサポートする必要があります。

具体的には、Azure SQL Data Warehouse をエンドユーザーが使用できる唯一のデータ ソースにすべきではありません。 Azure SQL DW をパワー ユーザー向けの リソースとして提供する場合、一般的なユーザーは Azure Analysis Services を利用できるようにする必要があります。

Azure SQL DW の同時実行の制限の詳細については、「[SQL Data Warehouse での同時実行とワークロード管理](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency)」を参照してください。

Azure Analysis Services の詳細については、「[Azure Analysis Services とは](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview)」を参照してください。

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Azure SQL リソースには読み取り専用のフェールオーバー レプリカが必要です
Azure SQL データベースは、セカンダリ インスタンスへの geo レプリケーションをサポートしています。 このインスタンスをフェールオーバー インスタンスとして使用することで、高可用性アプリケーションを提供できます。

Azure SQL データベースの geo レプリケーションの詳細については、[SQL Database の GEO レプリケーションの概要](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview)に関するページを参照してください。

Azure SQL の geo レプリケーションを構成する方法の手順については、「[Transact-SQL を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-transact-sql)」を参照してください。

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Azure SQL Data Warehouse で地理冗長バックアップが有効になっている必要があります
Azure SQL DW は、geo 冗長ストレージに対する毎日のバックアップをサポートしています。 この geo レプリケーションによって、プライマリ リージョンに格納されているスナップショットにアクセスできない状況でも、データ ウェアハウスを復元できることが保証されます。 この機能は既定で有効になっており、Cortana Intelligence ソリューションで無効にすべきではありません。

Azure SQL DW のバックアップと復元の詳細については、「[SQL Data Warehouse のバックアップ](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-backups)」を参照してください。

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>仮想マシンは可用性セットを使用して構成する必要があります
Azure の仮想マシンは、計画されたメンテナンス イベントと計画外のメンテナンス イベントの影響を最小限に抑えるために、可用性セット内に構成する必要があります。

Azure の仮想マシンの可用性の詳細については、「[Azure での Windows 仮想マシンの可用性の管理](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability)」を参照してください。

## <a name="other-evaluation-considerations"></a>その他の評価に関する考慮事項
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Cortana Intelligence アプリはデータのオーケストレーションを行う一元化ツールを使用する必要があります
データの移行や変換の管理とスケジュール設定を行う単一のツールを使用することで、ミッション クリティカルなデータの一貫性が確保され、 再試行ロジック、依存関係の管理、アラート/ログなどを行うための明確なロジックが用意されます。Azure でのデータのオーケストレーションでは、[Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-introduction) を使用することをお勧めします。

データのオーケストレーションを行うために Azure Data Factory 以外のツールを使用している場合は、使用しているツールを記述してください。
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Azure Machine Learning モデルは Azure Data Factory を使用して再トレーニングする必要があります
Azure Machine Learning (AzureML) には、予測モデルと機械学習のパイプラインを作成してデプロイするための使いやすいツールが用意されています。 ただし、これらの AzureML モデルの運用デプロイでは、1 つの固定されたデータセットをベースにするのではなく、現実世界の現象の変化に適応できるようにすることが重要です。

AzureML の再トレーニング Web サービスの作成の詳細については、「[プログラムによる Machine Learning のモデルの再トレーニング](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-retrain-models-programmatically)」を参照してください。

Azure Data Factory を使用したモデルのトレーニング プロセスの自動化の詳細については、「[更新リソース アクティビティを使って Azure Machine Learning モデルを更新する](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-azure-ml-update-resource-activity)」を参照してください。

## <a name="existing-documentation"></a>既存のドキュメント
[Microsoft Azure Certified でクラウド ビジネスを拡大](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure Certified for Cortana Intelligence](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)


