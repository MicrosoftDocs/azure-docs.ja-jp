---
title: トラブルシューティング:Apache Hive ログによってディスク領域がいっぱいになる - Azure HDInsight
description: この記事では、Apache Hive ログによって Azure HDInsight 内のヘッド ノード上のディスク領域がいっぱいになったときのトラブルシューティング手順について説明します。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930952"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>シナリオ:Apache Hive ログによって Azure HDInsight のヘッド ノードのディスク領域がいっぱいになる

この記事では、Azure HDInsight クラスターのヘッド ノードのディスク領域不足に関連する問題のトラブルシューティングの手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive/LLAP クラスターで、不要なログによってヘッド ノードのディスク領域全体が占有されている。 この状態により、次の問題が発生する可能性があります。

- ヘッド ノードに空き領域がないため、SSH アクセスが失敗する。
- Ambari によってスローされた "*HTTP エラー: 503 サービス利用不可*" が返される。
- HiveServer2 Interactive が再起動に失敗する

問題が発生すると、`ambari-agent` のログに次のエントリが含まれます。
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

高度な Hive log4j 構成の現在の既定の削除スケジュールでは、最後に変更された日付に基づき、30 日以上経過しているファイルは削除されます。

## <a name="resolution"></a>解像度

1. Ambari ポータルの Hive コンポーネントの概要に移動し、 **[Configs]\(構成\)** タブを選択します。

2. **[Advanced settings]\(高度な設定\)** の [`Advanced hive-log4j`]\(高度な hive-log4j\) セクションに移動します。

3. `appender.RFA.strategy.action.condition.age` パラメーターを任意の期間に設定します。 この例では、期間を 14 日に設定します: `appender.RFA.strategy.action.condition.age = 14D`

4. 関連する設定が表示されない場合は、次の設定を追加します。
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. 次の例に示すように、`hive.root.logger` を `INFO,RFA` に設定します。 既定の設定は `DEBUG` で、ログが大きくなります。

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. 構成を保存し、必要なコンポーネントを再起動します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
