---
title: Azure HDInsight で Apache Ambari を使用して Apache Pig を最適化する
description: Apache Ambari Web UI を使用して、Apache Pig を構成および最適化します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 43159360342063af1682a984db037ae286721411
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871165"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight で Apache Ambari を使用して Apache Pig を最適化する

Apache Ambari は、HDInsight クラスターを管理および監視するための Web インターフェイスです。 Ambari Web UI の概要については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

Ambari Web UI から Apache Pig プロパティを変更して、Pig クエリを調整できます。 Ambari から Pig プロパティを変更すると、`/etc/pig/2.4.2.0-258.0/pig.properties` ファイルの Pig プロパティが直接変更されます。

1. Pig プロパティを変更するには、Pig の **[Configs]\(構成\)** タブに移動し、 **[Advanced pig-properties]\(高度な pig-properties\)** ウィンドウを展開します。

1. 変更するプロパティの値を見つけ、コメント解除して変更します。

1. ウィンドウの右上の **[Save]\(保存\)** をクリックして新しい値を保存します。 一部のプロパティでは、サービスの再起動が必要な場合があります。

    :::image type="content" source="./media/optimize-pig-ambari/advanced-pig-properties.png" alt-text="Advanced Apache pig プロパティ" border="true":::

> [!NOTE]  
> セッション レベルの設定によって、`pig.properties` ファイルのプロパティ値がオーバーライドされます。

## <a name="tune-execution-engine"></a>実行エンジンを調整する

Pig スクリプトを実行するために、MapReduce と Tez の 2 つの実行エンジンを使用できます。 Tez は最適化されたエンジンであり、MapReduce よりもはるかに高速です。

1. 実行エンジンを変更するには、 **[Advanced pig-properties]\(高度な pig-properties\)** ウィンドウで、`exectype` プロパティを見つけます。

1. 既定値は **MapReduce** です。 これを **Tez** に変更します。

## <a name="enable-local-mode"></a>ローカル モードを有効にする

Hive と同様に、データ量が比較的少ないジョブを高速化するにはローカル モードを使用します。

1. ローカル モードを有効にするには、`pig.auto.local.enabled` を **true** に設定します。 既定値は false です。

1. 入力データ サイズが `pig.auto.local.input.maxbytes` プロパティ値よりも小さいジョブは、小さなジョブと見なされます。 既定値は 1 GB です。

## <a name="copy-user-jar-cache"></a>ユーザー JAR キャッシュをコピーする

Pig では、UDF に必要な JAR ファイルを分散キャッシュにコピーして、タスク ノードで使用できるようにします。 これらの JAR は、頻繁に変更されることはありません。 有効にした場合、`pig.user.cache.enabled` 設定を使用して、JAR をキャッシュに配置し、同じユーザーが実行するジョブで再利用できます。 この設定により、ジョブのパフォーマンスが少し向上します。

1. 有効にするには、`pig.user.cache.enabled` を true に設定します。 既定値は false です。

1. キャッシュされた JAR のベース パスを設定するには、`pig.user.cache.location` をベース パスに設定します。 既定では、 `/tmp`です。

## <a name="optimize-performance-with-memory-settings"></a>メモリ設定を使用してパフォーマンスを最適化する

次のメモリ設定は、Pig スクリプトのパフォーマンスの最適化に役立ちます。

* `pig.cachedbag.memusage`:バッグに割り当てるメモリの最大量。 バッグとはタプルのコレクションです。 タプルとはフィールドの順序付けされたセットであり、フィールドとはデータの一部です。 バッグ内のデータが割り当てられたメモリ容量を超えた場合はディスクに書き込まれます。 既定値は 0.2 です。これは使用可能なメモリの 20% を表します。 このメモリは、アプリケーションのすべてのバッグで共有されます。

* `pig.spill.size.threshold`:この書き込みサイズのしきい値 (バイト単位) より大きいバッグがディスクに書き込まれます。 既定値は 5 MB です。

## <a name="compress-temporary-files"></a>一時ファイルを圧縮する

Pig では、ジョブの実行中に一時ファイルが生成されます。 一時ファイルを圧縮すると、ファイルの読み取り時やディスクへの書き込み時にパフォーマンスが向上します。 一時ファイルを圧縮するには、次の設定を使用します。

* `pig.tmpfilecompression`:true の場合は、一時ファイルの圧縮が有効になります。 既定値は false です。

* `pig.tmpfilecompression.codec`:一時ファイルの圧縮に使用する圧縮コーデック。 CPU 使用率を削減するために推奨される圧縮コーデックは LZO と Snappy です。

## <a name="enable-split-combining"></a>分割結合を有効にする

有効にすると、マップ タスクを減らすために小さなファイルが結合されます。 この設定により、多数の小さなファイルを使用するジョブの効率が向上します。 有効にするには、`pig.noSplitCombination` を true に設定します。 既定値は false です。

## <a name="tune-mappers"></a>マッパーを調整する

マッパーの数を制御するには、`pig.maxCombinedSplitSize` プロパティを変更します。 このプロパティは、1 つのマップ タスクで処理されるデータのサイズを指定します。 既定値は、ファイル システムの既定のブロック サイズです。 この値を増やすと、マッパー タスクの数が減少します。

## <a name="tune-reducers"></a>レジューサーを調整する

レジューサーの数は、`pig.exec.reducers.bytes.per.reducer` パラメーターに基づいて計算されます。 このパラメーターは、レジューサーごとに処理されるバイト数を指定します。既定値は 1 GB です。 レジューサーの最大数を制限するには、`pig.exec.reducers.max` プロパティを設定します。既定値は 999 です。

## <a name="next-steps"></a>次のステップ

* [Apache Ambari Web UI を使用して HDInsight クラスターを管理する](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [クラスターを最適化する](./hdinsight-changing-configs-via-ambari.md)
* [Apache HBase を最適化する](./optimize-hbase-ambari.md)
* [Apache Hive を最適化する](./optimize-hive-ambari.md)
