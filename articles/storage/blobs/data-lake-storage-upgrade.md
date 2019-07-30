---
title: ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする
description: Azure Data Lake Storage Gen2 を使用するようにソリューションをアップグレードします
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4d5bf318a86e989ce66bffbd2aa72638ea477ab1
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673922"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする

ビッグ データ分析ソリューションで Azure Data Lake Storage Gen1 を使用している場合、Azure Data Lake Storage Gen2 を使用するようにソリューションをアップグレードするには、このガイドが役に立ちます。 このドキュメントを使用すると、Data Lake Storage Gen1 でのソリューションの依存関係を評価できます。 また、このガイドではアップグレードを計画して実行する方法も示します。

次のタスクについて説明します。

:heavy_check_mark:アップグレードの準備状態を評価する

:heavy_check_mark:アップグレードを計画する

:heavy_check_mark:アップグレードを実行する

## <a name="assess-your-upgrade-readiness"></a>アップグレードの準備状態を評価する

目標は、Data Lake Storage Gen1 に存在するすべての機能を、Data Lake Storage Gen2 で使用できるようにすることです。 それらの機能が SDK や CLI などで公開される方法は、Data Lake Storage Gen1 と Data Lake Storage Gen2 では異なる場合があります。 Data Lake Storage Gen1 で動作するアプリケーションとサービスは、Data Lake Storage Gen2 でも同じように動作できる必要があります。 最後に、一部の機能は、Data Lake Storage Gen2 ではすぐには使用できません。 使用できるようになったら、このドキュメントでお知らせします。

以下のセクションは、Data Lake Storage Gen2 へのアップグレードをどのような方法でいつ行うのが最もよいかを決定するのに役立ちます。

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 ソリューションのコンポーネント

分析ソリューションまたはパイプラインで Data Lake Storage Gen1 を使用するときは普通、エンド ツー エンドの全体的な機能を実現するために多くの追加テクノロジを導入します。 こちらの[記事](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)では、データの取り込み、処理、使用を含むデータ フローのさまざまなコンポーネントについて説明されています。

さらに、これらのコンポーネントをプロビジョニング、管理、監視するための横断的コンポーネントがあります。 各コンポーネントは、それらに最適なインターフェイスを使用して Data Lake Storage Gen1 で動作します。 ソリューションの Data Lake Storage Gen2 へのアップグレードを計画するときは、使用されているインターフェイスに留意する必要があります。 インターフェイスはそれぞれ要件が異なるため、管理インターフェイスとデータ インターフェイスの両方をアップグレードする必要があります。

![Data Lake Storage ソリューションのコンポーネント](./media/data-lake-storage-upgrade/solution-components.png)

上の**図 1** は、ほとんどの分析ソリューションで見られる機能コンポーネントを示したものです。

**図 2** は、特定のテクノロジを使用してそれらのコンポーネントを実装する方法の例です。

**図 1** の保存 (Storing) 機能が、Data Lake Storage Gen1 (**図 2**) で提供されます。 データ フローのさまざまなコンポーネントが REST API または Java SDK を使用して Data Lake Storage Gen1 と相互作用していることに注意してください。 また、横断機能コンポーネントも Data Lake Storage Gen1 と相互作用していることにも注意してください。 プロビジョニング コンポーネントでは Azure リソース テンプレートが使用されますが、Azure Monitor ログが使用される監視コンポーネントでは Data Lake Storage Gen1 からの運用データが利用されます。

Data Lake Storage Gen1 から Data Lake Storage Gen2 を使用するようにソリューションをアップグレードするには、データとメタデータをコピーし、データ フローを再フックしてから、すべてのコンポーネントが Data Lake Storage Gen2 で動作できるようにする必要があります。

以下のセクションでは、より適切な決定を下すのに役立つ情報を提供します。

:heavy_check_mark:プラットフォームの機能

:heavy_check_mark:プログラミング インターフェイス

:heavy_check_mark:Azure のエコシステム

:heavy_check_mark:パートナーのエコシステム

:heavy_check_mark:運用に関する情報

各セクションでは、アップグレードに "必要なもの" を判断できます。 機能を使用できることを確認した後、または適当な回避策があることを確認した後は、このガイドの「[アップグレードの計画](#planning-for-an-upgrade)」セクションに進みます。

### <a name="platform-capabilities"></a>プラットフォームの機能

このセクションでは、Data Lake Storage Gen1 プラットフォームの機能のうち、Data Lake Storage Gen2 で現在使用できるものについて説明します。

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - 目標 | Data Lake Storage Gen2 - 使用可否の状態  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| データの編成| フォルダーおよびファイルとしてのデータの格納をサポートします | フォルダーおよびファイルだけでなくオブジェクト/BLOB としてのデータの格納をサポートします - [リンク](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | フォルダーおよびファイルとしてのデータの格納をサポートします – "*既に使用可能*" <br><br> オブジェクト/BLOB として格納されているデータをサポートします - "*まだ使用不可能*" |
| 名前空間| 階層構造 | 階層構造 |  "*既に使用可能*"  |
| API  | HTTPS 経由の REST API | HTTP/HTTPS 経由の REST API| "*既に使用可能*" |
| サーバー側 API| [WebHDFS 互換の REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob Service REST API、[Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – "*既に使用可能*" <br><br> Azure Blob Service REST API – "*まだ使用不可能*"       |
| Hadoop ファイル システム クライアント | はい ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | はい ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | "*既に使用可能*"  |  
| データ操作 - 認可  | Azure Active Directory ID に基づくファイルおよびフォルダー レベルの POSIX アクセス制御リスト (ACL)  | Azure Active Directory ID に基づくファイルおよびフォルダー レベルの POSIX アクセス制御リスト (ACL)、アカウント レベルの認可のための[共有キー](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)、コンテナーにアクセスするためのロールベースのアクセス制御 ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) | "*既に使用可能*" |
| データ操作 – ログ  | はい | サポート チケットを使用した特定期間のログに対する 1 回限りの要求、Azure Monitoring の統合 | サポート チケットを使用した特定期間のログに対する 1 回限りの要求 – "*既に使用可能*"<br><br> Azure Monitoring の統合 – "*まだ使用不可能*" |
| 保存データの暗号化 | 透過的、Azure Key Vault でのサービス管理キーおよび顧客管理キーによるサーバー側 | 透過的、Azure Key Vault でのサービス管理キーおよび顧客管理キーによるサーバー側 | サービス管理キー – "*既に使用可能*"<br><br> 顧客管理キー – "*既に使用可能*"  |
| 管理操作 (アカウントの作成など) | [ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | [ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | "*既に使用可能*"|
| Developer SDK | .NET、Java、Python、Node.js  | .NET、Java、Python、Node.js、C++、Ruby、PHP、Go、Android、iOS| "*まだ使用不可能*" |
| |並列分析ワークロードに最適化されたパフォーマンス。 高スループットおよび高 IOPS。 | 並列分析ワークロードに最適化されたパフォーマンス。 高スループットおよび高 IOPS。 | "*既に使用可能*" |
| Virtual Network (VNet) のサポート  | [仮想ネットワーク統合の使用](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Azure Storage のサービス エンドポイントの使用](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | "*既に使用可能*" |
| サイズ制限 | アカウント サイズ、ファイル サイズ、ファイル数に制限はありません。 | アカウント サイズまたはファイル数に制限なし。 ファイル サイズは 5 TB に制限。 | "*既に使用可能*"|
| geo 冗長| ローカル冗長 (LRS) | ローカル冗長 (LRS)、ゾーン冗長 (ZRS)、geo 冗長 (GRS)、読み取りアクセス geo 冗長 (RA-GRS)。詳しくは[こちら](https://docs.microsoft.com/azure/storage/common/storage-redundancy)を参照| "*既に使用可能*" |
| リージョン別の提供状況 | 詳細については、 [こちら](https://azure.microsoft.com/regions/) | すべての [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | "*既に使用可能*"                                                                                                                           |
| 料金                                       | 詳細については、 [価格](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | 詳細については、 [価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| 可用性 SLA                            | [SLA を参照](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [SLA を参照](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | "*既に使用可能*"                                                                                                                           |
| データ管理                             | ファイルの有効期限                                                                                                                                        | ライフサイクル ポリシー                                                                                                                                                                                                                                                                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                       |

### <a name="programming-interfaces"></a>プログラミング インターフェイス

次の表では、カスタム アプリケーションで利用可能な API のセットをについて説明します。 わかりやすくするため、API セットを管理 API とファイル システム API の 2 つに分けてあります。

管理 API がアカウントの管理に役立つのに対し、ファイル システム API はファイルとフォルダーの操作に役立ちます。

|  API セット                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Data Lake Storage Gen2 での使用可否 - 共有キー認証を使用 | Data Lake Storage Gen2 での使用可否 - OAuth 認証を使用                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK - 管理                  | [リンク](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *サポートされていません*                                                      | "*既に使用可能*" - [リンク](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK - ファイル システム                  | [リンク](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | "*まだ使用不可能*"                                                | "*まだ使用不可能*"                                                                                                                                             |
| Java SDK - 管理                  | [リンク](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *サポートされていません*                                                      | "*既に使用可能*" - [リンク](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK - ファイル システム                  | [リンク](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | "*まだ使用不可能*"                                                | "*まだ使用不可能*"                                                                                                                                             |
| Node.js - 管理                   | [リンク](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | サポートされていません                                                      | "*既に使用可能*" - [リンク](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js - ファイル システム                   | [リンク](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | "*まだ使用不可能*"                                                | "*まだ使用不可能*"                                                                                                                                             |
| Python - 管理                    | [リンク](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *サポートされていません*                                                      | "*既に使用可能*" - [リンク](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python - ファイル システム                    | [リンク](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | "*まだ使用不可能*"                                                | "*まだ使用不可能*"                                                                                                                                             |
| REST API - 管理                  | [リンク](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *サポートされていません*                                                      | "*既に使用可能*" -                                                                                                                                               |
| REST API - ファイル システム                  | [リンク](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | "*既に使用可能*"                                                    | "*既に使用可能*" - [リンク](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell - 管理/ファイル システム | [リンク](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | 管理 - ファイル システムではサポートされない - "*まだ使用不可能*"        | 管理 – "*既に使用可能" -* [リンク](https://docs.microsoft.com/powershell/module/az.storage) <br><br>ファイル システム - "*まだ使用不可能*" |
| CLI – 管理                       | [リンク](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *サポートされていません*                                                      | "*既に使用可能*" - [リンク](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI - ファイル システム                       | [リンク](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | "*まだ使用不可能*"                                                | "*まだ使用不可能*"                                                                                                                                             |
| Azure Resource Manager テンプレート - 管理             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *サポートされていません*                                                      | "*既に使用可能*" - [リンク](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure のエコシステム

Data Lake Storage Gen1 を使用するときは、さまざまな Microsoft サービスと製品をエンド ツー エンドのパイプラインで使用できます。 これらのサービスと製品は、Data Lake Storage Gen1 で直接的または間接的に動作します。 次の表では、Data Lake Storage Gen1 で動作するように変更されているサービス、および現在 Data Lake Storage Gen2 と互換性があるサービスの一覧を示します。

| **領域**             | **Data Lake Storage Gen1 での使用可否**                                                                                                                                    | **Data Lake Storage Gen2 での使用可否 - 共有キー認証を使用**                                                                                                           | **Data Lake Storage Gen2 での使用可否 - OAuth を使用**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| 分析フレームワーク  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | "*既に使用可能*"                                                                                                                                                              | "*既に使用可能*"                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - "*既に使用可能*"、HDInsight 4.0 - "*まだ使用不可能*"      | HDInsight 3.6 ESP – "*既に使用可能*" <br><br>  HDInsight 4.0 ESP - "*まだ使用不可能*"                                                                 |
|                      | Databricks Runtime 3.1 以降                                                                                                                                               | [Databricks Runtime 5.1 以降](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) - "*既に使用可能*" | [Databricks Runtime 5.1 以降](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – "*既に使用可能*"                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *サポートされていません*                                                                                                                                                              | "*既に使用可能*" [リンク](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| データ統合     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [バージョン 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – "*既に使用可能*" バージョン 1 – "*非サポート*"                               | [バージョン 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – "*既に使用可能*" <br><br> バージョン 1 – "*非サポート*"  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *サポートされていません*                                                                                                                                                              | *サポートされていません*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
| IoT                  | [Event Hubs - Capture](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
| 消費          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
| 生産性         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *サポートされていません*                                                                                                                                                              | アカウント管理 *– "既に使用可能"* <br><br>データ操作 *–*  "*まだ使用不可能*"                                                                   |
|                      | [Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | "*既に使用可能*"                                                                                                                                                              | "*既に使用可能*"                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | "*まだ使用不可能*"                                                                                                                                                          | "*まだ使用不可能*"                                                                                                                             |

### <a name="partner-ecosystem"></a>パートナーのエコシステム

次の表では、Data Lake Storage Gen1 で動作するように変更されているサード パーティのサービスと製品の一覧を示します。 また、現時点で Data Lake Storage Gen2 と互換性があるものも示されています。

| 領域            | パートナー  | 製品/サービス  | Data Lake Storage Gen1 での使用可否                                                                                                                                               | Data Lake Storage Gen2 での使用可否 - 共有キー認証を使用                                                   | Data Lake Storage Gen2 での使用可否 - OAuth を使用                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| 分析フレームワーク | Cloudera     | CDH                  | [リンク](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | "*まだ使用不可能*"                                                                                                  | [リンク](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [リンク](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *サポートされていません*                                                                                                                  | "*まだ使用不可能*"                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [リンク](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | "*まだ使用不可能*"                                                                                                  | "*まだ使用不可能*"                                                                                                  |
|                     | Qubole       |                      | [リンク](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | "*まだ使用不可能*"                                                                                                  | "*まだ使用不可能*"                                                                                                  |
| ETL                 | StreamSets   |                      | [リンク](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | "*まだ使用不可能*"                                                                                                  | "*まだ使用不可能*"                                                                                                  |
|                     | Informatica  |                      | [リンク](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | "*まだ使用不可能*"                                                                                                  | "*まだ使用不可能*"                                                                                                  |
|                     | Attunity     |                      | [リンク](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | "*まだ使用不可能*"                                                                                                  | "*まだ使用不可能*"                                                                                                  |
|                     | Alteryx      |                      | [リンク](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | "*まだ使用不可能*"                                                                                                  | "*まだ使用不可能*"                                                                                                  |
|                     | ImanisData   |                      | [リンク](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | "*まだ使用不可能*"                                                                                                  | "*まだ使用不可能*"                                                                                                  |
|                     | WANdisco     |                      | [リンク](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [リンク](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [リンク](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>運用に関する情報

Data Lake Storage Gen1 では特定の情報とデータが他のサービスにプッシュされて、パイプラインの運用化に役立っています。 次の表では、Data Lake Storage Gen2 での対応するサポートの使用可否を示します。

| データの種類                                                                                       | Data Lake Storage Gen1 での使用可否                                                                 | Data Lake Storage Gen2 での使用可否                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| 課金データ - 課金のためにコマース チームに送信された後、顧客が利用できるようになる測定値  | "*既に使用可能*"                                                                                             | "*既に使用可能*"                                                                                                                           |
| アクティビティ ログ                                                                                          | [リンク](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | サポート チケットを使用した特定期間のログに対する 1 回限りの要求 - "*既に使用可能*"、Azure Monitoring の統合 - "*まだ使用不可能*" |
| 診断ログ                                                                                        | [リンク](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | サポート チケットを使用した特定期間のログに対する 1 回限りの要求 - "*既に使用可能*"、Azure Monitoring の統合 - "*まだ使用不可能*" |
| メトリック                                                                                                | *サポートされていません*                                                                                               | "*既に使用可能*" - [リンク](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>アップグレードの計画

このセクションでは、このガイドの「[アップグレードの準備状態を評価する](#assess-your-upgrade-readiness)」セクションを読んであり、すべての依存関係が満たされているものとします。 Data Lake Storage Gen2 でまだ使用できない機能がある場合は、対応する回避策がわかっている場合にのみ先に進んでください。 以下のセクションでは、パイプラインのアップグレードを計画する方法についてのガイダンスを提供します。 アップグレードの実際の実行については、このガイドの「[アップグレードの実行](#performing-the-upgrade)」で説明します。

### <a name="upgrade-strategy"></a>アップグレードの戦略

アップグレードの最も重要な部分は、戦略の決定です。 この決定により、使用できる選択肢が決まります。

次の表では、データベースや Hadoop クラスターなどの移行に使用されているよく知られた戦略を示します。このガイダンスでも同様の戦略を採用し、コンテキストに合わせて変更しています。

| 戦略                   | 長所                                                                                  | 短所                                                           | 使用する局面                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| リフトアンドシフト                 | 最も簡単です。                                                                                 | データのコピー、ジョブの移動、イングレスとエグレスの移動のために、ダウンタイムが必要です                                             | 1 つの Gen1 アカウントにアクセスしているソリューションが 1 つだけであり、したがって管理された方法で簡単にまとめて移動できる、単純なソリューションの場合。                                                  |
| 1 回のコピーと増分コピー | 移行元システムがまだアクティブな間にバックグラウンドでコピーを実行することにより、ダウンタイムを短縮します。 | イングレスとエグレスを移動するためにダウンタイムが必要です。                   | コピーするデータの量が多く、リフトアンドシフトに伴うダウンタイムを許容できない場合。 移行の前に、ターゲット システム上で重大な運用データのテストが必要な場合があります。 |
| 並列導入              | ダウンタイムは最短です。移行の時間をアプリケーションごとに個別に判断できます。           | 2 つのシステム間で双方向の同期が必要であるため、最も複雑です。 | Data Lake Storage Gen1 上に構築されたすべてのアプリケーションを一括して移行することができず、増分方式で移動する必要がある、複雑なシナリオの場合。                                                      |

以下では、各戦略の手順をさらに詳しく説明します。 手順では、アップグレードに関連するコンポーネントで行うことの一覧を示します。 これには、移行元システム全体、移行先システム全体、移行元システムのイングレス元、移行元システムのエグレス先、移行元システムで実行されているジョブが含まれます。

以下の手順は規範的なものではありません。 各戦略についての考え方に関するフレームワークを設定することを意図したものです。 戦略ごとに実装のケース スタディを提供します。

#### <a name="lift-and-shift"></a>リフトアンドシフト

1. 移行元システム (イングレス元、ジョブ、エグレス先) を一時停止します。
2. 移行元システムから移行先システムにすべてのデータをコピーします。
3. すべてのイングレス元が移行先システムをポイントするようにします。 移行先システムがエグレス先をポイントするようにします。
4. すべてのジョブを移行先システムに移動し、変更して、実行します。
5. 移行元システムをオフにします。

#### <a name="copy-once-and-copy-incremental"></a>1 回のコピーと増分コピー

1. 移行元システムから移行先システムにデータをコピーします。
2. 一定の間隔で、移行元システムから移行先システムに増分データをコピーします。
3. 移行先システムがエグレス先をポイントするようにします。
4. すべてのジョブを移行先システムに移動し、変更して、実行します。
5. 都合に従って、段階的にイングレス元が移行先システムをポイントするようにします。
6. すべてのイングレス元が移行先システムをポイントするようになったら、次のようにします。
    1. 増分コピーをオフにします。
    2. 移行元システムをオフにします。

#### <a name="parallel-adoption"></a>並列導入

1. 移行先システムをセットアップします。
2. 移行元システムと移行先システムの間に双方向のレプリケーションを設定します。
3. 段階的にイングレス元が移行先システムをポイントするようにします。
4. 段階的にジョブを移行先システムに移動し、変更して、実行します。
5. 段階的に移行先システムがエグレス先をポイントするようにします。
6. 移行元のすべてのイングレス元、ジョブ、エグレス先が移行先システムで動作するようになった後、移行元システムをオフにします。

### <a name="data-upgrade"></a>データのアップグレード

アップグレードを実行するために使用する全体的な戦略 (このガイドの「[アップグレード戦略](#upgrade-strategy)」を参照) により、データのアップグレードに使用できるツールが決まります。 以下に示すツールは、現在の情報に基づく提案です。 

#### <a name="tools-guidance"></a>ツール ガイダンス

| 戦略                       | ツール                                                                                                             | 長所                                                                                                                             | 考慮事項                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **リフトアンドシフト**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | マネージド クラウド サービス                                                                                                                | 現在、データと ACL の両方をコピーできます。                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | よく知られた Hadoop 提供のるツール。このツールではアクセス許可つまり ACL をコピーできます                                                   | Data Lake Storage Gen1 と Gen2 の両方に同時に接続できるクラスターが必要です。                                                                                                                                                                                   |
| **1 回のコピーと増分コピー** | Azure Data Factory                                                                                                    | マネージド クラウド サービス                                                                                                                | 現在、データと ACL の両方をコピーできます。 ADF で増分コピーをサポートするには、データを時系列的に編成する必要があります。 増分コピー間の最短の間隔は [15 分](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger)です。 |
| **並列導入**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Azure Data Lake Storage に接続された純粋な Hadoop 環境を使用した場合は、一貫性のあるレプリケーションがサポートされます。双方向のレプリケーションがサポートされます | 純粋な Hadoop 環境を使用しない場合、レプリケーションが遅れる可能性があります。                                                                                                                                                                                                                                                  |

上記のデータ/メタデータ コピー ツールを使用することなく、Data Lake Storage Gen1 から Data Lake Storage Gen2 へのアップグレードを処理できるサード パーティがあることに注意してください (例:[Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/))。 それらでは、データ移行とワークロード移行を実行する "ワンストップ ショップ" エクスペリエンスが提供されます。 それらのエコシステムの外部にあるツールでは、帯域外のアップグレードを実行することが必要な場合があります。

#### <a name="considerations"></a>考慮事項

* 現在のガイダンスには Gen1 アカウント情報に基づく自動 Gen2 アカウント プロセスが含まれていないので、アップグレードのいずれの部分についても、開始する前に、Data Lake Storage Gen2 アカウントを最初に手動で作成する必要があります。 [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) と [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account) のアカウント作成プロセスを比較して確認してください。

* Data Lake Storage Gen2 では、サイズが 5 TB 以下のファイルのみがサポートされています。 Data Lake Storage Gen2 にアップグレードするには、サイズが 5 TB を超えないように Data Lake Storage Gen1 のファイルのサイズを変更することが必要な場合があります。

* ACL がコピーされないツールを使用する場合、または ACL をコピーしたくない場合は、移行先の適切な最上位レベルで ACL を手動で設定する必要があります。 それを行うには Storage Explorer を使用できます。 コピーするファイルとフォルダーが継承するように、それらの ACL が既定の ACL であることを確認します。

* Data Lake Storage Gen1 では、ACL を設定できる最高レベルはアカウントのルートです。 ただし、Data Lake Storage Gen2 では、ACL を設定できる最高レベルは、アカウント全体ではなく、ファイル システムのルート フォルダーです。 そのため、既定の ACL をアカウント レベルで設定する場合は、Data Lake Storage Gen2 アカウントのすべてのファイル システムにそれを複製する必要があります。

* 2 つのストレージ システム間では、ファイルの名前付けの制約が異なります。 Data Lake Storage Gen1 の方が制約が厳しいため、Data Lake Storage Gen2 から Data Lake Storage Gen1 にコピーするときに特にこれらの相違が関連します。

### <a name="application-upgrade"></a>アプリケーションのアップグレード

Data Lake Storage Gen1 または Data Lake Storage Gen2 でアプリケーションを構築する必要がある場合は、最初に、適切なプログラミング インターフェイスを選択する必要があります。 そのインターフェイスで API を呼び出すときに、適切な URI と適切な資格情報を提供する必要があります。 これら 3 つの要素 (API、URI、資格情報の提供方法) の表現は、Data Lake Storage Gen1 と Data Lake Storage Gen2 で異なるので、アプリケーションのアップグレードの一環として、これら 3 つの構成要素を適切にマップする必要があります。

#### <a name="uri-changes"></a>URI の変更

ここでの主なタスクは、プレフィックス `adl://` 付きの URI をプレフィックス `abfss://` 付きの URI に変換することです。

Data Lake Storage Gen1 の詳細な URI 形式は[こちら](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store)でせつめいされていますが、大まかには *adl://mydatalakestore.azuredatalakestore.net/\<ファイル パス\>* です。

Data Lake Storage Gen2 のファイルにアクセスするための URI 形式については[こちら](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)で詳しく説明されていますが、大まかには `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>` です。

既存のアプリケーションを調べて、Data Lake Storage Gen2 の URI を指すように URI が適切に変更されていることを確認する必要があります。 また、適切な資格情報を追加する必要があります。 最後に、元のアプリケーションの使用を終了して新しいアプリケーションに置き換える方法は、全体的なアップグレード戦略と密接に整合している必要があります。

#### <a name="custom-applications"></a>カスタム アプリケーション

Data Lake Storage Gen1 でアプリケーションが使用しているインターフェイスによっては、Data Lake Storage Gen2 に合わせて変更することが必要な場合があります。

##### <a name="rest-apis"></a>REST API

アプリケーションで Data Lake Storage REST API を使用している場合は、Data Lake Storage Gen2 の REST API を使用するようにアプリケーションを変更する必要があります。 リンクは "*プログラミング インターフェイス*" のセクションで提供されています。

##### <a name="sdks"></a>SDK

「*アップグレードの準備状態を評価する*」セクションで示されているように、SDK は現在使用できません。 アプリケーションを Data Lake Storage Gen2 に移植したい場合は、サポートされている SDK が使用できるようになるまで待つことをお勧めします。

##### <a name="powershell"></a>PowerShell

「アップグレードの準備状態を評価する」セクションで示されているように、現在、PowerShell のサポートはデータ プレーンでは使用できません。

管理プレーンのコマンドレットは、Data Lake Storage Gen2 での適切なものに置き換えることができます。 リンクは "*プログラミング インターフェイス*" のセクションで提供されています。

##### <a name="cli"></a>CLI

「*アップグレードの準備状態を評価する*」セクションで示されているように、現在、CLI のサポートはデータ プレーンでは使用できません。

管理プレーンのコマンドは、Data Lake Storage Gen2 での適切なものに置き換えることができます。 リンクは "*プログラミング インターフェイス*" のセクションで提供されています。

### <a name="analytics-frameworks-upgrade"></a>分析フレームワークのアップグレード

明示的なファイルやフォルダーのパスなど、ストア内の情報に関するメタデータをアプリケーションで作成する場合は、ストアのデータ/メタデータをアップグレードした後で、追加のアクションを実行する必要があります。 これは、Azure HDInsight や Databricks など、ストアのデータについてのカタログ データを通常作成する分析フレームワークに特に当てはまります。

分析フレームワークでは、Data Lake Storage Gen1 や Gen2 のようなリモート ストアに格納されているデータとメタデータが操作されます。 そのため、理論上は、エンジンは一時的でかまわず、格納されているデータに対してジョブを実行する必要がある場合にのみ起動できます。

しかし、パフォーマンスを最適化するため、分析フレームワークでは、リモート ストアに格納されているファイルとフォルダーへの明示的な参照が作成され、それを保持するキャッシュが作成される可能性があります。 以前は Data Lake Storage Gen1 にデータを格納していたクラスターで、現在は Data Lake Storage Gen2 に格納する場合などのように、リモート データの URI を変更する必要がある場合は、コピーされた同じコンテンツに対する URI が異なります。 そのため、データとメタデータをアップグレードした後は、これらのエンジンのキャッシュも更新または再初期化する必要があります

計画プロセスの一環として、アプリケーションを識別し、現在は Data Lake Storage Gen2 に格納されているデータをポイントするようにメタデータの情報を再初期化する方法を明らかにする必要があります。 一般的に採用されている分析フレームワークのアップグレード手順に役立つガイダンスを次に示します。

#### <a name="azure-databricks"></a>Azure Databricks

選択するアップグレード戦略により、手順は異なります。 このセクションでは、"リフトアンドシフト" 戦略を選択したものとします。 また、Data Lake Storage Gen1 アカウントのデータにアクセスしていた既存の Databricks が、Data Lake Storage Gen2 アカウントにコピーされたデータで動作することが期待されます。

最初に、Gen2 アカウントを作成し、適切なツールを使用して Gen1 から Gen2 にデータとメタデータをコピーしてあることを確認します。 これらのツールは、このガイドの「[データのアップグレード](#data-upgrade)」セクションで示されています。

次に、既存の Databricks クラスターを[アップグレード](https://docs.azuredatabricks.net/user-guide/clusters/index.html)し、Data Lake Storage Gen2 をサポートしている Databricks ランタイム 5.1 以降を使い始めます。

その後の手順は、既存の Databricks ワークスペースで Data Lake Storage Gen1 アカウント内のデータにアクセスしている方法によって異なります。 ノートブックから adl:// の URI を[直接](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly)呼び出すか、または[マウント ポイント](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)を介して呼び出すことにより行うことができます。

完全な adl:// URI を提供することでノートブックから直接アクセスしている場合は、各ノートブックを調べて、対応する Data Lake Storage Gen2 の URI にアクセスするように構成を変更する必要があります。

将来的には、Data Lake Storage Gen2 アカウントをポイントするように再構成する必要があります。 それ以外の変更は必要なく、ノートブックは以前と同じように動作できるはずです。

他のいずれかのアップグレード方法を使用している場合は、要件を満たすように上記の手順のバリエーションを作成できます。

### <a name="azure-ecosystem-upgrade"></a>Azure のエコシステムのアップグレード

このガイドの「[Azure のエコシステム](#azure-ecosystem)」セクションで示されている各ツールとサービスを、Data Lake Storage Gen2 で動作するように構成する必要があります。

最初に、Data Lake Storage Gen2 で使用可能な統合があることを確認します。

その後、上で示した要素 (たとえば、URI や資格情報) を変更する必要があります。 Data Lake Storage Gen1 で動作している既存のインスタンスを変更するのでも、Data Lake Storage Gen2 で動作する新しいインスタンスを作成するのでもかまいません。

### <a name="partner-ecosystem-upgrade"></a>パートナーのエコシステムのアップグレード

コンポーネントとツールを提供するパートナーと協力して、Data Lake Storage Gen2 で動作できるようにしてください。 

## <a name="performing-the-upgrade"></a>アップグレードの実行

### <a name="pre-upgrade"></a>アップグレード前

その一環として、このガイドの「*アップグレードの準備状態を評価する*」セクションおよび「[アップグレードの計画](#planning-for-an-upgrade)」セクションの手順を実施し、必要な情報をすべて入手し、ニーズを満たす計画を作成しておきます。 おそらく、このフェーズの間にテスト タスクを行います。

### <a name="in-upgrade"></a>アップグレード中

選択した戦略とソリューションの複雑さに応じて、このフェーズは、短くて済む場合も、Data Lake Storage Gen2 への段階的な移動を待機する複数のワークロードが存在する長いものになる場合もあります。 これはアップグレードの最も重要な部分です。

### <a name="post-upgrade"></a>アップグレード後

移行操作が完了した後の最後の手順には、完全な検証が含まれます。 これには、データが確実にコピーされたことの確認、ACL が正しく設定されたことの確認、エンドツーエンドのパイプラインが正しく機能していることの検証などが含まれます。検証が完了したら、古いパイプラインをオフにし、移行元の Data Lake Storage Gen1 アカウントを削除して、Data Lake Storage Gen2 ベースのソリューションに全力を注ぐことができます。

## <a name="conclusion"></a>まとめ

このドキュメントで提供したガイダンスは、Data Lake Storage Gen2 を使用するようにソリューションをアップグレードする際に役に立ったことと思います。 

さらに質問やフィードバックがある場合は、以下にコメントを入力するか、[Azure フィードバック フォーラム](https://feedback.azure.com/forums/327234-data-lake)でフィードバックを提供してください。
