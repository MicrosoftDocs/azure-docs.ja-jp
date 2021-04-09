---
title: Azure HDInsight で WASB ファイル操作をデバッグする
description: Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938737"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Azure HDInsight で WASB ファイル操作をデバッグする

場合によっては、WASB ドライバーによって Azure Storage でどのような処理が開始されたかを理解する必要があります。 クライアント側では、WASB ドライバーから **DEBUG** レベルで各ファイル システム操作のログが生成されます。 WASB ドライバーでは、ログ レベルの制御に log4j が使用されます。既定値は **INFO** レベルです。 Azure Storage サーバー側の分析ログについては、「[Azure Storage Analytics のログ](../../storage/common/storage-analytics-logging.md)」を参照してください。

生成されるログは次のようになります。

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>ファイル操作に関する WASB デバッグ ログを有効にする

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` に移動します。ここで、`CLUSTERNAME` は実際の Spark クラスターの名前です。

1. **[advanced spark2-log4j-properties]\(高度な spark2-log4j-properties\)** に移動します。

    1. `log4j.appender.console.Threshold=INFO` を `log4j.appender.console.Threshold=DEBUG` に変更します。

    1. `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`を追加します。

1. **[Advanced livy2-log4j-properties]\(高度な livy2-log4j-properties\)** に移動します。

    `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`を追加します。

1. 変更を保存します。

## <a name="additional-logging"></a>追加ログ

上記のログを使用すると、ファイル システムの操作に関する概要を把握できます。 上記のログに有益な情報がない場合、または BLOB ストレージの API 呼び出しを調査する場合は、`fs.azure.storage.client.logging=true` を `core-site` に追加します。 この設定により、wasb ストレージ ドライバーの Java SDK ログが有効になり、BLOB ストレージ サーバーへの各呼び出しが出力されます。 ディスクがすぐにいっぱいになり、プロセスの速度が低下する可能性があるため、調査後に設定を削除してください。

バックエンドが Azure Data Lake ベースの場合、コンポーネントに次の log4j 設定を使用します (たとえば、spark/tez/hdfs)。

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

ログの `/var/log/adl/adl.log` でログを探します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。