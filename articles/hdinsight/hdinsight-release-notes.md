---
title: "Azure HDInsight の Hadoop コンポーネントのリリース ノート | Microsoft Docs"
description: "Azure HDInsight の Hadoop コンポーネントの最新のリリース ノートとバージョン。 Spark、R Server、Hive などの開発に関するヒントや詳細を紹介します。"
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 96b2b4976729da5b7d8b75909dbe099090240c08
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Azure HDInsight の Hadoop コンポーネントのリリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン管理に関する記事](hdinsight-component-versioning.md)に関するページを参照してください。


## <a name="notes-for-08012017-release-of-hdinsight"></a>HDInsight の 2017 年 8 月 1 日リリース

| タイトル | Description | 影響を受ける領域  | クラスターの種類  | 
| --- | --- | --- | --- | --- |
| HDInsight での Microsoft R Server 9.1 の リリース |HDInsight では、HDInsight での Microsoft R Server 9.1 クラスターのプロビジョニングをサポートするようになりました。 Microsoft R Server 9.1 リリースの詳細については、[このブログ](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/)をご覧ください。 |サービス |R Server |
| HDInsight 3.6 に Hadoop スタックの新バージョンを搭載|<ul><li>更新されたバージョンの詳細な一覧については、[HDInsight で使用可能な Hadoop コンポーネント バージョン](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions)に関するページをご覧ください。</li><li>Hadoop スタックの最新バージョンで修正されたバグの一覧については、「[Apache Patch Information (Apache パッチ情報)](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html)」をご覧ください。</li><li>HDP 2.6.1 (HDInsight 3.6 で使用可能になりました) との間の重大な変更の一覧については、[https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html) をご覧ください。</li><li>HDP 2.6.1 の既知の問題の一覧については、「[Known issues (既知の問題)](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html)」をご覧ください。</li></ul> |サービス |すべて |該当なし |
| インタラクティブ Hive (プレビュー) クラスターの更新 |<ul><li><b>機能改善。</b> メタデータをキャッシュすることによってバックエンド SQL への負荷を削減し、すべてのメタデータ操作のパフォーマンスを向上させるキャッシュ metastore を実装。  この改善は、すべてのインタラクティブ Hive クラスターに適用されています。 詳細については、[https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520) をご覧ください。</li><li><b>機能改善。</b> パーティションの動的読み込みを最適化。 詳細については、[https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204) をご覧ください。</li><li><b>機能改善。</b> Linux 上の HDInsight の構成を最適化。</li><li><b>バグ修正。</b> `CredentialProviderFactory$getProviders` がスレッドセーフではない。 この問題は解決されました。 詳細については、[https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195) をご覧ください。</li><li><b>バグ修正。</b> WASB ドライバーの `liststatus` API の CPU 使用率が高いため、ATS のパフォーマンスが悪化する。 この問題は解決されました。 詳細については、[https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154) をご覧ください。</li></ul> |サービス |対話型 Hive (プレビュー) |
| Hadoop クラスターの更新 |Templeton ジョブ操作の信頼性が向上。 詳細については、[https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) をご覧ください。 |サービス |Hadoop |
| YARN の更新 | HDInsight で (コストを増やすことなく) 250 GB の Ambari データベースが作成されるようになり、カスタマー エクスペリエンスが向上。 この変更により、ATS がいっぱいになる状態が回避されるため、パフォーマンスの改善が見込めます。 |サービス |すべて |
| Spark の更新 | Spark 2.1.1 のリリース。 詳細については、「[Spark Release 2.1.1 (Spark リリース 2.1.1)](https://spark.apache.org/releases/spark-release-2-1-1.html)」をご覧ください。 | サービス | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>2017 年 4 月 6 日 - HDInsight 3.6 の一般提供

* このリリースでは、Azure HDInsight に、HDP 2.6 に基づくバージョン 3.6 が追加されます。 HDP 2.6 リリース ノートについては、[こちら](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html)を参照してください。また、HDInsight バージョンの詳細については、[こちら](hdinsight-component-versioning.md)を参照してください。 HDInsight 3.6 は、次のワークロードに使用できます。

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Hive View 2.0 のサポート**。 これによって、Interactive Hive のユーザー エクスペリエンスが向上します。 詳細については、[Hortonworks のドキュメント](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html)を参照してください。

* **Hive LLAP のパフォーマンス強化**。 詳細については、[Hortonworks のドキュメント](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/)を参照してください。

* **Hive の新機能**。 [Hortonworks のドキュメント](https://hortonworks.com/apache/hive/#section_4)をご覧ください。

* **Hive CLI の廃止**: Hive CLI は廃止される予定です。お客様には代わりに Beeline を使用することをお勧めします。 詳細については、[Apache のドキュメント](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline)を参照してください。 HDInsight で Beeline を使用する方法については、[「HDInsight Hadoop クラスターで Beeline を使用する」](hdinsight-hadoop-use-hive-beeline.md)を参照してください。

* **Apache Phoenix および HBase の新機能**。
    * ストレージ クォータのサポート: マルチ テナント環境でよく使用され、テーブルごとと名前空間レベルごとに限られたストレージ スペースを許可します。
    * Phoenix のインデックス作成機能の向上: インクリメンタル インデックス作成と以前の障害からのインデックス作成の再構築/再開。
    * Phoenix データ整合性ツール: スキーマ、インデックス、およびその他のメタデータの検証をサポートします。


* **HBase での問題**: CSV 一括アップロード MapReduce ジョブの実行時、次の構文がエラーになることがあります。

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    代わりに、次の構文を使用してください。

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>2017 年 2 月 28 日 - HDInsight 3.6 プレビューでの Spark 2.1 のリリース ノート
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) では、以前のバージョンにあった安定性と使いやすさに関するさまざまな問題が改善されます。 Spark Core、SQL、ML、ストリーミングなど、すべての Spark ワークロードに及ぶ新機能も導入します。
* 構造化ストリーミングでは、イベント時刻の透かしと Kafka 0.10 コネクタのサポートによってスケーラビリティが向上しています。
* Spark SQL のパーティション分割は、新しいスケーラブルなパーティション処理メカニズムを使用して処理されるようになりました。 アップグレードする方法の詳細については、[ここ](http://spark.apache.org/releases/spark-release-2-1-0.html)を参照してください。
* Azure HDInsight 3.6 プレビュー上の Spark 2.1 は、現在 ODBC ドライバーを使用した BI ツールの接続をサポートしていません。
* このプレビューでは、Spark 2.1 クラスターから Azure Data Lake Store へのアクセスはサポートされていません。


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>2016 年 11 月 18 日 - HDInsight 3.5 での Spark 2.0.1 の リリース
Spark 2.0.1 は、現在 Spark クラスター (HDInsight バージョン 3.5) で公開されています。

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>2016 年 11 月 16 日 - HDinsight 3.5 (Spark 2.0) での R Server 9.0 の リリース
*   R Server クラスターのバージョン オプションに、HDI 3.5 (Spark 2.0) の R Server 9.0 と HDI 3.4 (Spark 1.6) の R Server 8.0 の 2 つが追加されました。
*   HDI 3.5 (Spark 2.0) の R Server 9.0 は R 3.3.2 上に構築され、Hive および Parquet から直接 Spark DataFrames にデータを読み込み ScaleR で分析するための、RxHiveData と RxParquetData という新しい ScaleR データ ソース関数が追加されています。 詳細については、R の **?RxHiveData** コマンドおよび **?RxParquetData** コマンドを使用して表示されるこれらの関数のインライン ヘルプを参照してください。
*   RStudio コミュニティ エディションが、プロビジョニング フローの一環として、[クラスターの構成] ブレードにおいて既定で (オプトアウト オプションで) インストールされるようになりました。

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>2016 年 11 月 9 日 - HDInsight での Spark 2.0 の リリース
* HDInsight 3.5 の Spark 2.0 クラスターで、Livy サービスおよびJupyter サービスがサポートされるようになりました。

## <a name="10262016---release-of-r-server-on-hdinsight"></a>2016 年 10 月 26 日 - HDInsight での R Server のリリース
* エッジ ノードへのアクセス用の URI が、**clustername**-ed-ssh.azurehdinsight.net に変更されました。
* HDInsight クラスターでの R Server のプロビジョニングの効率が向上しています。
* HDInsight の R Server が、標準の HDInsight "R Server" クラスター タイプとして使用可能になりました。今後は別の HDInsight アプリケーションとしてインストールする必要はありません。 エッジ ノードと R Server バイナリは、R Server クラスター デプロイメントの一部としてプロビジョニングされるようになりました。 これにより、プロビジョニングの速度と信頼性が向上します。 これに伴い、R Server の価格モデルも更新されます。
* R Server クラスター タイプの価格は、Standard Tier の価格に R Server の追加料金を加えたものになります。 Premium Tier は、複数のクラスター タイプで使用可能な Premium 機能用になり、R Server クラスター タイプでは使用されなくなります。 この変更は、実際の R Server の価格には影響しません。請求書の記載方法が変わるだけです。 既存のすべての R Server クラスターは引き続き実行でき、Resource Manager テンプレートは非推奨に関する通知が行われるまで使用できます。 **ただし、スクリプト化したデプロイメントが新しい Resource Manager テンプレートを使用するように更新することをお勧めします。**







