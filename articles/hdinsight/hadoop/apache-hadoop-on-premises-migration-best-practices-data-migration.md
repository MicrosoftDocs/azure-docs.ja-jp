---
title: データの移行:オンプレミスの Apache Hadoop から Azure HDInsight
description: オンプレミスの Apache Hadoop クラスターを Azure HDInsight に移行することについてのデータ移行のベスト プラクティスについて説明します。
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cdb028bcd608aa7a19a7c83e5e2c1129386928a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939796"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - データ移行のベスト プラクティス

この記事では、Azure HDInsight へのデータ移行に関する推奨事項について説明します。 これは、オンプレミスの Apache Hadoop システムを Azure HDInsight に移行する際に役立つベスト プラクティスを紹介するシリーズの一部です。

## <a name="migrate-on-premises-data-to-azure"></a>オンプレミスのデータを Azure に移行する

データをオンプレミスから Azure 環境に移行するには、主に次の 2 つのオプションがあります。

* TLS を使用してネットワーク経由でデータを転送する
    * インターネット経由 - Azure Storage Explorer、AzCopy、Azure Powershell、Azure CLI などの複数のツールのいずれかを使用して、通常のインターネット接続経由でデータを Azure ストレージに転送できます。 詳細については、「[Azure Storage との間でのデータの移動](../../storage/common/storage-choose-data-transfer-solution.md)」をご覧ください。

    * ExpressRoute - ExpressRoute は、Microsoft のデータセンターとオンプレミスや共用施設にあるインフラストラクチャの間にプライベート接続を作成できる Azure サービスです。 ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 詳しくは、「[ExpressRoute 回線の作成と変更](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)」をご覧ください。

    * Data Box オンライン データ転送 - Data Box Edge と Data Box Gateway は、ネットワーク ストレージのゲートウェイとして機能して自社サイトと Azure の間でデータを管理するオンライン データ転送製品です。 Data Box Edge は、Azure との間でデータを転送する機能と、人工知能 (AI) に対応したエッジ計算によってデータを処理する機能とを備えたオンプレミスのネットワーク デバイスです。 Data Box Gateway は、ストレージ ゲートウェイ機能を備えた仮想アプライアンスです。 詳しくは、「[Azure Data Box のドキュメント - オンライン転送](../../databox-online/index.yml)」をご覧ください。

* オフラインでのデータの発送

    Data Box オフライン データ転送 - ネットワークを利用できない場合は、Data Box、Data Box Disk、Data Box Heavy の各デバイスを使えば、大量のデータを Azure に転送するのに役立ちます。 これらのオフライン データ転送デバイスは、貴社と Azure データセンターとの間で運送業者を介してやり取りされます。 転送中のデータは AES 暗号化を使って保護され、また、アップロード後はサニタイズ処理が適用され、データがデバイスから削除されます。 Data Box オフライン転送デバイスの詳細については、「[Azure Data Box のドキュメント - オフライン転送](../../databox/index.yml)」を参照してください。 Hadoop クラスターの移行の詳細については、[Azure Data Box を使用したオンプレミス HDFS ストアから Azure Storage への移行](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)に関するページを参照してください。

次の表に、データ ボリュームとネットワーク帯域幅に基づくおおまかなデータ転送時間を示します。 データ移行に 3 週間以上かかることが予想される場合は Data Box を使用してください。

|データ量 | ネットワーク帯域幅<br>of<br>**45 Mbps (T3)**|ネットワーク帯域幅<br>of<br>**100 Mbps**|ネットワーク帯域幅<br>of<br>**1 Gbps**|ネットワーク帯域幅<br>of<br>**10 Gbps**|
|---|:---:|:---:|:---:|:---:|
|1 TB (テラバイト)|2 日|1 日| 2 時間|14 分|
|10 TB|22 日|10 日|1 日|2 時間|
|35 TB|76 日|34 日|3 日|8 時間|
|80 TB|173 日|78 日|8 日|19 時間|
|100 TB|216 日|97 日|10 日|1 日|
|200 TB|1 年|194 日|19 日|2 日|
|500 TB|3 年|1 年|49 日|5 日|
|1 PB|6 年|3 年|97 日|10 日|
|2 PB|12 年|5 年|194 日|19 日|

Apache Hadoop DistCp、Azure Data Factory、AzureCp などの Azure にネイティブなツールを使用して、ネットワーク経由でデータを転送できます。 同じ目的で、サード パーティ製ツール WANDisco も使用できます。 オンプレミスから Azure ストレージ システムへの継続的なデータ転送には、Apache Kafka Mirrormaker および Apache Sqoop を使用できます。

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Apache Hadoop DistCp を使用する場合のパフォーマンスに関する考慮事項

DistCp は、 MapReduce マップ ジョブを使用して、データ転送、エラーの処理、およびそれらのエラー回復を行う Apache プロジェクトです。 ソース ファイルの一覧が、各マップ タスクに割り当てられます。 マップ タスクは、割り当て済みのすべてのファイルを宛先にコピーします。 いくつかの手法によって、DistCp のパフォーマンスを向上させることができます。

### <a name="increase-the-number-of-mappers"></a>マッパーの数を増やす

DistCp では、それぞれがほぼ同じバイト数をコピーするようにするために、マップ タスクを作成しようとしています。 既定では、DistCp ジョブは 20 個のマッパーを使用します。 Distcp に対して使用するマッパーを増やす (コマンド ラインで「m」パラメーターを指定する) と データ転送プロセス中の並列度が増加し、データ転送時間が減少します。 ただし、Mapper の数を増やすときに次の 2 つのことを考慮する必要があります。

* DistCp の最小粒度は 1 つのファイルです。 マッパーの数をソース ファイルの数より多く指定しても効果はなく、使用可能なクラスター リソースが浪費されます。

* マッパーの数を決定する場合はクラスターで使用可能な Yarn メモリを検討してください。 各マップ タスクは Yarn コンテナーとして起動されます。 負荷の大きい別のワークロードがクラスター上で実行中でない場合、マッパー数は m = (ワーカー ノードの数 \* worker ノードごとの YARN メモリ) / YARN コンテナーサイズという式で決定できます。 ただし、他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp ジョブに対する部分のみを使用するように選択できます。

### <a name="use-more-than-one-distcp-job"></a>複数の DistCp ジョブを使用する

移動するデータセットのサイズが 1 TB より大きい場合は、複数の DistCp ジョブを使用します。 複数のジョブを使用すると、障害の影響が制限されます。 いずれかのジョブが失敗した場合、すべてのジョブではなく、その特定のジョブのみ再起動する必要があります。

### <a name="consider-splitting-files"></a>ファイルの分割を検討する

大きいファイルが少ししかない場合、マッパーを増やして潜在的な並列性の機能を高めるために、ファイルを 256 MB のファイル チャンクに分割することを検討してください。

### <a name="use-the-strategy-command-line-parameter"></a>'strategy' コマンドライン パラメーターを使用する

コマンド ラインで `strategy = dynamic` パラメーターを使用することを検討します。 `strategy` パラメーターの既定値は `uniform size` で、この場合、各マップはほぼ同じバイト数だけコピーします。 このパラメーターが `dynamic` に変更されると、リスティング ファイルはいくつかの「チャンク ファイル」に分割されます。 チャンク ファイルの数はマップの数の倍数です。 各マップ タスクには、いずれかのチャンク ファイルが割り当てられます。 チャンク内のすべてのパスが処理された後、現在のチャンクが削除され、新しいチャンクが取得されます。 この処理はチャンクがなくなるまで続きます。 この「動的」アプローチでは、高速なマップタスクが低速のものより多くのパスを使用できるため、DistCp ジョブが全体として高速化されます。

### <a name="increase-the-number-of-threads"></a>スレッドの数を増やす

`-numListstatusThreads` パラメーターを増やすとパフォーマンスが向上するかどうかを確認してみてください。 このパラメーターは、ファイルの一覧を構築するために使用するスレッドの数を制御し、最大値は 40 です。

### <a name="use-the-output-committer-algorithm"></a>出力コミッター アルゴリズムを使用する

`-Dmapreduce.fileoutputcommitter.algorithm.version=2` パラメーターを渡すと DistCp のパフォーマンスが向上するかどうかを確認してみてください。 この出力コミッター アルゴリズムは、保存先への出力ファイルの書き込みを最適化します。 次のコマンドは、さまざまなパラメーターの使用状況を示す例です。

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>メタデータの移行

### <a name="apache-hive"></a>Apache Hive

Hive metastore はスクリプトを使用するか、DB のレプリケーションを使用して移行できます。

#### <a name="hive-metastore-migration-using-scripts"></a>スクリプトを使用した Hive metastore の移行

1. オンプレミス Hive metastore から Hive DDL を生成します。 この手順は、[ラッパー bash スクリプト](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)を使用して実行できます。
1. 生成された DDL を編集して、HDFS URL を WASB/ADLS/ABFS URL に置き換えます。
1. メタストア上の更新された DDL を HDInsight クラスターから実行します。
1. Hive metastore バージョンがオンプレミスとクラウドの間で互換性があることを確認します。

#### <a name="hive-metastore-migration-using-db-replication"></a>DB レプリケーションを使用した Hive metastore の移行

- オンプレミスの Hive metastore DB と HDInsight metastore DB の間でデータベース レプリケーションを設定します。
- 「Hive MetaTool」を使用して、HDFS の url を WASB/ABFS/WASB または ADLS の url で置き換えます。たとえば次のようにします

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- オンプレミスの Ranger ポリシーを xml ファイルにエクスポートします。
- XSLT などのツールを使用して、オンプレミスの特定の HDFS ベースのパスを特定の WASB または ADLS に変換します。
- HDInsight で実行されている Ranger にポリシーをインポートします。

## <a name="next-steps"></a>次のステップ

このシリーズの次の記事をお読みください。

- [オンプレミスから Azure HDInsight Hadoop への移行のためのセキュリティおよび DevOps のベスト プラクティス](apache-hadoop-on-premises-migration-best-practices-security-devops.md)