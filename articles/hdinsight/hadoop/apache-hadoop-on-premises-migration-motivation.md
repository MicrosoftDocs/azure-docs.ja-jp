---
title: オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - 動機と利点
description: オンプレミスの Apache Hadoop クラスターを Azure HDInsight に移行することの動機と利点について説明します。
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: f886cb71d4dbc191dae317e8ea72d8c896f37c03
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442070"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - 動機と利点

この記事は、オンプレミスの Apache Hadoop エコシステム展開を Azure HDInsight に移行するためのベスト プラクティスに関する一連の記事の最初の記事です。 この一連の記事は、Azure HDInsight での Apache Hadoop ソリューションの設計、展開、および移行を担当する責任者を対象としています。 これらの記事が有益になる可能性がある役職として、クラウド アーキテクト、Hadoop 管理者、および DevOps エンジニアなどがあります。 さまざまな種類のクラスターがクラウド内でどのように機能するかについての説明は、ソフトウェア開発者、データ エンジニア、データ サイエンティストにとっても有益です。

## <a name="why-to-migrate-to-azure-hdinsight"></a>Azure HDInsight に移行する理由

Azure HDInsight は、Hadoop コンポーネントのクラウド ディストリビューションです。 Azure HDInsight によって大量のデータを簡単かつ迅速に高いコスト効率で処理できるようになります。 HDInsight には、好評を博している次のようなオープン ソース フレームワークが含まれています。

- Apache Hadoop
- Apache Spark
- Apache Hive with LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>オンプレミスの Hadoop に勝る Azure HDInsight の利点

- **低コスト** - [クラスターをオンデマンドで作成して](../hdinsight-hadoop-create-linux-clusters-adf.md)使用する分だけ料金を支払うことでコストを削減できます。 データの計算と記憶を切り離すことで、データ ボリュームがクラスター サイズに依存しなくなり、柔軟性が生まれます。

- **クラスターの自動作成** - クラスターの自動作成によって、セットアップと構成が最小限で済みます。 自動化はオンデマンド クラスターで使用できます。

- **ハードウェアと構成の管理** - HDInsight クラスターでは物理的なハードウェアやインフラストラクチャへの心配は不要です。 クラスターの構成さえ指定すれば、設定は Azure が行います。

- **簡単に拡張できる** - HDInsight ではワークロードの [スケール](../hdinsight-administer-use-portal-linux.md)を増減できます。 データの再配布およびワークロードの再均衡化処理は、データ処理ジョブを中断することなく Azure が行います。

- **グローバルに利用可能** - HDInsight は、他のあらゆるビッグ データ分析サービスよりも多くの[リージョン](https://azure.microsoft.com/regions/services/)で提供されています。 Azure HDInsight は、Azure Government、Azure China、Azure Germany でも提供されており、独自の法令が施行されている地域における企業のニーズに応えます。

- **セキュリティとコンプライアンス** - HDInsight を使用すると、 [Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)、 [暗号化](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)、 [Azure Active Directory](../domain-joined/hdinsight-security-overview.md) との統合によって、企業のデータ資産を保護することができます。 また HDInsight は、業界や行政上の最も一般的な [コンプライアンス基準](https://azure.microsoft.com/overview/trusted-cloud)を満たしています。

- **バージョン管理を簡略化** - Azure HDInsight は Hadoop エコシステム コンポーネントのバージョンを管理し、最新の状態に保持します。 ソフトウェアの更新は、オンプレミスの展開では一般的に複雑なプロセスです。

- **コンポーネント間の依存関係が少ない特定のワークロード用に最適化された小さいクラスター** - 典型的なオンプレミス Hadoop セットアップでは、多目的に使用できる 1 つのクラスターが使用されます。 Azure HDInsight では、ワークロード固有のクラスターを作成できます。 特定のワークロード用のクラスターを作成することによって、ますます複雑化する 1 つのクラスターを維持するための複雑な作業から解消されます。

- **生産性** - お好みの開発環境で Hadoop および Spark 向けのさまざまなツールを使用できます。

- **カスタム ツールまたはサードパーティ製のアプリケーションによる拡張機能** - HDInsight クラスターは、インストールされているコンポーネントを使用して拡張したり、Azure Marketplace から[ワンクリック](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) 展開を使用することによって他のビッグ データ ソリューションと統合したりできます。

- **管理と監視が容易に** - Azure HDInsight は  [Azure Monitor ログ](../hdinsight-hadoop-oms-log-analytics-tutorial.md) と統合することによって、すべてのクラスターを監視できる一元化されたインターフェイスが得られます。

- **他の Azure サービスとの統合** - HDInsight は、好評を博している以下の Azure サービスと簡単に統合できます。

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **プロセスとコンポーネントを自動修復** - HDInsight は常に独自の監視インフラストラクチャを使用して、インフラストラクチャとオープン ソース コンポーネントを継続的に確認しています。 また、オープン ソース コンポーネントやノードが利用できないなどの重大な障害も自動的に回復します。 いずれかの OSS コンポーネントに障害が発生した場合、Ambari でアラートがトリガーされます。

詳しくは、記事「[Azure HDInsight および Apache Hadoop テクノロジ スタックとは](../hadoop/apache-hadoop-introduction.md)」をご覧ください。

## <a name="migration-planning-process"></a>移行計画のプロセス

次の手順は、オンプレミス Hadoop クラスターから Azure HDInsight への移行計画で推奨されています。

1. 現在のオンプレミス展開およびトポロジを理解します。
2. 現在のプロジェクト スコープ、タイムライン、およびチームの専門知識について理解します。
3. Azure の要件を理解します。
4. ベスト プラクティスに基づく詳細な計画を構築します。

## <a name="gathering-details-to-prepare-for-a-migration"></a>移行の準備のために詳細情報を収集する

このセクションでは、次に関する重要な情報を収集するためのアンケートのテンプレートを提供します。

- オンプレミス展開
- プロジェクトの詳細
- Azure の要件

### <a name="on-premises-deployment-questionnaire"></a>オンプレミス展開のアンケート

| **質問** | **例** | **回答** |
|---|---|---|
|**トピック**:**Environment**|||
|クラスター ディストリビューションのバージョン|HDP 2.6.5、CDH 5.7|
|ビッグ データ エコシステム コンポーネント|HDFS、Yarn、Hive、LLAP、Impala、Kudu、HBase、Spark、MapReduce、Kafka、Zookeeper、Solr、Sqoop、Oozie、Ranger、Atlas、Falcon、Zeppelin、R|
|クラスターの種類|Hadoop、Spark、Confluent Kafka、Storm、Solr|
|クラスターの数|4|
|マスター ノードの数|2|
|ワーカー ノードの数|100|
|エッジ ノードの数| 5|
|ディスク領域の合計|100 TB|
|マスター ノード構成|m/y、cpu、ディスクなど|
|データ ノード構成|m/y、cpu、ディスクなど|
|エッジ ノード構成|m/y、cpu、ディスクなど|
|HDFS の暗号化?|はい|
|高可用性|HDFS HA、Metastore HA|
|ディザスター リカバリー/バックアップ|クラスターのバックアップ?|  
|クラスターに依存するシステム|SQL Server、Teradata、Power BI、MongoDB|
|サードパーティによる統合|Tableau、GridGain、Qubole、Informatica、Splunk|
|**トピック**:**セキュリティ**|||
|境界セキュリティ|ファイアウォール|
|クラスターの認証と承認|Active Directory、Ambari、Cloudera Manager、認証なし|
|HDFS アクセス制御|  手動、ssh ユーザー|
|Hive の認証と承認|Sentry、LDAP、AD と Kerberos、Ranger|
|監査|Ambari、Cloudera Navigator、Ranger|
|監視|Graphite、collectd、statsd、Telegraf、InfluxDB|
|アラート|Kapacitor、Prometheus、Datadog|
|データの保存期間| 3 年、5 年|
|クラスター管理者|単一の管理者、複数の管理者|

### <a name="project-details-questionnaire"></a>プロジェクトの詳細のアンケート

|**質問**|**例**|**回答**|
|---|---|---|
|**トピック**:**ワークロードと頻度**|||
|MapReduce のジョブ|1 日 2 回の 10 個のジョブ||
|Hive ジョブ|1 時間ごとに 100 個のジョブ||
|Spark バッチ ジョブ|15 分ごとに 50 個のジョブ||
|Spark Streaming ジョブ|3 分ごとに 5 個のジョブ||
|Structured Streaming ジョブ|1 分ごとに 5 個のジョブ||
|ML モデルのトレーニング ジョブ|1 週間に 1 回 2 個ジョブ||
|プログラミング言語|Python、Scala、Java||
|スクリプト|Shell、Python||
|**トピック**:**データ**|||
|データ ソース|フラット ファイル、Json、Kafka、RDBMS||
|データのオーケストレーション|Oozie ワークフロー、エアフロー||
|メモリ内の検索|Apache Ignite、Redis||
|データの保存先|HDFS、RDBMS、Kafka、MPP ||
|**トピック**:**メタデータ**|||
|Hive DB の種類|Mysql、Postgres||
|いいえ。 Hive メタストアの数|2||
|いいえ。 Hive テーブルの数|100||
|いいえ。 Ranger ポリシーの数|20||
|いいえ。 Oozie ワークフローの数|100||
|**トピック**:**スケール**|||
|レプリケーションを含むデータ ボリューム|100 TB||
|日次インジェストの量|50 GB||
|データ増加率|年 10%||
|クラスター ノード増加率|年 5%
|**トピック**:**クラスター使用率**|||
|平均 CPU 使用率|60%||
|平均メモリ使用率|75%||
|使用済みディスク領域|75%||
|平均ネットワーク 使用率|25%
|**トピック**:**スタッフ**|||
|いいえ。 管理者数|2||
|いいえ。 開発者数|10||
|いいえ。 エンド ユーザー数|100||
|スキル|Hadoop、Spark||
|いいえ。 移行作業に使用可能なリソース数|2||
|**トピック**:**制限事項**|||
|現時点での制限事項|待機時間が長い||
|現在の課題|コンカレンシーの問題||

### <a name="azure-requirements-questionnaire"></a>Azure の要件のアンケート

|**トピック**:**インフラストラクチャ** |||
|---|---|---|
|**質問**|**例**|**回答**|
| 優先リージョン|米国東部||
|VNet を推奨しますか?|はい||
|HA / DR が必要?|はい||
|他のクラウド サービスと統合しますか?|ADF、CosmosDB||
|**トピック**: **データの移動**  |||
|初期読み込みの基本設定|DistCp、データ ボックス、ADF WANDisco||
|データ転送の差分|DistCp、AzCopy||
|進行中の増分のデータ転送|DistCp、Sqoop||
|**トピック**: **監視とアラート** |||
|Azure 監視とアラートまたは統合サード パーティ監視の使用|Azure 監視とアラートの使用||
|**トピック**: **セキュリティ設定** |||
|プライベートな保護済みのデータ パイプラインですか?|はい||
|ドメイン参加済みクラスター (ESP) ですか?|     はい||
|クラウドへのオンプレミス AD Sync ですか?|     はい||
|いいえ。 同期する AD ユーザー数|          100||
|パスワードをクラウドに同期しますか?|    はい||
|クラウドのみのユーザー?|                 はい||
|MFA が必要ですか?|                       いいえ|| 
|データの承認要件ですか?|  はい||
|ロール ベースのアクセス制御?|        はい||
|監査が必要ですか?|                  はい||
|保存データの暗号化ですか?|          はい||
|転送中のデータの暗号化ですか?|       はい||
|**トピック**: **再アーキテクチャの設定** |||
|1 つのクラスターまたは特定のクラスターの種類|特定のクラスターの種類||
|併置されたストレージまたはリモート ストレージのどちらを指定するか?|リモート ストレージ||
|データをリモートに保管するときにクラスター サイズを小さくするか?|クラスター サイズを小さくする||
|単一の大型クラスターではなく複数の小さいクラスターを使用するか?|複数の小さいクラスターを使用する||
|リモート メタストアを使用するか?|はい||
|異なるクラスター間でメタストアを共有するか?|はい||
|ワークロードを分解するか?|Hive ジョブを Spark ジョブに置き換える||
|データのオーケストレーションに ADF を使用するか?|いいえ||

## <a name="next-steps"></a>次の手順

このシリーズの次の記事をお読みください。

- [オンプレミスから Azure HDInsight Hadoop への移行のためのアーキテクチャのベスト プラクティス](apache-hadoop-on-premises-migration-best-practices-architecture.md)
