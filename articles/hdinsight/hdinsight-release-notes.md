---
title: "Azure HDInsight の Hadoop コンポーネントのリリース ノート | Microsoft Docs"
description: "Azure HDInsight の Hadoop コンポーネントの最新のリリース ノートとバージョン。 Hadoop、Apache Storm、HBase に関する開発のヒントと詳細情報を示します。"
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/28/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 7a9e23e8d06bc73855058242cdebd315c9d4d243
ms.lasthandoff: 03/21/2017


---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Azure HDInsight の Hadoop コンポーネントのリリース ノート

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン管理に関する記事](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関するページを参照してください。

##<a name="notes-for-02282017-release-of-spark-21-on-hdinsight-36-preview"></a>HDInsight 3.6 プレビューでの Spark 2.1 の 2017 年 2 月 28 日リリース ノート
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) では、以前のバージョンにあった安定性と使いやすさに関するさまざまな問題が改善されます。 Spark Core、SQL、ML、ストリーミングなど、すべての Spark ワークロードに及ぶ新機能も導入します。
* 構造化ストリーミングでは、イベント時刻の透かしと Kafka 0.10 コネクタのサポートによってスケーラビリティが向上しています。
* Spark SQL のパーティション分割は、新しいスケーラブルなパーティション処理メカニズムを使用して処理されるようになりました。 アップグレードする方法の詳細については、[ここ](http://spark.apache.org/releases/spark-release-2-1-0.html)を参照してください。
* Azure HDInsight 3.6 プレビュー上の Spark 2.1 は、現在 ODBC ドライバーを使用した BI ツールの接続をサポートしていません。
* このプレビューでは、Spark 2.1 クラスターから Azure Data Lake Store へのアクセスはサポートされていません。


##<a name="notes-for-11182016-release-of-spark-201-on-hdinsight-35"></a>HDInsight 3.5 の Spark 2.0.1 の 2016 年 11 月 18 日リリース
Spark 2.0.1 は、現在 Spark クラスター (HDInsight バージョン 3.5) で公開されています。

## <a name="notes-for-11162016-release-of-r-server-90-on-hdinsight-35-spark-20"></a>HDinsight 3.5 (Spark 2.0) の R Server 9.0 の 2016 年 11 月 16 日リリース
*    R Server クラスターのバージョン オプションに、HDI 3.5 (Spark 2.0) の R Server 9.0 と HDI 3.4 (Spark 1.6) の R Server 8.0 の 2 つが追加されました。
*    HDI 3.5 (Spark 2.0) の R Server 9.0 は R 3.3.2 上に構築され、Hive および Parquet から直接 Spark DataFrames にデータを読み込み ScaleR で分析するための、RxHiveData と RxParquetData という新しい ScaleR データ ソース関数が追加されています。 詳細については、R の ?RxHiveData コマンドおよび ?RxParquetData コマンドで表示されるこれらの関数のインライン ヘルプを参照してください。
*    RStudio コミュニティ エディションが、プロビジョニング フローの一環として、[クラスターの構成] ブレードにおいて既定で (オプトアウト オプションで) インストールされるようになりました。

## <a name="notes-for-11092016-release-of-spark-20-on-hdinsight"></a>HDInsight の Spark 2.0 の 2016 年 11 月 9 日リリース
* HDInsight 3.5 の Spark 2.0 クラスターで、Livy サービスおよびJupyter サービスがサポートされるようになりました。

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>HDinsight の R Server の 2016 年 10 月 26 日リリース
* エッジ ノードへのアクセス用の URI が、**clustername**-ed-ssh.azurehdinsight.net に変更されました。
* HDInsight クラスターでの R Server のプロビジョニングの効率が向上しています。
* HDInsight の R Server が、標準の HDInsight "R Server" クラスター タイプとして使用可能になりました。今後は別の HDInsight アプリケーションとしてインストールする必要はありません。 エッジ ノードと R Server バイナリは、R Server クラスター デプロイメントの一部としてプロビジョニングされるようになりました。 これにより、プロビジョニングの速度と信頼性が向上します。 これに伴い、R Server の価格モデルも更新されます。
* R Server クラスター タイプの価格は、Standard Tier の価格に R Server の追加料金を加えたものになります。 Premium Tier は、今後は複数のクラスター タイプで使用可能な Premium 機能用になり、R Server クラスター タイプでは使用されなくなります。 この変更は、実際の R Server の価格には影響しません。請求書の記載方法が変わるだけです。 既存のすべての R Server クラスターは引き続き実行でき、ARM テンプレートは非推奨に関する通知が行われるまで使用できます。 **ただし、スクリプト化したデプロイメントが新しい ARM テンプレートを使用するように更新することをお勧めします。**

## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>HDinsight の R Server の 2016 年 08 月 30 日リリース
今回のリリースがデプロイされている Linux ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド | Ambari ビルド |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

今回のリリースがデプロイされている Windows ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド |
| --- | --- | --- | --- |
| 2.1 |2.1.10.1033.2559206 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>HDinsight の R Server の 2016 年 08 月 17 日リリース
* R Server 8.0.5 - 主にバグの修正プログラムのリリースです。 詳細については、 [R Server リリース ノート](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) を参照してください。
* エッジ ノードでの AzureML パッケージ – [この R パッケージ](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)により、R モデルが Azure ML Web サービスとして発行され、使用されます。  詳細については、「[HDInsight の R Server の概要](hdinsight-hadoop-r-server-overview.md)」の記事の「[モデルの運用化](hdinsight-hadoop-r-server-overview.md#operationalize-a-model)」のセクションを参照してください。
* [上位 100 の最も一般的な R パッケージ](https://github.com/metacran/cranlogs) の Linux の依存関係 – これらの Linux パッケージの依存関係は、あらかじめインストールされています。
* オプション R をデータ ノードに追加するときに、CRAN リポジトリを使用するオプションです。 詳しくは、「[HDInsight での R Server の概要](hdinsight-hadoop-r-server-get-started.md)」をご覧ください。
* クラスターが作成されるときの、R Server のプロビジョニングの信頼性が向上しました。

## <a name="notes-for-08012016-release-of-hdinsight"></a>HDInsight の 2016 年 8 月 1 日リリース ノート
今回のリリースがデプロイされている Linux ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド | Ambari ビルド |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8028416 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8028416 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8053402 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

今回のリリースがデプロイされている Windows ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド |
| --- | --- | --- | --- |
| 2.1 |2.1.10.1005.2488842 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1005.2488842 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1005.2488842 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1005.2488842 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1005.2488842 |2.3 |2.3.3.1-25 |

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Spark、Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| HDInsight 3.4 クラスターに対する変更 |パフォーマンスを向上させるため、次の hive 構成の既定値が変更されています。 <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul> |サービス |すべて |該当なし |
| このリリースに含まれる修正プログラム |HIVE-13632、HIVE-12897、HIVE-12907、HIVE-12908、HIVE-12988、HIVE-13510、HIVE-13572、HIVE-13716、HIVE-13726、HIVE-12505、HIVE-13632、HIVE-13661、HIVE-13705、HIVE-13743、HIVE-13810、HIVE-13857、HIVE-13902、HIVE-13911、HIVE-13933 |サービス |すべて |該当なし |

## <a name="notes-for-07142016-release-of-hdinsight"></a>HDInsight の 2016 年 7 月 14 日リリース ノート
今回のリリースがデプロイされている Linux ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド | Ambari ビルド |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.7932505 |2.2 |2.2.9.1-11 |2.2.1.12-2 |
| 3.3 |3.3.1000.0.7932505 |2.3 |2.3.3.1-18 |2.2.1.12-2 |
| 3.4 |3.4.1000.0.7933003 |2.4 |2.4.2.0 |2.2.1.12-2 |

今回のリリースがデプロイされている Windows ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド |
| --- | --- | --- | --- |
| 2.1 |2.1.10.989.2441725 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.989.2441725 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.989.2441725 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.989.2441725 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.989.2441725 |2.3 |2.3.3.1-21 |

## <a name="notes-for-07072016-release-of-hdinsight"></a>HDInsight の 2016 年 7 月 7 日リリース ノート
今回のリリースがデプロイされている Linux ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド |
| --- | --- | --- | --- |
| 3.2 |3.2.1000.0.7864996 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.1000.0.7864996 |2.3 |2.3.3.1-18 |
| 3.4 |3.4.1000.0.7861906 |2.4 |2.4.2.0 |

今回のリリースがデプロイされている Windows ベースの HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDI | HDI クラスター バージョン | HDP | HDP ビルド |
| --- | --- | --- | --- |
| 2.1 |2.1.10.977.2413853 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.977.2413853 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.977.2413853 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.977.2413853 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.977.2413853 |2.3 |2.3.3.1-21 |

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Spark、Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| [HDInsight Tools for IntelliJ (IntelliJ 用 HDInsight ツール)](hdinsight-apache-spark-intellij-tool-plugin.md) |HDInsight Spark クラスター用の IntelliJ IDEA プラグインが、IntelliJ の Azure Toolkit と統合されました。 Azure SDK v2.9.1、最新の Java SDK をサポートし、IntelliJ のスタンドアロンの HDInsight プラグインのすべての機能が含まれています。 |ツール |Spark |該当なし |
| [HDInsight Tools for Eclipse (Eclipse 用 HDInsight ツール)](hdinsight-apache-spark-eclipse-tool-plugin.md) |Azure Toolkit for Eclipse が、HDInsight Spark クラスターをサポートするようになりました。 次の機能が有効になります。 <ul><li>IntelliSense、自動書式設定、エラー チェックなど、優れたオーサリング サポートを使用して、Spark アプリケーションを Scala および Java で作成し記述します。</li><li>ローカルで Spark アプリケーションをテストします。</li><li>HDInsight Spark クラスターにジョブを送信し、結果を取得します。</li><li>Azure にログインし、Azure サブスクリプションに関連付けられているすべての Spark クラスターにアクセスします。</li><li>HDInsight Spark クラスターに関連付けられたすべてのストレージ リソースをナビゲートします。</li></ul> |ツール |Spark |該当なし |

今回のリリースから、Linux ベースの HDInsight クラスターに対するゲスト OS 更新プログラムの適用ポリシーが変更されました。 新しいポリシーの目的は、修正プログラム適用のための再起動の回数を大幅に削減することです。 新しいポリシーでは、Linux クラスターの仮想マシン (VM) への修正プログラム適用が継続されます。修正プログラム適用は、指定されたクラスターのノード間で交互に、毎週月曜日または木曜日の午前 12 時 (UTC) に開始されます。 ただし、どの VM も、ゲスト OS の修正プログラム適用のための再起動は 30 日ごとに最大で 1 回のみです。 また、新しく作成したクラスターの最初の再起動は、クラスターの作成日から 30 日以内には行われません。

> [!NOTE]
> これらの変更は、このリリース バージョン以上の新しく作成したクラスターにのみ適用されます。
>
>

## <a name="notes-for-06062016-release-of-hdinsight"></a>HDInsight の 2016 年 6 月 6 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

| HDP | HDI バージョン | Spark バージョン | Ambari のビルド番号 | HDP のビルド番号 |
| --- | --- | --- | --- | --- |
| 2.3 |3.3.1000.0.7702215 |1.5.2 |2.2.1.8-2 |2.3.3.1-18 |
| 2.4 |3.4.1000.0.7702224 |1.6.1 |2.2.1.8-2 |2.4.2.0 |

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Spark、Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| Spark on HDInsight is generally available (HDInsight の Spark が一般公開) |このリリースでは、HDInsight でのオープン ソース Apache Spark の可用性、スケーラビリティ、生産性が向上しました。 <ul><li>業界最高レベルの可用性 SLA 99.9% を実現し、高水準の企業ワークロード要件に適しています。</li><li>Azure Data Lake Store を使用するスケーラブルなストレージ レイヤー。</li><li>データの探索と開発のあらゆる段階における生産性向上ツールです。 カスタマイズされた Spark カーネルを使用する Jupyter Notebook により、対話型のデータ探索が実現します。Power BI、Tableau、Qlik などの BI ダッシュボードとの統合はデータのすばやい共有と継続的なレポート作成に適しており、IntelliJ プラグインは長期的なコード アーティファクト開発およびデバッグ向けに高い信頼性を誇ります。</li></ul> |サービス |Spark |該当なし |
| HDInsight Tools for IntelliJ (IntelliJ 用 HDInsight ツール) |HDInsight の Spark クラスター用の IntelliJ IDEA プラグインです。 次の機能が有効になります。<ul><li>IntelliSense、自動書式設定、エラー チェックなど、優れたオーサリング サポートを使用して、Spark アプリケーションを Scala および Java で作成し記述します。</li><li>ローカルで Spark アプリケーションをテストします。</li><li>HDInsight Spark クラスターにジョブを送信し、結果を取得します。</li><li>Azure にログインし、Azure サブスクリプションに関連付けられているすべての Spark クラスターにアクセスします。</li><li>HDInsight Spark クラスターに関連付けられたすべてのストレージ リソースをナビゲートします。</li><li>HDInsight Spark クラスターのすべてのジョブ履歴とジョブ情報をナビゲートします。</li><li>デスクトップ コンピューターを使用し Spark ジョブをリモートでデバッグできます。</li></ul> |ツール |Spark |該当なし |

## <a name="notes-for-05132016-release-of-hdinsight"></a>HDInsight の 2016 年 5月 13 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.875.2159884 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.875.2159884 (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.922.2266903  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.922.2266903  (HDP 2.2.9.1-11)
* HDInsight (Windows)        3.3.0.922.2266903  (HDP 2.3.3.1-18)
* HDInsight    (Linux)            3.2.1000.0.7565644   (HDP    2.2.9.1-11)
* HDInsight (Linux)            3.3.1000.0.7565644   (HDP 2.3.3.1-18)
* HDInsight (Linux)            3.4.1000.0.7548380   (HDP 2.4.2.0)

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Spark、Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| Spark バージョンの更新とその他のバグ修正 |このリリースでは、HDInsight クラスターの Spark バージョンを 1.6.1 に更新し、その他のバグを修正します |サービス |Spark |該当なし |

## <a name="notes-for-04112016-release-of-hdinsight"></a>HDInsight の 2016 年 4 月 11 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.889.2191206 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.889.2191206 (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.889.2191206  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.889.2191206  (HDP 2.2.9.1-10)
* HDInsight (Windows)        3.3.0.889.2191206  (HDP 2.3.3.1-16 - 変更なし)
* HDInsight    (Linux)            3.2.1000.0.7339916   (HDP 2.2.9.1-10)
* HDInsight (Linux)            3.3.1000.0.7339916   (HDP 2.3.3.1-16)
* HDInsight (Linux)            3.4.1000.0.7338911   (HDP 2.4.1.1-3)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| HDI 3.4 でのカスタム メタストアのアップグレードに関する問題 |以前に下位バージョンの別の HDInsight クラスターで使用されていたカスタム メタストアを使用した場合、クラスターの作成が失敗しました。 これは、現在では修正されたアップグレード スクリプトのエラーによるものでした |クラスターの作成 |すべて |該当なし |
| Livy クラッシュ復旧 |Livy を通じて送信されるすべてのジョブについてジョブ状態の復元性を提供します。 |信頼性 |Linux での Spark |該当なし |
| Jupyter コンテンツ HA |クラスターに関連付けられているストレージ アカウントとの間で、Jupyter Notebook の内容を保存およびロードする機能を提供します。 詳細については、 [Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)に関するページをご覧ください。 |Notebook |Linux での Spark |該当なし |
| Jupyter Notebook での hiveContext の削除 |`%%hive` マジックの代わりに `%%sql` マジックを使用します。 sqlContext は hiveContext と同等です。 詳細については、 [Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md) |Notebook |Linux 上の Spark クラスター |該当なし |
| Spark の古いバージョンの非推奨 |古い Spark 1.3.1 バージョンは、5 月 31 でサービスが終了します。 |サービス |Windows 上の Spark クラスター |該当なし |

## <a name="notes-for-03292016-release-of-hdinsight"></a>HDInsight の 2016 年 3 月 29 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.875.2159884 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.875.2159884 (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.875.2159884  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.875.2159884  (HDP 2.2.9.1-7 - 変更なし)
* HDInsight (Windows)        3.3.0.875.2159884  (HDP 2.3.3.1-16)
* HDInsight    (Linux)            3.2.1000.0.7193255   (HDP    2.2.9.1-8 - 変更なし)
* HDInsight (Linux)            3.3.1000.0.7193255   (HDP 2.3.3.1-7 - 変更なし)
* HDInsight (Linux)            3.4.1000.0.7195842   (HDP 2.4.1.0-327)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターに HDInsight 3.4 バージョンが追加され、HDP バージョンが更新されました |このリリースでは、HDInsight v3.4 (HDP 2.4 に基づく) が追加され、他の HDP バージョンも更新されました。 HDP 2.4 リリース ノートについては、[こちら](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)を参照してください。また、HDInsight バージョンの詳細については、[こちら](hdinsight-component-versioning.md)を参照してください。 |サービス |すべての Linux クラスター |該当なし |
| HDInsight Premium |HDInsight は、Standard と Premium の 2 つのカテゴリで利用できるようになりました。 HDInsight Premium は現在プレビューの段階で、Linux 上の Hadoop および Spark クラスターでのみ利用できます。 詳細については、 [こちら](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)を参照してください。 |サービス |Linux 上の Hadoop および Spark |該当なし |
| Microsoft R Server |HDInsight Premium は、Linux 上の Hadoop クラスターや Spark クラスターに含めることのできる Microsoft R Server を提供します。 詳細については、 [HDInsight の R Server の概要](hdinsight-hadoop-r-server-overview.md)に関するページをご覧ください。 |サービス |Linux 上の Hadoop および Spark |該当なし |
| Spark 1.6.0 |HDInsight 3.4 クラスターに Spark 1.6.0 が導入されました |サービス |Linux 上の Spark クラスター |該当なし |
| Jupyter Notebook の拡張機能 |Spark クラスターで使用できる Jupyter Notebook に、追加の Spark カーネルが備わりました。 また、%%magic の使用、自動視覚化、Python 視覚ライブラリ (matplotlib など) との統合などの拡張機能も含まれています。 詳細については、 [Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)に関するページをご覧ください。 |サービス |Linux 上の Spark クラスター |該当なし |

## <a name="notes-for-03222016-release-of-hdinsight"></a>HDInsight の 2016 年 3 月 22 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.875.2159884 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.875.2159884 (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.875.2159884  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.875.2159884  (HDP 2.2.9.1-7 - 変更なし)
* HDInsight (Windows)        3.3.0.875.2159884  (HDP 2.3.3.1-16)
* HDInsight    (Linux)            3.2.1000.0.7193255   (HDP    2.2.9.1-8 - 変更なし)
* HDInsight (Linux)            3.3.1000.0.7193255   (HDP 2.3.3.1-7 - 変更なし)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターの HDInsight のバージョンの更新 |このリリースで、すべての HDInsight クラスターの HDInsight のバージョンを更新しました |サービス |すべて |該当なし |

## <a name="notes-for-03102016-release-of-hdinsight"></a>HDInsight の 2016 年 3 月 10 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.859.2123216 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.859.2123216 (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.859.2123216  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.859.2123216  (HDP 2.2.9.1-7)
* HDInsight (Windows)        3.3.0.859.2123216  (HDP 2.3.3.1-5 - 変更なし)
* HDInsight    (Linux)            3.2.1000.7076817   (HDP    2.2.9.1-8)
* HDInsight (Linux)            3.3.1000.7076817   (HDP 2.3.3.1-7)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターの HDInsight のバージョンの更新 |このリリースで、すべての HDInsight クラスターの HDInsight のバージョンを更新しました |サービス |すべて |該当なし |

## <a name="notes-for-01272016-release-of-hdinsight"></a>HDInsight の 2016 年 1 月 27 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.817.2028315 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.817.2028315 (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.817.2028315  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.817.2028315  (HDP 2.2.9.1-1)
* HDInsight (Windows)        3.3.0.817.2028315  (HDP 2.3.3.1-5 - 変更なし)
* HDInsight    (Linux)            3.2.1000.4072335   (HDP    2.2.9.1-1)
* HDInsight (Linux)            3.3.1000.4072335   (HDP 2.3.3.1-1)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターの HDInsight のバージョンの更新 |このリリースで、すべての HDInsight クラスターの HDInsight のバージョンを更新しました |サービス |すべて |該当なし |

## <a name="notes-for-12022015-release-of-hdinsight"></a>HDInsight 3.1 の 2015 年 12 月 2 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.763.1931434 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.763.1931434 (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.763.1931434  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.763.1931434  (HDP 2.2.7.1-34 - 変更なし)
* HDInsight (Windows)        3.3.1000.0           (HDP 2.3.3.1-5)
* HDInsight    (Linux)            3.2.1000.0.6392801 (HDP    2.2.7.1-34 - 変更なし)
* HDInsight (Linux)            3.3.1000.0           (HDP 2.3.3.0-3039)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターに HDInsight 3.3 バージョンが追加され、HDP バージョンが更新されました |このリリースでは、HDInsight v3.3 (HDP 2.3 に基づく) が追加され、他の HDP バージョンも更新されました。 HDP 2.3 リリース ノートについては、[こちら](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)を参照してください。また、HDInsight バージョンの詳細については、[こちら](hdinsight-component-versioning.md)を参照してください。 |サービス |すべて |該当なし |

## <a name="notes-for-11302015-release-of-hdinsight"></a>HDInsight の 2015 年 11 月 30 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.757.1923908 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.757.1923908  (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.757.1923908  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.757.1923908  (HDP 2.2.7.1-34)
* HDInsight    (Linux)            3.2.1000.0.6392801 (HDP    2.2.7.1-34)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターの HDInsight バージョンと HDInsight 3.2 クラスターの HDP バージョンの更新 (Windows と Linux) |このリリースでは、HDInsight と HDP のバージョンが更新されています |サービス |すべて |該当なし |

## <a name="notes-for-10272015-release-of-hdinsight"></a>HDInsight の 2015 年 10 月 27 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight    (Windows)         2.1.10.726.1866228 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight    (Windows)         3.0.6.726.1866228  (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight    (Windows)         3.1.4.726.1866228  (HDP 2.1.15.0-2374 - 変更なし)
* HDInsight    (Windows)        3.2.7.726.1866228  (HDP 2.2.7.1-33)
* HDInsight    (Linux)            3.2.1000.0.6035701 (HDP    2.2.7.1-33)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターの HDInsight のバージョンの更新 (Windows と Linux) |このリリースでは、HDInsight と HDP のバージョンが更新されています |サービス |すべて |該当なし |
| 大文字のクラスターを含む Jupyter for Windows Spark クラスターを修正しました |DNS 名が大文字で指定されたクラスターは、元の要求チェックのため、Jupyter Notebook で問題が発生していました。 今回の修正では、Jupyter の構成の DNS 名を小文字に変更されました。 |サービス |HDInsight Spark (Windows) |該当なし |

## <a name="notes-for-10202015-release-of-hdinsight"></a>HDInsight 3.1 の 2015 年 10 月 20 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.716.1846990 (Windows)    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.716.1846990 (Windows)      (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.4.716.1846990 (Windows)      (HDP 2.1.16.0-2374)
* HDInsight        3.2.7.716.1846990 (Windows)      (HDP 2.2.7.1-0004)
* HDInsight        3.2.1000.0.5930166 (Linux)        (HDP 2.2.7.1-0004)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| 既定 HDP バージョンが HDP 2.2 に変更されました |HDInsight Windows クラスターの既定のバージョンが HDP 2.2 に変更されました。 HDInsight バージョン 3.2 (HDP 2.2) が 2015 年 2 月から一般公開されました。 この変更によって、Azure Portal、PowerShell コマンドレット、または SDK を使用してクラスターをプロビジョニングするときに、クラスター バージョンを明示的に選択していない場合の既定のバージョンのみが変わります。 |サービス |すべて |該当なし |
| 1 つの Virtual Network の Linux クラスターに複数の HDInsight をデプロイする場合の VM 名の形式の変更 |今回のリリースで、1 つの仮想ネットワークに複数の HDInsight Linux クラスターをデプロイできるようになりました。 その一環で、クラスター内の仮想マシン名の形式が headnode\*、workernode\*、zookeepernode\* からそれぞれ hn\*、wn\*、zk\* に変更されました。 仮想ネットワーク名は変更される可能性があるため、仮想ネットワーク名の形式に直接依存することは推奨されません。 ローカル コンピューターで "hostname -f" を使用するか、Ambari API を使用して、ホスト一覧と、コンポーネントからホストへのマッピングを取得してください。 詳細については、[https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) と [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md) を参照してください。 |サービス |Linux 上の HDInsight クラスター |該当なし |
| 構成の変更 |HDInsight 3.1 クラスターでは、次の構成が有効になりました。 <ul><li>tez.yarn.ats.enabled および yarn.log.server.url。 これにより、Application Timeline Server とログ サーバーからログを提供できるようになりました。</li></ul>HDInsight 3.2 クラスターでは、次の構成が変更されています。 <ul><li>mapreduce.fileoutputcommitter.algorithm.version が 2 に設定されました。 これにより、V2 バージョンの FileOutputCommitter を使用できるようになりました。</li></ul> |サービス |すべて |該当なし |

## <a name="notes-for-09092015-release-of-hdinsight"></a>HDInsight の 2015 年 9 月 9 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.675.1768697 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.675.1768697  (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.4.675.1768697  (HDP 2.1.15.0-2334 - 変更なし)
* HDInsight        3.2.6.675.1768697  (HDP 2.2.6.1-0012 - 変更なし)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターの HDInsight のバージョンの更新 |このリリースでは、HDInsight のバージョンが更新されています |サービス |すべて |該当なし |

## <a name="notes-for-07312015-release-of-hdinsight"></a>HDInsight の 2015 年 7 月 31 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.640.1695824 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.640.1695824  (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.4.640.1695824  (HDP 2.1.15.0-2334 - 変更なし)
* HDInsight        3.2.6.640.1695824  (HDP 2.2.6.1-0012 - 変更なし)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| Spark クラスター ノードの再イメージ化ワークフローの修正 |再イメージ化後に Spark クラスター ノードが復元できないバグを修正しました |サービス |Spark |該当なし |

## <a name="notes-for-07312015-release-of-hdinsight"></a>HDInsight の 2015 年 7 月 31 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.635.1684502 (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.635.1684502  (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.4.635.1684502  (HDP 2.1.15.0-2334 - 変更なし)
* HDInsight        3.2.6.635.1684502  (HDP 2.2.6.1-0012 - 変更なし)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| すべての HDInsight クラスターの HDInsight のバージョンの更新 |このリリースでは、HDInsight のバージョンが更新されています |サービス |すべて |該当なし |

## <a name="notes-for-07072015-release-of-hdinsight"></a>HDInsight の 2015 年 7 月 7 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.610.1630216    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.610.1630216    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.4.610.1630216    (HDP 2.1.15.0-2334 - 変更なし)
* HDInsight        3.2.4.610.1630216    (HDP 2.2.6.1-0012)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

| タイトル | Description | 影響を受ける領域 (例: サービス、コンポーネント、SDK) | クラスターの種類 (例: Hadoop、HBase、Storm) | JIRA (該当する場合) |
| --- | --- | --- | --- | --- |
| HDInsight 3.2 クラスターの更新された HDP バージョン |このリリースでは、HDInsight 3.2 は HDP 2.2.6.1-0012 をデプロイします |サービス |すべて |該当なし |

## <a name="notes-for-06262015-release-of-hdinsight"></a>HDInsight の 2015 年 06 月 26 日リリース ノート
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.601.1610731    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.601.1610731    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.4.601.1610731    (HDP 2.1.15.0-2334 - 変更なし)
* HDInsight        3.2.4.601.1610731    (HDP 2.2.6.1-0011)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>HDInsight 3.2 クラスターの更新された HDP バージョン</td>
<td>このリリースでは、HDInsight 3.2 は HDP 2.2.6.1 をデプロイします</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>HDInsight の 2015 年 06 月 18 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.596.1601657    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.596.1601657    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.4.596.1601657    (HDP 2.1.15.0-2334)
* HDInsight        3.2.4.596.1601657    (HDP 2.2.6.1-0002)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>追加で開かれた HTTPS ポート</td>
<td>クラウド サービスは、クラスターの 5 つのポート (8001 から 8005) を開くようになりました (例:  https://<clustername>.azurehdinsight.net:8001/)。 これらの URL に対する要求は、ポート 443 と同じ基本認証パスワード メカニズムを使用して認証されます。 これらのポートは、アクティブなヘッドノード上の同じポートにバインドされます。 スクリプト アクションを使用して、カスタマー サービスがヘッドノード上のこれらのポートをリッスンし、クラスターの外部にルーティングするように設定できます。</td>
<td>クラウド サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>HDInsight 3.2 の断続的な MapReduce のシャッフルの問題</td>
<td>大規模なクラスター上での MapReduce のシャッフルにおいてまれに断続的な競合状態が発生し、タスクが失敗することがある問題を修正しました。 詳細については、<a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a> に関するページを参照してください。</td>
<td>Hadoop Core</td>
<td>すべて</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a></td>
</tr>
<tr>
<td>HDInsight 3.2 向けの最新の Azure Java SDK 2.2 への移行</td>
<td>WASB ドライバーによって使用される Azure SDK for Java の最新バージョンに移行しました。 最新の SDK にはいくつかの修正が加えられています。リリース ノートは、https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt で入手できます。</td>
<td>Hadoop Core</td>
<td>すべて</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP-11959</a></td>
</tr>
<tr>
<td>HDInsight 3.1 クラスター向けの HDP 2.1.15 への移行</td>
<td>Hortonworks 社発行のこのリリースに関するリリース ノートは、<a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">ここ</a>から入手できます。</td>
<td>HDP</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>HDInsight の 2015 年 06 月 4 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.583.1575584    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.583.1575584    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.3.583.1575584    (HDP 2.1.12.1-0003 - 変更なし)
* HDInsight        3.2.4.583.1575584    (HDP 2.2.6.1-1)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>Storm クラスターの 502 bad gateway エラーの修正</td>
<td>このリリースでは、再起動後に Web サイトがダウンする原因となるジョブの送信 API に影響を及ぼすバグを修正しました。</td>
<td>サービス</td>
<td>Storm</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>HDInsight の 2015 年 06 月 1 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.577.1563827    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.577.1563827    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.3.577.1563827    (HDP 2.1.12.1-0003 - 変更なし)
* HDInsight        3.2.4.577.1563827    (HDP 2.2.6.0-2800 - 変更なし)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>さまざまなバグの修正</td>
<td>このリリースでは、クラスターのプロビジョニングに関連するバグを修正しました。</td>
<td>サービス</td>
<td>すべてのクラスターの種類</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>HDInsight の 2015 年 5 月 27 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight        3.2.4.570.1554102    (HDP 2.2.6.0-2800)
* その他のクラスター バージョンおよび SDK は、このリリースの一部としてデプロイされません。

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>HDP 2.2 の更新</td>
<td>HDInsight 3.2 のこのリリースには HDP 2.2.6 が含まれ、HDInsight のいくつかの重要なバグが修正されています。 完全なリリース ノートは、<a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 のリリース ノート</a>に関するページから入手できます。</td>
<td>HDP</td>
<td>すべてのクラスターの種類</td>
<td>該当なし</td>
</tr>
<tr>
<td>既定の Yarn コンテナーのメモリ構成の変更</td>
<td>この更新では、Node Manager によって起動される YARN コンテナー (yarn.nodemanager.resource.memory-mb および yarn.scheduler.maximum-allocation-mb) が既定で使用できるメモリが 5,632 MB に増えました。 従来この値は 4,608 MB に減らされていましたが、さまざまなジョブの実行に基づいて、この新しい値の方がほとんどのジョブにおいてより高い信頼性とパフォーマンスが得られることが確認されたため、より適切な既定値として採用されました。 従来どおり、このメモリ構成に対する重要な依存関係がある場合は、クラスターの作成時に明示的に設定してください。</td>
<td>HDP</td>
<td>すべてのクラスターの種類</td>
<td>該当なし</td>
</tr>
<tr>
<td>HBase クラスターと Storm クラスターの既定の構成パリティ</td>
<td>この更新では、Hbase クラスターと Storm クラスターが Hadoop クラスターと同じ YARN 構成の値を使用するように再び設定されました。 これは、すべてのクラスターの種類のパリティに適用されます。</td>
<td>HDP</td>
<td>HBase、Storm</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>HDInsight の 2015 年 5 月 20 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.564.1542093    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.564.1542093    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.3.564.1542093    (HDP 2.1.12.1-0003)
* HDInsight        3.2.4.564.1542093    (HDP 2.2.4.6-2)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>SCP.NET EventHub のサポート</td>
<td>HDInsight Storm の更新されたクラスター パッケージにより、SCP.NET の新しい機能が提供されます。 トポロジ ビルダーで、EventHubSpout または Java スパウトをより簡単に使用できる新しい API にアクセスできます。 コントラクトが更新されているため、新しいクラスターを使用するには、SCP.NET クライアント SDK を更新する必要があります。 新しい API の詳細、使用法、および (バグの修正を含む) リリース ノートについては、SCP.NET nuget パッケージに含まれる Readme を参照してください。</td>
<td>VS のツール</td>
<td>Storm HDInsight 3.2 クラスター</td>
<td>該当なし</td>
</tr>
<tr>
<td>JDBC ドライバーの更新</td>
<td>sqljdbc_4.1.5605.100 で、SQL Server でサポートされているバージョンにドライバーを更新しました。</td>
<td>メタストア</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>HDInsight の 2015 年 4 月 27 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.537.1486660    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.537.1486660    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.3.537.1486660    (HDP 2.1.12.0-2329 - 変更なし)
* HDInsight        3.2.3.537.1486660    (HDP 2.2.2.2-4)
* SDK            1.5.8

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>DLL 依存関係の修正</td>
<td>単体テスト フレームワークで HDInsight 依存関係を削除しました。</td>
<td>SDK</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>競合状態のバグの修正</td>
<td>クラスター作成要求が状態をポーリングする前に PUT 要求の受理を待機するようになりました。</td>
<td>SDK</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>HDInsight の 2015 年 4 月 14 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.521.1453250    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.521.1453250    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.3.521.1453250    (HDP 2.1.12.0-2329 - 変更なし)
* HDInsight        3.2.3.525.1459730    (HDP 2.2.2.2-2)
* SDK            1.5.6

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>Tez バグの修正</td>
<td>Apache TEZ 2214 と TEZ 1923 の修正はこの HDI 3.2 リリースに含まれます。 これらは、Tez で大量のデータのシャッフルを必要とする特定の Hive クエリ に特に必要です。
</td>
<td>HDP</td>
<td>Hadoop は、</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>HDInsight の 2015 年 4 月 6 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.521.1453250    (HDP 1.3.12.0-01795 - 変更なし)
* HDInsight     3.0.6.521.1453250    (HDP 2.0.13.0-2117 - 変更なし)
* HDInsight     3.1.3.521.1453250    (HDP 2.1.12.0-2329 - 変更なし)
* HDInsight        3.2.3.521.1453250    (HDP 2.2.2.2-1)
* SDK            1.5.6

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Linux で HDInsight の内部クラスを一部削除する更新を実施します。</td>
<td>SDK</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>Avro Library 1.5.6</td>
<td><b>KnownTypeAttribute</b> を <b>GetAllKnownTypes</b> メソッドに追加しました。 GetAllKnownTypes メソッドの型が Null の場合の NullReferenceException を修正しました。</td>
<td>SDK</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>バグの修正</td>
<td>サービスに対するさまざまなバグの修正</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-04012015-release-of-hdinsight"></a>HDInsight の 2015 年 4 月 1 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.513.1431705    (HDP 1.3.12.0-01795)
* HDInsight     3.0.6.513.1431705    (HDP 2.0.13.0-2117)
* HDInsight     3.1.3.513.1431705    (HDP 2.1.12.0-2329)
* HDInsight        3.2.3.513.1431705    (HDP 2.2.2.1-2600)
* SDK            1.5.5

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>.NET SDK を介した Windows クラスターでのリモート デスクトップの資格情報を有効または無効にする機能</td>
<td>Windows クラスターで RDP 資格情報の有効化または無効化をプログラムでサポートします。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>クラスターのプロビジョニング中にリモート デスクトップ資格情報を有効にする機能</td>
<td>クラスターの作成中にリモート デスクトップの資格情報をプログラムで有効にします。 これにより、クラスターを最初にプロビジョニングしてからリモート デスクトップを有効にするといった 2 段階のプロセスが削除されます。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>Python 2.7.8 へのアップグレード</td>
<td>HDInsight クラスターの Python を Python 2.7.8 にアップグレードしました。これには HDInsight バージョン 2.1、3.0、3.1、3.2 の重要なセキュリティ上の修正が含まれます。</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>YARN 構成の変更</td>
<td>HDInsight バージョン 3.1 と 3.2 のすべてのクラスターの種類について、YARN 構成 yarn.resourcemanager.max-completed-applications を 1000 に変更しました。 この値は、YARN UI で完了したアプリケーションの一覧のみを制御します。 UI に表示されるアプリケーションの一覧の前に送信されたアプリケーションに関する情報は、履歴サーバーから直接入手できます。</td>
<td>YARN</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>HBase クラスターでのノード サイズの変更</td>
<td>HBase クラスターで HDInsight バージョン 3.1 と 3.2 のノード (上下) サイズを変更できるようになりました。</td>
<td>サービス</td>
<td>hbase</td>
<td>該当なし</td>
</tr>
<tr>
<td>JDBC のアップグレード</td>
<td>SQL JDBC ドライバーは、HDInsight バージョン 3.2 の sqljdbc_4.0.2206.100 のバージョンにアップグレードされます。 このバージョンには、重要なセキュリティの機能強化が含まれています。</td>
<td>HDP</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>JVM 構成の更新</td>
<td>HDInsight バージョン 3.1 と 3.2 の JVM 構成 networkaddress.cache.ttl を既定値の -1 から 300 秒に更新しました。 この構成値は、ネーム サービスから正常な名前参照のキャッシュ ポリシーを制御します。 これにより、HBase クラスターの拡張と縮小に関連するバグが修正されます。</td>
<td>サービス</td>
<td>hbase</td>
<td>該当なし</td>
</tr>
<tr>
<td>Azure Storage SDK for Java 2.0 へのアップグレード</td>
<td>HDInsight バージョン 3.2 が Azure Storage SDK for Java の最新バージョンを使用するようアップグレードされます。 これには、現在の 0.6.0 バージョンの重要なバグ修正がいくつか含まれます。</td>
<td>HDP</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>Apache WASB のソース コードへのアップグレード</td>
<td>HDInsight バージョン 3.2 で Apache Hadoop から WASB ファイル システム ドライバー用の最新のコードを使用するようになりました。 この変更により、WASB ドライバーは個別の jar としてパッケージ化されます。 これは単にパッケージの変更だけであり、WASB ドライバーの動作への変更は含まれません。 この JAR ファイルの名前は、hadoop-azure-2.6.0.jar です。</td>
<td>HDP</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>HDInsight 3.2 での Jar ファイル名の更新</td>
<td>この HDInsight バージョン 3.2 への更新にはいくつかのバグ修正が含まれ、HDP の一部としてパッケージ化されたいくつかの内部 jar がアップグレードされています。 これらの JAR ファイルはお客様のアプリケーションで直接使用するのではなく、HDP のパッケージ内部であることにご注意ください。 HDP へのアップグレードによってお客様のアプリケーションを損なわないように、アプリケーションは独自のバージョンの JAR でパッケージ化する必要があります。</td>
<td>HDP</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-03032015-release-of-hdinsight"></a>HDInsight の 2015 年 3 月 3 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.488.1375841    (HDP 1.3.9.0-01351 - 変更なし)
* HDInsight     3.0.6.488.1375841    (HDP 2.0.9.0-2097 -  変更なし)
* HDInsight     3.1.3.488.1375841    (HDP 2.1.10.0-2290 - 変更なし)
* HDInsight        3.2.3.488.1375841    (HDP-2.2.10.0-2340 - 変更なし)
* SDK            1.5.0                (変更なし)

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA</th>
</tr>
<tr>
<td>信頼性に関する機能強化</td>
<td>クラスター作成に関する負荷の増加に応じてスケールが可能なサービスに修正しました。</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-02182015-release-of-hdinsight"></a>HDInsight の 2015 年 2 月 18 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.471.1342507    (HDP 1.3.9.0-01351 - 変更なし)
* HDInsight     3.0.6.471.1342507    (HDP 2.0.9.0-2097 -  変更なし)
* HDInsight     3.1.3.471.1342507    (HDP 2.1.10.0-2290 - 変更なし)
* HDInsight        3.2.3.471.1342507    (HDP-2.2.10.0-2340)
* SDK            1.5.0

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>HDInsight 3.2 クラスター</td>
<td>Hadoop 2.6/HDP2.2 は HDInsight 3.2 クラスターで使用できます。 これにはすべてのオープン ソース コンポーネントの主要な更新プログラムが含まれます。 詳細については、「HDInsight での新機能」と「<a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">HDP 2.2.0.0 リリース ノート</a>」を参照してください。</td>
<td>オープンソース ソフトウェア</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>Linux での HDInsight (プレビュー)</td>
<td>Ubuntu Linux で実行されているクラスターをデプロイできます。 詳細については、「Linux での HDInsight の概要」を参照してください。</td>
<td>サービス</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>Storm の一般公開</td>
<td>Apache Storm クラスターは一般で使用できます。 詳細については、「HDInsight での Storm 使用方法の概要」を参照してください。</td>
<td>サービス</td>
<td>Storm</td>
<td>該当なし</td>
</tr>
<tr>
<td>仮想マシン サイズ</td>
<td>Azure HDInsight は、その他の仮想マシンの種類やサイズでも使用可能です。 HDInsight は、汎用目的でビルドされた A2 ～ A7 サイズ、ソリッド ステート ドライブ (SSD) と 60% 高速のプロセッサを特徴とする D シリーズ ノード、高速ネットワーク用 InfiniBand をサポートする A8 と A9 サイズを利用できます。</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>クラスター スケーリング</td>
<td>実行する HDInsight クラスター用データ ノードの数は、削除または再作成しなくても変更できます。 現時点では、Hadoop クエリと Apache Storm クラスターの種類にのみこの機能がありますが、Apache HBase クラスターの種類も近日中にサポートされます。 詳細については、HDInsight クラスターの管理に関するページを参照してください。</td>
<td>サービス</td>
<td>Hadoop、Storm</td>
<td>該当なし</td>
</tr>
<tr>
<td>Visual Studio ツール</td>
<td>Apache Storm のツール一式に加えて、Visual Studio で Apache Hive のツールが更新され、ステートメント入力候補、ローカル検証、改善されたデバッグ サポートが追加されました。 詳細については、「HDInsight Hadoop Tools for Visual Studio の使用開始」を参照してください。</td>
<td>ツール</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<td>DocumentDB 用 Hadoop コネクタ</td>
<td>DocumentDB 用 Hadoop コネクタを使用すれば、DocumentDB コレクション全体またはデータベース アカウント全体に格納されているスキーマのない JSON ドキュメントで複雑な集計、分析、操作を実行できます。 詳細とチュートリアルについては、「DocumentDB と HDInsight を使用した Hadoop ジョブの実行」を参照してください。</td>
<td>サービス</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>バグの修正</td>
<td>HDInsight サービスに対するさまざまなマイナー バグ修正を行いました。 顧客向け動作に変更はありません。</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-02062015-release-of-hdinsight"></a>HDInsight の 2015 年 2 月 6 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.463.1325367    (HDP 1.3.9.0-01351 - 変更なし)
* HDInsight     3.0.6.463.1325367    (HDP 2.0.9.0-2097 -  変更なし)
* HDInsight     3.1.2.463.1325367    (HDP 2.1.10.0-2290)
* SDK            該当なし

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>バグの修正</td>
<td>HDInsight サービスに対するさまざまなマイナー バグ修正を行いました。 顧客向け動作に変更はありません。</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>HDP 2.1 メンテナンスの更新</td>
<td>HDInsight 3.1 が更新され、HDP 2.1.10.0 がデプロイされます。 詳細については、「 <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">HDP-2.1.10 リリース ノート</a>」を参照してください。 </td>
<td>オープンソース ソフトウェア</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>HDP バイナリの更新</td>
<td>HBase の一部の JAR ファイル名が更新されています。 これらの JAR ファイルは HBase で内的に使用するため、顧客がこれらの JAR ファイル名に依存することはありません。 チェックの内容は次のとおりです
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>オープンソース ソフトウェア</td>
<td>HBase</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-1292015-release-of-hdinsight"></a>HDInsight の 2015 年 1 月 29 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.455.1309616    (HDP 1.3.9.0-01351 - 変更なし)
* HDInsight     3.0.6.455.1309616    (HDP 2.0.9.0-2097 -  変更なし)
* HDInsight     3.1.2.455.1309616    (HDP 2.1.9.0-2196 -  変更なし)
* SDK            該当なし

このリリースには、次の更新プログラムが含まれています。

<table border="1">

<tr>
<th>タイトル</th>
<th>Description</th>
<th>影響を受ける領域 (例: サービス、コンポーネント、SDK)</p></th>
<th>クラスターの種類 (例: Hadoop、HBase、Storm)</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>バグの修正</td>
<td>Azure のアップグレード中に HDInsight クラスターの信頼性を向上させる重要な修正をいくつか行いました。</td>
<td>サービス</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-152015-release-of-hdinsight"></a>HDInsight の 2015 年 1 月 5 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.420.1246118    (HDP 1.3.9.0-01351 - 変更なし)
* HDInsight     3.0.6.420.1246118    (HDP 2.0.9.0-2097 - 変更なし)
* HDInsight     3.1.2.420.1246118    (HDP 2.1.9.0-2196 - 変更なし)

このリリースには、次の更新プログラムが含まれています。

<table border="1">

<tr>
<th>タイトル</th>
<th>Description</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>Twitter の傾向分析と Mahout に基づく映画のリコメンデーションのサンプル</td>
<td><p>このリリースでは、HDInsight クエリ コンソールに 2 つの追加のサンプルがあります。</p>

<p><b>Twitter の傾向分析</b><br>
Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。 このチュートリアルで Hive を使用して、特定の単語を含むツイートを最も多く送信した Twitter ユーザーの一覧を取得する方法について説明します。 </p>

<p><b>Mahout 映画のリコメンデーション</b><br>
Apache Mahout は、Apache Hadoop の機械学習ライブラリです。 Mahout には、フィルター処理、分類、クラスタリングなどデータを処理するためのアルゴリズムが含まれています。 このチュートリアルでは、リコメンデーション エンジンを使用し、友人たちが鑑賞した映画に基づいて映画のリコメンデーションを生成します。</p></td>
<td>クエリ コンソール</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>Hive 構成 hive.auto.convert.join.noconditionaltask.size の既定値への変更</td>
<td><p>このサイズ構成は、自動変換されたマップの結合に適用されます。 この値は、メモリに収まるハッシュ マップに変換できるテーブルのサイズの合計を表します。 以前のリリースで、この値は既定値の 10MB から 128MB に増えました。 ただし、128MB という新しい値がメモリ不足でジョブが失敗する原因になっていました。 このリリースでは、既定値を 10 MB に戻します。 ユーザーは、クエリとテーブル サイズに応じて、クラスターの作成中にこの値を上書きできます。 この設定と、それを上書きする方法の詳細については、「<a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Optimize Auto Join Conversion (Auto Join Conversion の最適化)</a>」を参照してください。 </p></td>
<td>Hive</td>
<td>Hadoop、HBase</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-12232014-release-of-hdinsight"></a>HDInsight の 2014 年 12 月 23 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.10.420.1246783    (HDP バージョンは変更なし)
* HDInsight     3.0.6.420.1246783    (HDP バージョンは変更なし)
* HDInsight     3.1.1.420.1246783    (HDP バージョンは変更なし)

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>負荷が高すぎることによる断続的なクラスター作成エラー</td>
<td><p>クラスター作成中の HDP パッケージのダウンロード アルゴリズムの改良により、負荷の増加によるエラーをより確実に処理できます。</p></td>
<td>サービス</td>
<td>Hadoop、Hbase、Storm</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-12182014-release-of-hdinsight"></a>HDInsight の 2014 年 12 月 18 日リリース
このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">クラスターのカスタマイズの一般公開</a></td>
<td><p>カスタマイズ機能により、Apache Hadoop エコシステムから利用できるプロジェクトで Azure HDInsight クラスターをカスタマイズできます。 この新しい機能により、Hadoop プロジェクトを実験し、Azure HDInsight にデプロイできます。 これは、カスタム スクリプトを使用して、Hadoop クラスターを任意の方法で変更できる **スクリプト アクション** 機能により実現します。 このカスタマイズ機能は、Hadoop、HBase、および Storm などあらゆる種類の HDInsight クラスターで利用できます。 この機能の能力を示すために、一般的な <a href = "hdinsight-hadoop-spark-install.md" target="_blank">Spark</a>、<a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>、<a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>、<a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> モジュールをインストールするプロセスを説明しています。 またこのリリースでは、ユーザーが Azure Portalを使用してカスタム スクリプト アクションを指定する機能が追加され、ヘルパー メソッドを使用してカスタム スクリプト アクションを作成するためのガイドラインとベスト プラクティスの提供、さらにスクリプト アクションをテストする方法に関するガイドラインが提供されます。 </p></td>
<td>機能の一般公開</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-12052014-release-of-hdinsight"></a>HDInsight の 2014 年 12 月 5 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight     2.1.9.406.1221105    (HDP 1.3.9.0-01351)
* HDInsight     3.0.5.406.1221105    (HDP 2.0.9.0-2097)
* HDInsight     3.1.1.406.1221105    (HDP 2.1.9.0-2196)
* HDInsight SDK N/A

このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr>
<th>タイトル</th>
<th>Description</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>
<tr>
<td>バグの修正: Hive DDL でテーブルに大量のパーティションを追加するときに断続的にエラーが発生する。 </td>
<td><p>Hive テーブルに対してパーティションを大量に追加しようとしたときに、Hive メタストア データベースとの間に断続的に接続エラーが発生した場合には、Hive DDL にエラーが発生することがあります。 このエラーが発生した場合には、この Hive エラー ログに以下の文言が表示されます。 </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop、HBase</td>
<td>HIVE-482 (これは、内部 JIRA の一種です。このため、外部から引用することはできません。 ここには参考までに記載しています)</td>
</tr>
<tr>
<td>バグの修正: HDInsight クエリ コンソールが応答を停止することがある</td>
<td>このエラーが発生すると、WebHCat ランチャー ジョブの WebHCat ログに以下の文言が表示されます。 <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop は、</td>
<td>HIVE-482 (これは、内部 JIRA の一種です。このため、外部から引用することはできません。 ここには参考までに記載しています)</td>
</tr>
<tr>
<td>バグの修正: HBase クエリの待機時間が急激に増加することがある</td>
<td>このエラーが発生すると、HBase クエリによる待ち時間が突然 3 秒に増加します。 </td>
<td>HDInsight クラスター ゲートウェイ</td>
<td>HBase</td>
<td>該当なし</td>
</tr>
<tr>
<td>HDP JAR ファイルの名称変更</td>
<td>バージョン 3.0 の HDI クラスターでは、HDP によってイントールされる内部の JAR ファイルの名称がいくつか変更になります。 jetty-6.1.26.jar は、jetty-6.1.26.hwx.jar という名称に変更になります。 jetty-util-6.1.26.jar は、jetty-util-6.1.26.hwx.jar という名称に変更になります。 これらの変更は、Hadoop、Mahout、WebHCat および Oozie プロジェクトに適用されます。</td>
<td>Hadoop、Mahout、WebHCat、Oozie</td>
<td>Hadoop、HBase</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-11212014-release-of-hdinsight"></a>HDInsight の 2014 年 11 月 25 日リリース
今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 2.1.9.382.1169709 (2014 年 11 月 14 日から変更なし)
* HDInsight 3.0.5.382.1169709 (2014 年 11 月 14 日から変更なし)
* HDInsight 3.1.1.382.1169709 (2014 年 11 月 14 日から変更なし)
* HDInsight SDK 1.4.0

このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr><th>タイトル</th><th>Description</th><th>コンポーネント</th><th>クラスターの種類</th><th>JIRA (該当する場合)</th></tr>
<tr>
<td>アプリケーション ログへのアクセス</td>
<td>クラスターで実行されたアプリケーションをプログラムを使って列挙し、アプリケーションに固有またはコンテナーごとのログをダウンロードできるようになりました。この機能は、問題のあるアプリケーションのデバッグに役立ちます。</td>
<td>SDK</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>IHdInsightClient.DeleteCluster でのリージョン名の指定 </td>
<td>Azure HDInsight SDK で **DeleteCluster** の使用時にリージョン名を指定できます。 これまでは、別々のリージョンに同じ名前のリソースが 1 つずつある場合に、どちらかだけを削除することはできませんでした。この更新では、そのような問題が解消します。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>ClusterDetails.DeploymentId</td>
<td>**ClusterDetails** オブジェクトが **DeploymentID** フィールドを返すようになりました。このフィールドは、クラスターを一意に識別する情報です。 この情報は、同じ名前のクラスターを作成しようとしても常に一意の状態が確保されます。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

## <a name="notes-for-11142014-release-of-hdinsight"></a>HDInsight の 2014 年 11 月 14 日リリース

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

このリリースでは、以下の新機能、コンポーネントの更新、バグ修正が実施されます。

<table border="1">
<tr><th>タイトル</th><th>Description</th><th>コンポーネント</th><th>クラスターの種類</th><th>JIRA (該当する場合)</th></tr>
<tr>
<td>スクリプト アクション (プレビュー):</td>
<td>クラスター カスタマイズ機能のプレビュー版であり、カスタム スクリプトを使用して任意の方法で Hadoop クラスターを変更できます。 この機能により、ユーザーは、Apache Hadoop エコシステムから入手できるプロジェクトを検証し、Azure HDInsight クラスターにデプロイできます。 このカスタマイズ機能は、Hadoop、HBase、Storm など、すべての種類の HDInsight クラスターで使用できます。</td>
<td>新機能</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>Azure Websites および Azure Storage のログ分析用に既成のジョブを用意</td>
<td>HDInsight クエリ コンソールには、既にあるデータまたはサンプルのデータを使用するソリューションで役立つ初心者向けギャラリーが用意されています。
<p>**既にあるデータを使用するソリューション**:<br>
データ分析のなかでも特によくあるもののいくつかについて、ジョブを作成しました。自分でソリューションを作成するときの第一歩として役立ちます。 各ジョブには既にお持ちのデータを使って、どのように動作するかを確認できます。 その後準備ができたら、これまでに得た知識を使って、既成のジョブを手本にソリューションを作成します。</p>
<p>**サンプルのデータを使用するソリューション**:<br>
Web ログやセンサー データの分析など、基本的なシナリオをいくつかなぞっていくことによって、HDInsight の使い方を学習できます。 HDInsight を使ってデータを分析する方法だけでなく、その他のアプリケーションやサービスにこのデータを接続する方法も学べます。 こちらは、Microsoft Excel に接続してデータを視覚化する場合などに役立ちます。</p></td>
<td>クエリ コンソール</td>
<td>Hadoop は、</td>
<td>該当なし</td>
</tr>
<tr>
<td>Templeton のメモリ リークを修正</td>
<td>クラスターを長時間実行したり、1 秒あたり何百回ものジョブ リクエストを送信したりした場合に問題になっていた Templeton のメモリ リークを修正しました。 この Templeton 5xx という種のエラーについては、回避策としてサービスを再起動する必要がありました。 今回の更新により、再起動は不要になりました。</td>
<td>Templeton</td>
<td>すべて</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>

> [!NOTE]
> クラスターのカスタマイズによって利用が可能になる新機能の例として、スクリプト アクションを使ってクラスターに Spark と R の 2 つのモジュールをインストールする手順が文書化されています。 詳細については、以下を参照してください。

* [HDInsight クラスターに Spark 1.0 をインストールして使用する](hdinsight-hadoop-spark-install.md)
* [HDInsight Hadoop クラスターに R をインストールして使用する](hdinsight-hadoop-r-scripts.md)

## <a name="notes-for-11072014-release-of-hdinsight"></a>HDInsight の 2014 年 11 月 7 日リリース

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 2.1    2.1.9.374.1153876
* HDInsight 3.0    3.0.5.374.1153876
* HDInsight 3.1    3.1.1.374.1153876

このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr><th>タイトル</th><th>Description</th><th>コンポーネント</th><th>クラスターの種類</th><th>JIRA (該当する場合)</th></tr>
<tr>
<td>HDP 2.1.7</td>
<td>このリリースは、Hortonworks Data Platform (HDP) 2.1.7 に基づくものです。 詳細については、「<a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">HDP-2.1.7 リリース ノート</a>」を参照してください。</td>
<td>HDP</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
<tr>
<td>YARN タイムライン サーバー</td>
<td>YARN Timeline Server (別名 Generic Application History Server) は、既定で有効になっています。 Timeline Server は、アプリケーション ID、アプリケーション名、アプリケーションの状態、アプリケーションの送信時刻、アプリケーションの完了時刻など、完了済みのアプリケーションに関する全般的な情報を提供するものです。

このアプリケーション情報は、ヘッド ノードから URI (http://headnodehost:8188) にアクセスするか、YARN コマンド (yarn application -list -appStates ALL) を実行すると取得できます。

この情報は、REST API を使って https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/ からリモートで取得することもできます。

詳細については、「<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a>」を参照してください。</td>
<td>サービス、YARN</td>
<td>Hadoop、HBase</td>
<td>該当なし</td>
</tr>
<tr>
<td>クラスター デプロイメント ID</td>
<td>SDK の最新バージョン (1.3.3.1.5426.29232) より、ユーザーは、HDInsight が各クラスターに対して発行した一意の ID にアクセスできます。 これにより、作成とドロップのシナリオで DNS 名を再利用する場合に、ユーザーがクラスターのインスタンスを一意に特定できるようになります。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>

> [!NOTE]
> ポータルにバージョン番号の全体が表示されなかったバグと、SDK または Windows PowerShell によって返されるバージョン番号が全部でなかったバグについても、今回のリリースで修正しました。

## <a name="notes-for-10152014-release"></a>2014 年 10 月 15 日リリース

この修正プログラムのリリースでは、Templeton のヘビー ユーザーに影響を与えた Templeton のメモリ リークを修正しました。 場合によっては、要求の実行に十分なメモリがないために、Templeton を何回も実行したユーザーに 500 エラー コードとしてエラーが表示されることがあります。 Templeton サービスを再起動すると、この問題を回避できます。 この問題は修正されています。

## <a name="notes-for-1072014-release"></a>2014 年 10 月 7 日リリース

* Ambari エンドポイント (https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}) を使用すると、*host_name* フィールドがホスト名だけではなくノードの完全修飾ドメイン名 (FQDN) を返します。 たとえば、"**headnode0**" を返す代わりに、FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**" を返します。 この変更は、1 つの仮想ネットワークに HBase や Hadoop などの複数のクラスターの種類をデプロイできるシナリオの実現を容易にするために必須でした。 このシナリオは、Hadoop のバックエンド プラットフォームとして HBase を使用する場合などが該当します。

* HDInsight クラスターの既定のデプロイ用に新しいメモリ設定を提供しています。 前の既定のメモリ設定は、デプロイされる CPU コアの数についてガイダンスの考慮が適切ではありませんでした。 今回の新しいメモリ設定は、Hortonworks による推奨に基づき、既定の設定を改善するものです。 メモリ設定の変更については、クラスター構成の変更に関する SDK リファレンス ドキュメントを参照してください。 既定の 4 CPU コア (8 コンテナー) HDInsight クラスターで使用される新しいメモリ設定を次の表に示します (前のリリースで使用された値もかっこ内に記載します)。

<table border="1">
<tr><th>コンポーネント</th><th>メモリの割り当て</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768 MB (以前は 512 MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144 MB (変更なし)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144 MB (変更なし)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768 MB (以前は 512 MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-X m x 512 m (以前は -X m x 410 m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536 MB (以前は 1024 MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-X m x 1024 m (以前は -X m x 819 m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768 MB (以前は 1024 MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-X m x 512 m (以前は -X m x 819 m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 MB (以前は 200 MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536 MB (変更なし)</td></tr>
</table>

HDInsight で使用される Hortonworks Data Platform で YARN および MapReduce によって使用されるメモリ構成の設定の詳細については、「 [Determine HDP Memory Configuration Settings (HDP メモリ構成の設定の決定)](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html)」をご覧ください。 Hortonworks は、適切なメモリ設定を計算するツールも提供しています。

Azure PowerShell と HDInsight SDK のエラー メッセージ: "*クラスターが HTTP サービスのアクセス用に構成されていません*" について

* このエラーは既知の[互換性の問題](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight)であり、HDInsight SDK または Azure PowerShell のバージョンとクラスターのバージョンが異なることが原因で起こることがあります。 8 月 15 日以降に作成されたクラスターは、仮想ネットワークへの新しいプロビジョニング機能をサポートします。 ただし、HDInsight SDK または Azure PowerShell の以前のバージョンでは、この機能が正しく解釈されません。 一部のジョブ送信の操作で失敗する結果になります。 HDInsight SDK API または Azure PowerShell コマンドレット (**Use-AzureRmHDInsightCluster** または **Invoke-AzureRmHDInsightHiveJob**) を使用してジョブを送信する場合、エラー メッセージ "*<clustername> が HTTP サービスのアクセス用に構成されていません*"  または操作によっては、"*クラスターに接続できません*" などのその他のエラー メッセージが表示され、これらの操作が失敗する可能性があります。
* これらの互換性の問題は、HDInsight SDK および Azure PowerShell の最新バージョンで解決されます。 HDInsight SDK をバージョン 1.3.1.6 以降に、Azure PowerShell ツールをバージョン 0.8.8 以降に更新することをお勧めします。 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) から最新の HDInsight SDK に、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」から Azure PowerShell ツールにアクセスできます。

## <a name="notes-for-9122014-release-of-hdinsight-31"></a>HDInsight 3.1 の 2014 年 9 月 12 日リリース
* このリリースは、Hortonworks Data Platform (HDP) 2.1.5 に基づくものです。 このリリースで修正されたバグの一覧については、Hortonworks のサイトで「 [Fixed in this Release (このリリースでの修正点)](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) 」ページをご覧ください。
* Pig ライブラリ フォルダーで、ファイル "avro-mapred-1.7.4.jar" が "avro-mapred-1.7.4-hadoop2.jar" に変更されています。 これらのファイルの内容には、重要でない小さなバグ修正が含まれます。 ファイルの名前が変更されたときにファイルが壊れるのを避けるため、JAR ファイルの名前と直接依存関係を持たないことをお勧めします。

## <a name="notes-for-8212014-release"></a>2014 年 8 月 21 日リリース
* 以下の WebHCat 構成 (HIVE-7155) を追加しました。 この構成は Templeton コントローラーのジョブに対し、既定のメモリ上限を 1 GB に設定します (前の既定値は 512 MB)。

     templeton.mapper.memory.mb (=1024)

  * これにより、メモリ上限が低いために特定の Hive クエリで発生する次のエラーに対処します。"コンテナーは物理メモリ上限を超過して実行しています"
  * クラスター作成時に Azure PowerShell で以下のコマンドを使用すると、この構成値を使用していない既定値の 512 に戻すことができます。

      Add-AzureRmHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}
* zookeeper ロールのホスト名が *zookeeper*に変更されました。 これはクラスター内の名前解決には影響しますが、外部の REST API には影響しません。 ホスト名 *zookeepernode* を使用するコンポーネントがある場合は、新しい名前を使用するようにそのコンポーネントを更新する必要があります。 3 つの zookeeper ノードの新しい名前は以下のとおりです。

  * zookeeper0
  * zookeeper1
  * zookeeper2
* HBase のバージョン サポート マトリックスが更新されました。 運用環境の HBase ワークロードでサポートされるのは、HDInsight バージョン 3.1 (HBase バージョン 0.98) のみです。 プレビューで利用可能だったバージョン 3.0 は、今後はサポートされなくなります。

## <a name="notes-about-clusters-created-prior-to-8152014"></a>2014 年 8 月 15 日より前に作成されたクラスターに関する留意事項
Azure PowerShell または HDInsight SDK とクラスター間でのバージョンの相違のために、Azure PowerShell または HDInsight SDK のエラー メッセージ "Cluster <clustername> is not configured for HTTP services access" または操作によっては、"Cannot connect to cluster" が表示される場合があります。 8 月 15 日以降に作成されたクラスターは、仮想ネットワークへの新しいプロビジョニング機能をサポートします。 Azure PowerShell または HDInsight SDK の以前のバージョンでは、この機能が正しく解釈されません。その結果、ジョブの送信の操作で失敗します。 HDInsight SDK API または Azure PowerShell コマンドレットを使用して Use-AzureRmHDInsightCluster や Invoke-AzureRmHDInsightHiveJob などのジョブを送信する場合、これらの操作は上に示したエラー メッセージで失敗する可能性があります。

これらの互換性の問題は、HDInsight SDK および Azure PowerShell の最新バージョンで解決されます。 HDInsight SDK をバージョン 1.3.1.6 以降に、Azure PowerShell ツールをバージョン 0.8.8 以降に更新することをお勧めします。 最新の HDInsight SDK へは [NuGet][nuget-link] からアクセスできます。 [Microsoft Web Platform インストーラー][webpi-link]から Azure PowerShell ツールにアクセスできます。

## <a name="notes-for-7282014-release"></a>2014 年 7 月 28 日リリース
* **このリージョン利用可能な HDInsight**: HDInsight の地理的なプレゼンスを以下の 3 つのリージョンに拡張しています。 HDInsight の顧客は、これらのリージョンでクラスターを作成できます。
  * 東アジア
  * 米国中北部
  * 米国中南部
* HDInsight バージョン 1.6 (HDP 1.1、Hadoop 1.0.3) と HDInsight バージョン 2.1 (HDP1.3、Hadoop 1.2) は、Azure Portal から削除されます。 これらのバージョンの Hadoop クラスターは、Azure PowerShell コマンドレット ([New-AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx)) または [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx) を使用すれば引き続き作成できます。 詳細については、「 [HDInsight で提供されるクラスター バージョンの新機能](hdinsight-component-versioning.md) 」のページを参照してください。
* このリリースでは、Hortonworks Data Platform (HDP) が以下のように変更されました。

<table border="1">
<tr><th>HDP</th><th>変更点</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>変更なし</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>変更なし</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>
</table>

## <a name="notes-for-6242014-release"></a>2014 年 6 月 24 日リリース
このリリースには、以下のように、HDInsight サービスの拡張機能が含まれています。

* **HDP 2.1 が使用可能**: HDP 2.1 が含まれる HDInsight 3.1 が一般に使用可能となり、新しいクラスターの既定のバージョンになりました。
* **HBase - Azure Portal の改良**: HBase クラスターがプレビューで使用可能になりました。 ポータルで数回クリックするだけで HBase クラスターが作成できます。

HBase を使用すると、大規模なデータセットを処理する対話的な Web サイトから、何百万ものエンド ポイントからのセンサーのデータやテレメトリ データを格納するサービスまで、さまざまなリアルタイムのワークロードを HDInsight で構築できます。 次の手順としては Hadoop ジョブでこれらのワークロードのデータを分析しますが、これは Azure PowerShell や Hive クラスター ダッシュボードを使用した HDInsight で実現可能です。

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Apache Mahout が HDInsight 3.1 にプレインストール
 HDInsight 3.1 Hadoop クラスターに [Mahout](http://hortonworks.com/hadoop/mahout/) がプレインストールされています。そのため、他のクラスター構成を追加することなく、Mahout ジョブを実行できます。 たとえば、リモート デスクトップ プロトコル (RDP) を使用して Hadoop クラスターにリモート接続したり、以下のように、追加の手順なしで Hello world Mahout コマンドを実行したりできます。

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

この手順の詳しい説明については、Apache Mahout Web サイトのドキュメント「[Breiman Example (Breiman の例)](https://mahout.apache.org/users/classification/breiman-example.html)」をご覧ください。

### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>HDInsight 3.1 で Hive クエリに Tez を使用可能
HDInsight 3.1 で Hive 0.13 が利用可能になり、Tez を使用してクエリを実行できます。これを活用してパフォーマンスを大幅に向上できます。
既定では、Hive クエリでの Tez の使用は有効ではありません。 これを使用するにはオプトインが必要です。 以下のコード スニペットを実行すると Tez を有効にできます。

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks では、Tez を使用した Hive クエリのパフォーマンスの向上について、標準ベンチマークによる詳細なブレークダウンを公開しています。 詳細については、「[Benchmarking Apache Hive 13 for Enterprise Hadoop (Enterprise Hadoop 向け Apache Hive 13 のベンチマーク)](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/)」を参照してください。

Tez を使用した Hive に関する詳細については、「 [Hive on Tez (Tez を使用した Hive)](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)」をご覧ください。

### <a name="global-availability"></a>グローバル対応
Hadoop 2.2 対応の HDInsight のリリースに伴い、Azure が利用できるすべての主要な地域で HDInsight が利用可能になりました。 具体的には、西ヨーロッパと東南アジアのデータセンターがオンラインになりました。 これで、お客様は、距離的に近く、コンプライアンス要件が似ていると思われる地域にあるデータセンター内のクラスターを見つけることができます。

### <a name="dos--donts-between-cluster-versions"></a>クラスター バージョン間でできることとできないこと
**HDInsight 3.1 クラスターで使用される Oozie メタストアは HDInsight 2.1 クラスターとの下位互換性はなく、以前のバージョンで使用できません**。

HDInsight 3.1 クラスターでデプロイされたカスタムの Oozie メタストア データベースは、HDInsight 2.1 クラスターでは再利用できません。 これは、そのメタストアが最初に HDInsight 2.1 クラスターで作成されたものであったとしても同じです。 HDInsight 3.1 クラスターで使用するときに metastore スキーマがアップグレードされるため、このシナリオはサポートされていません。そのため、HDInsight 2.1 クラスターで必要とされるメタストアとの互換性がなくなります。 HDInsight 3.1 クラスターで使用されていた Oozie メタストアを再利用しようと試みると、HDInsight 2.1 クラスターが使用できなくなります。

**Oozie メタストアはクラスター間で共有できない**

Oozie メタストアは特定のクラスターに接続されるものであり、クラスター間で共有できません。

### <a name="breaking-changes"></a>重大な変更
**プレフィックスの構文**: HDInsight 3.1 と 3.0 クラスターでは、"wasbs://" 構文のみがサポートされます。 旧バージョンの "asv://" 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.1 クラスターまたは 3.0 クラスターではサポートされません。 つまり、"asv://" 構文を明示的に使用するジョブを HDInsight 3.1 または 3.0 クラスターに送信すると、そのジョブは失敗します。 代わりに、"wasbs://" 構文を使用する必要があります。 また、既存のメタストアにより、asv:// 構文を使用してリソースへの明示的な参照を含むジョブを作成し、HDInsight 3.1 または 3.0 クラスターに送信すると、そのジョブは失敗します。 これらのメタストアは、"wasbs://" 構文を使用してリソースをアドレス指定するように再作成する必要があります。

**ポート**: HDInsight サービスで使用されるポートが変更されました。 これまで使用されてきたポート番号は、Windows オペレーティング システムの一時的なポート範囲内の番号です。 ポートは、短期間のインターネット プロトコル ベースの通信に対して一時的に事前定義される範囲から自動的に割り当てられます。 今回 Hortonworks Data Platform (HDP) サービスに許可された新しいポート番号のセットは、この範囲の外部にあり、ヘッドノードで実行するサービスが使用するポート番号の競合の可能性が回避されます。 新しいポート番号によって重大な変更が発生することはありません。 使用される番号は以下のとおりです。

 **HDInsight 1.6 (HDP 1.1)**

<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table>

 **HDInsight 3.1 および 3.0 (HDP 2.1 および 2.0)**

<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table>

### <a name="dependencies"></a>依存関係
HDInsight 3.x (HDP2.x) に以下の依存関係が追加されました。

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

HDInsight 3.x (HDP2.x) から削除された依存関係は以下のとおりです。

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

### <a name="version-changes"></a>バージョンの変更
HDInsight 2.x (HDP1.x) と HDInsight 3.x (HDP2.x) との間で以下のバージョンが変更されました。

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['
* 0.8.0.RELEASE']

### <a name="drivers"></a>ドライバー
SQL Server の Java Database Connnectivity (JDBC) ドライバーは HDInsight によって内部的に使用され、外部の操作では使用されません。 Open Database Connectivity (ODBC) を使用して HDInsight に接続する場合は、Microsoft Hive ODBC ドライバーを使用してください。 詳細については、「 [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続](hdinsight-connect-excel-hive-odbc-driver.md)」をご覧ください。

### <a name="bug-fixes"></a>バグの修正
このリリースでは、以下のバージョンの HDInsight でいくつかのバグが修正されました。

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)

## <a name="hortonworks-release-notes"></a>Hortonworks リリース ノート
各バージョンの HDInsight クラスターで使用される Hortonworks Data Platforms (HDP) のリリース ノートは、以下の場所から利用可能です。

* HDInsight バージョン 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します。 これが、2014 年 11 月 7 日以降の Azure Portal の使用時に作成される既定の Hadoop クラスターです。 2014 年 11 月 7 日より前に作成された HDInsight 3.1 クラスターは、[Hortonworks Data Platform 2.1.1][hdp-2-1-1] に基づいています。
* HDInsight バージョン 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight バージョン 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight バージョン 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/

