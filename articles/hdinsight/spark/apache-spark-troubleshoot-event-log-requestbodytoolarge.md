---
title: Azure HDInsight で Apache Spark ストリーミング アプリのログに表示される RequestBodyTooLarge
description: NativeAzureFileSystem ... RequestBodyTooLarge が Azure HDInsight の Apache Spark ストリーミング アプリのログに表示される
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b6e6d3eeff8569c8b00ac16310da3c94e484b32f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088701"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem...RequestBodyTooLarge" が HDInsight の Apache Spark ストリーミング アプリのログに表示される

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

エラー `NativeAzureFileSystem ... RequestBodyTooLarge` が、Apache Spark ストリーミング アプリのドライバー ログに表示されます。

## <a name="cause"></a>原因

お使いの Spark イベント ログ ファイルが、WASB に対するファイルの長さの上限に達している可能性があります。

Spark 2.3 では、各 Spark アプリによって 1 つの Spark イベント ログ ファイルが生成されます。 Spark ストリーミング アプリの Spark イベント ログ ファイルは、アプリの実行中も拡張され続けます。 現在、WASB 上のファイルには 5 万ブロックの制限があり、既定のブロック サイズは 4 MB です。 そのため、既定の構成では、最大ファイル サイズは 195 GB です。 ただし、Azure Storage では最大ブロック サイズが 100 MB に増やされており、1 つのファイルの上限は実質的に 4.75 TB になります。 詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)」をご覧ください。

## <a name="resolution"></a>解決策

このエラーには、次の 3 つの解決策があります。

* ブロック サイズを最大 100 MB に増やします。 Ambari UI で、HDFS 構成プロパティ `fs.azure.write.request.size` を変更します (または、`Custom core-site` セクションにそれを作成します)。 次の例のように、プロパティをより大きな値に設定します: 33554432。 更新した構成を保存し、影響を受けるコンポーネントを再起動します。

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

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
