---
title: Apache Spark アプリからの RequestBodyTooLarge エラー - Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge が Azure HDInsight の Apache Spark ストリーミング アプリのログに表示される
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 73ae646cb083841ee1d55b6c7ce6af7180cef08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929430"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem...RequestBodyTooLarge" が HDInsight の Apache Spark ストリーミング アプリのログに表示される

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

エラー `NativeAzureFileSystem ... RequestBodyTooLarge` が、Apache Spark ストリーミング アプリのドライバー ログに表示されます。

## <a name="cause"></a>原因

お使いの Spark イベント ログ ファイルが、WASB に対するファイルの長さの上限に達している可能性があります。

Spark 2.3 では、各 Spark アプリによって 1 つの Spark イベント ログ ファイルが生成されます。 Spark ストリーミング アプリの Spark イベント ログ ファイルは、アプリの実行中も拡張され続けます。 現在、WASB 上のファイルには 5 万ブロックの制限があり、既定のブロック サイズは 4 MB です。 そのため、既定の構成では、最大ファイル サイズは 195 GB です。 しかし Azure Storage では最大ブロック サイズが 100 MB に増えており、1 つのファイルの上限は実質的に 4.75 TB になります。 詳細については、「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../../storage/blobs/scalability-targets.md)」を参照してください。

## <a name="resolution"></a>解決方法

このエラーには、次の 3 つの解決策があります。

* ブロック サイズを最大 100 MB に増やします。 Ambari UI で、HDFS 構成プロパティ `fs.azure.write.request.size` を変更します (または、`Custom core-site` セクションにそれを作成します)。 次の例のように、33554432 など、より大きい値にプロパティを設定します。 更新した構成を保存し、影響を受けるコンポーネントを再起動します。

* Spark ストリーミング ジョブを定期的に停止して再送信します。

* HDFS を使用して Spark イベント ログを格納します。 ストレージに HDFS を使用すると、クラスターのスケーリングまたは Azure のアップグレード中に、Spark のイベント データが失われる可能性があります。

    1. Ambari UI で `spark.eventlog.dir` および `spark.history.fs.logDirectory` を変更します。

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. HDFS にディレクトリを作成します。

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Ambari UI を使用して、影響を受けるすべてのサービスを再起動します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]