---
title: Azure HDInsight での Apache Ambari のハートビートの問題
description: Azure HDInsight での Apache Ambari のハートビートの問題のさまざまな理由のレビュー
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946728"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight での Apache Ambari のハートビートの問題

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="scenario-high-cpu-utilization"></a>シナリオ: CPU の使用率が高い

### <a name="issue"></a>問題

Ambari エージェントの CPU 使用率が高くなっています。これにより、一部のノードで Ambari エージェントのハートビートが失われたことを示すアラートが Ambari UI から出されています。 ハードビートが失われたというアラートは通常、一時的なものです。

### <a name="cause"></a>原因

さまざまな Ambari エージェントのバグが原因で、まれに、Ambari エージェントの CPU 使用率が高く (100 近くに) なる場合があります。

### <a name="resolution"></a>解決方法

1. Ambari エージェントのプロセス ID (pid) を識別します。

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 次のコマンドを実行して、CPU の使用率を表示します。

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Ambari エージェントを再起動して、問題を軽減します。

    ```bash
    service ambari-agent restart
    ```

1. 再起動しても効果がない場合は、Ambari エージェント プロセスを強制終了してから開始してください。

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>シナリオ: Ambari エージェントが開始されていない

### <a name="issue"></a>問題

Ambari エージェントが開始されていません。これにより、一部のノードで Ambari エージェントのハートビートが失われたことを示すアラートが Ambari UI から出ています。

### <a name="cause"></a>原因

アラートは、Ambari エージェントが実行されていないことが原因で発生します。

### <a name="resolution"></a>解決方法

1. Ambari エージェントの状態を確認します。

    ```bash
    service ambari-agent status
    ```

1. フェールオーバー コントローラー サービスが実行されているかどうかを確認します。

    ```bash
    ps -ef | grep failover
    ```

    フェールオーバー コントローラー サービスが実行されていない場合は、問題によって、HDInsight エージェントがフェールオーバー コントローラーを起動できないことが原因の可能性があります。 `/var/log/hdinsight-agent/hdinsight-agent.out` ファイルから HDInsight エージェントのログを確認してください。

## <a name="scenario-heartbeat-lost-for-ambari"></a>シナリオ: Ambari のハートビートが失われた

### <a name="issue"></a>問題

Ambari のハートビート エージェントが失われました。

### <a name="cause"></a>原因

OMS ログで CPU 使用率が高くなっています。

### <a name="resolution"></a>解決方法

* [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell コマンドレットを使用して Azure Monitor ログ記録を無効にします。
* `mdsd.warn` ログ ファイルを削除します

---

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]