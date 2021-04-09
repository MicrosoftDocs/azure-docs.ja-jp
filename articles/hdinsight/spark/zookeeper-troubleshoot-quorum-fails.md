---
title: Apache ZooKeeper サーバーが Azure HDInsight にクォーラムを形成できない
description: Apache ZooKeeper サーバーが Azure HDInsight にクォーラムを形成できない
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929210"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper サーバーが Azure HDInsight にクォーラムを形成できない

この記事では、Azure HDInsight クラスターの Zookeeper に関連する問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="symptoms"></a>現象

* 両方のリソース マネージャーがスタンバイ モードになる
* 両方の Namenode がスタンバイ モードになる
* Zookeeper 接続の失敗のために Spark、Hive、および Yarn ジョブまたは Hive クエリが失敗する
* セキュリティで保護された Spark クラスターまたはインタラクティブ Hive クラスターで LLAP デーモンを起動できない

## <a name="sample-log"></a>サンプル ログ

YARN ログ (ヘッドノートに /var/log/hadoop-yarn/yarn/yarn-yarn*.log が付く) に、次のようなエラー メッセージが表示される場合があります。

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>関連した問題

* Yarn、NameNode、Livy などの高可用性サービスは、さまざまな理由でダウンする可能性があります。
* ログで Zookeeper 接続に関連していることを確認します
* 問題が繰り返し発生することを確認します (1 回限りの問題に、以下のソリューションを使用しないでください)
* Zookeeper 接続の問題で、一時的にジョブが失敗する場合があります

## <a name="common-causes-for-zookeeper-failure"></a>Zookeeper エラーの一般的な原因

* zookeeper サーバーでの高い CPU 使用率
  * Ambari UI で、zookeeper サーバーの CPU 使用率が 100% 近くで持続する場合は、その間に開かれている zookeeper セッションが有効期限切れになり、タイムアウトすることがあります
* Zookeeper クライアントが頻繁にタイムアウトをレポートしている
  * Resource Manager、Namenode などのログでは、クライアント接続タイムアウトが頻繁に発生します
  * これにより、クォーラムの損失、頻繁なフェールオーバー、およびその他の問題が発生する可能性があります

## <a name="check-for-zookeeper-status"></a>zookeeper の状態の確認

* /etc/hosts ファイルまたは Ambari UI で zookeeper サーバーを探します
* 次のコマンドを実行します。
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (または 2182)  
  * ポート 2181 は、apache zookeeper インスタンスです
  * ポート 2182 は、HDInsight zookeeper によって使用されます (ネイティブに HA ではないサービスを HA に提供するため)
  * コマンドの出力が表示されない場合は、zookeeper サーバーが実行されていないことを意味します
  * サーバーが実行されている場合、結果にはクライアント接続の統計とその他の統計が含まれます

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>CPU 負荷が毎時間ピークに達する

* zookeeper サーバーにログインし、/etc/crontab を確認します
* この時間に実行されている毎時のジョブがある場合は、異なる zookeeper サーバー間で開始時刻をランダムにします。
  
## <a name="purging-old-snapshots"></a>古いスナップショットの消去

* Zookeeper は、古いスナップショットを自動的に消去するように構成されています
* 既定では、直近の 30 個のスナップショットが保持されます
* 保持されるスナップショットの数は、構成キー `autopurge.snapRetainCount` によって制御されます。 このプロパティは、以下のファイルで確認できます。
  * Hadoop zookeeper の場合は `/etc/zookeeper/conf/zoo.cfg`
  * HDInsight zookeeper の場合は `/etc/hdinsight-zookeeper/conf/zoo.cfg`
* `autopurge.snapRetainCount` の値を 3 に設定し、zookeeper サーバーを再起動します
  * Hadoop zookeeper の構成は更新でき、サービスは Ambari を通じて再起動できます
  * HDInsight zookeeper は手動で停止し、再起動します
    * `sudo lsof -i :2182` で、中止するプロセス ID を取得します
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* スナップショットを手動で消去しないでください。手動でスナップショットを削除すると、データが失われる可能性があります

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>zookeeper サーバー ログの CancelledKeyException には、スナップショットのクリーンアップは必要ありません

* この例外は、zookeeper サーバー (/var/log/zookeeper/zookeeper-zookeeper-* または /var/log/hdinsight-zookeeper/zookeeper* ファイル) に表示されます
* この例外は通常、クライアントがアクティブでなくなり、サーバーがメッセージを送信できないことを意味します
* また、zookeeper クライアントがセッションを途中で終了していることも示しています
* このドキュメントに記載されている他の症状を探します

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。
- [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。
- さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。