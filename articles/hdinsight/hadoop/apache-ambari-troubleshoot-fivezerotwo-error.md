---
title: Azure HDInsight における Apache Ambari UI の 502 エラー
description: Azure HDInsight クラスターにアクセスしようとした場合の Apache Ambari UI 502 エラー
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 750232b19bd5ef0674a9df79fdf3972a679eda7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946781"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>シナリオ: Azure HDInsight における Apache Ambari UI の 502 エラー

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDInsight クラスターの Apache Ambari UI にアクセスしようとすると、次のようなメッセージが表示される: "502 - Web server received an invalid response while acting as a gateway or proxy server." (502 - Web サーバーがゲートウェイまたはプロキシ サーバーとして動作しているときに、無効な応答を受信しました。)

## <a name="cause"></a>原因

一般に、HTTP 502 状態コードは、アクティブなヘッドノードで Ambari サーバーが正常に実行されていないことを意味します。 いくつかの根本原因が考えられます。

## <a name="resolution"></a>解決方法

ほとんどの場合、問題を軽減するには、アクティブなヘッドノードを再起動します。 または、ヘッドノードにより大きな VM サイズを選択します。

### <a name="ambari-server-failed-to-start"></a>Ambari サーバーを起動できない

ambari-server ログを調べて、Ambari サーバーの起動に失敗した理由を確認できます。 一般的な理由の 1 つは、データベースの整合性チェック エラーです。 次のログ ファイルでこれを確認できます: `/var/log/ambari-server/ambari-server-check-database.log`。

クラスター ノードに変更を加えた場合は、元に戻してください。 Hadoop または Spark に関連する構成を変更する場合は、必ず Ambari UI を使用します。

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari サーバーの CPU 使用率が 100% になる

まれに ambari-server プロセスの CPU 使用率が常時 100% 近くになるケースが確認されています。 軽減策として、アクティブなヘッドノードに ssh 接続し、Ambari サーバー プロセスを中止してから再度起動します。

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari サーバーが oom-killer によって中止される

一部のシナリオでは、ヘッドノードのメモリが不足し、Linux の oom-killer が、中止するプロセスを選択し始めます。 この状況は AmbariServer プロセスの ID を検索することで確認できます。この ID は見つからないはずです。 次に、`/var/log/syslog` を見て、次のようなものを探します。

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

次に、メモリを大量に使用しているプロセスを特定し、その根本原因を突き止めます。

### <a name="other-issues-with-ambari-server"></a>Ambari サーバーに関するその他の問題

まれに、Ambari サーバーが受信要求を処理できないことがあります。ambari-server ログでエラーを調べて、詳細情報を見つけることができます。 このようなケースの 1 つは、次のようなエラーです。

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]