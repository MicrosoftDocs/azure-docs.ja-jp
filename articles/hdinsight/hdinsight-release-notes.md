---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 1c8bdd0ca715b6dfd00dc9876a074c61711990af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67117822"
---
# <a name="release-notes-for-azure-hdinsight"></a>Azure HDInsight のリリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン管理に関する記事](hdinsight-component-versioning.md)に関するページを参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープンソースの Apache Hadoop および Apache Spark の分析を行う、エンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="new-features"></a>新機能

HDInsight 4.0 での重要な変更の詳細については、「[HDI 4.0 の新機能](../hdinsight/hdinsight-version-release.md)」を参照してください。

## <a name="component-versions"></a>コンポーネントのバージョン

以下に HDInsight 4.0 のすべてのコンポーネントの正式な Apache バージョンを示します。 ここに列挙されているコンポーネントは、利用できる最新の安定したバージョンのリリースです。

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.2
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

新しいバージョンの Apache のコンポーネントは、上の一覧のバージョンに加えて HDP ディストリビューションにバンドルされていることがあります。 この場合、これらの新しいバージョンの一覧はテクニカル プレビューの表に表示され、運用環境で上の一覧の Apache コンポーネントのバージョンの代替にはなりません。

## <a name="apache-patch-information"></a>Apache のパッチ情報

HDInsight 4.0 で使用可能なパッチの詳細については、以下の表の各製品のパッチ一覧を参照してください。

| 製品名 | パッチ情報 |
|---|---|
| Ambari | [Ambari のパッチ情報](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| hbase | [HBase のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | このリリースは Hive 3.1.0 を提供します。Apache の追加パッチはありません。  |
| Kafka | このリリースは Kafka 1.1.1 を提供します。Apache の追加パッチはありません。 |
| Oozie | [Oozie のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | このリリースは Sqoop 1.4.7 を提供します。Apache の追加パッチはありません。 |
| Tez | このリリースは Tez 0.9.1 を提供します。Apache の追加パッチはありません。 |
| Zeppelin | このリリースは Zeppelin 0.8.0 を提供します。Apache の追加パッチはありません。 |
| Zookeeper | [Zookeeper のパッチ情報](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>修正された共通脆弱性識別子

このリリースで解決されたセキュリティの問題の詳細については、Hortonworks の「[Fixed Common Vulnerabilities and Exposures for HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html)」を参照してください。

## <a name="known-issues"></a>既知の問題

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>既定のインストールで Secure HBase のレプリケーションが破損する

HDInsight 4.0 の場合は、次の手順を実行します。

1. クラスター間通信を有効にします。
1. アクティブなヘッドノードにサインインします。
1. 次のコマンドを使用して、スクリプトをダウンロードしてレプリケーションを有効にします。

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. コマンド `sudo kinit <domainuser>` を入力します。
1. 次のコマンドを入力して、スクリプトを実行します。

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
HDInsight 3.6 の場合は、次の手順を実行します。

1. アクティブな HMaster ZK にサインインします。
1. 次のコマンドを使用して、スクリプトをダウンロードしてレプリケーションを有効にします。
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. コマンド `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>` を入力します。
1. 次のコマンドを入力します。

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>HBase クラスターを HDInsight 4.0 に移行した後に Phoenix Sqlline の動作が停止する

次の手順を実行します。

1. 次の Phoenix テーブルを削除します。
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. テーブルを削除できない場合は、HBase を再起動してテーブルへの接続をすべて解除してください。
1. `sqlline.py` をもう一度実行します。 手順 1 で削除されたすべてのテーブルが Phoenix によって再作成されます。
1. HBase データ用の Phoenix テーブルとビューを再生成します。

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>HBase Phoenix のメタデータを HDInsight 3.6 から 4.0 にレプリケートした後に Phoenix Sqlline の動作が停止する

次の手順を実行します。

1. レプリケーションを行う前に、宛先の 4.0 クラスターに移動して `sqlline.py` を実行します。 このコマンドによって、4.0 にのみ存在する `SYSTEM.MUTEX` や `SYSTEM.LOG` などのPhoenix テーブルが生成されます。
1. 次のテーブルを削除します。
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. HBase レプリケーションの起動

## <a name="deprecation"></a>非推奨

Apache Storm と ML サービスは、HDInsight 4.0 では使用できません。
