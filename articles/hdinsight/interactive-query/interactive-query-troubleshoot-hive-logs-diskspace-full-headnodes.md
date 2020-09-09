---
title: Apache Hive ログによってディスク領域がいっぱいになる - Azure HDInsight
description: Apache Hive ログによって Azure HDInsight のヘッド ノードのディスク領域がいっぱいになる。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943960"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>シナリオ:Apache Hive ログによって Azure HDInsight のヘッド ノードのディスク領域がいっぱいになる

この記事では、Azure HDInsight クラスターのヘッド ノードのディスク領域不足に関連する問題のトラブルシューティングの手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive/LLAP クラスターで、不要なログによってヘッド ノードのディスク領域全体が占有されている。 そのため、次の問題が発生する可能性がある。

1. ヘッド ノードに空き領域がないため、SSH アクセスが失敗する。
2. Ambari によって "*HTTP エラー:503 サービス利用不可*" が返される。

この問題が発生すると、`ambari-agent` ログに次のように表示されます。
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

Hive-log4j の詳細構成で、パラメーター *log4j.appender.RFA.MaxBackupIndex* が省略されています。 これにより、ログ ファイルが無限に生成されます。

## <a name="resolution"></a>解決策

1. Ambari ポータルの Hive コンポーネントの概要に移動し、[`Configs`] タブをクリックします。

2. [Advanced]\(詳細\) 設定の `Advanced hive-log4j` セクションに移動します。

3. `log4j.appender.RFA` パラメーターを RollingFileAppender に設定します。 

4. `log4j.appender.RFA.MaxFileSize` と `log4j.appender.RFA.MaxBackupIndex` を次のように設定します。

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. 次に示すように、`hive.root.logger` を `INFO,RFA` に設定します。 既定の設定は DEBUG で、これによりログが非常に大きくなります。

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. 構成を保存し、必要なコンポーネントを再起動します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
