---
title: Apache HBase REST が Azure HDInsight の要求に応答しない
description: 要求に応答しない HDInsight HBase REST の問題を解決する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 7219f66457e47bba34e750ec74810b8d2edee36e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816894"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>シナリオ: Apache HBase REST が Azure HDInsight の要求に応答しない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache HBase REST サービスが Azure HDInsight の要求に応答しません。

## <a name="cause"></a>原因

ここで考えられる原因は、Apache HBase REST サービスがソケットをリークしている可能性があります。これは、サービスが長時間 (たとえば、数か月間) 実行されている場合に特に一般的です。 クライアント SDK からは、次のようなエラー メッセージが表示されることがあります。

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>解決策

ホストに SSH で接続した後、次のコマンドを使用して HBase REST を再起動します。 また、スクリプト アクションを使用して、すべてのワーカー ノード上でこのサービスを再開することもできます。

```bash
sudo service hdinsight-hbrest restart
```

このコマンドで、同じホスト上の HBase Region Server が停止されます。 Ambari を使用して HBase Region Server を手動で開始するか、Ambari の自動再起動機能を使用して HBase Region Server を自動的に復旧することができます。

問題が引き続き発生する場合は、すべてのワーカー ノードで 5 分ごとに実行される CRON ジョブとして、次の軽減スクリプトをインストールできます。 この軽減スクリプトは REST サービスに対して ping を実行し、REST サービスが応答しない場合は再起動します。

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure のサポート リクエストを作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページを参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
