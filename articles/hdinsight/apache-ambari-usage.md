---
title: Azure HDInsight での Apache Ambari の使用法
description: Azure HDInsight での Apache Ambari の使用方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2021
ms.openlocfilehash: 6233c42d7fbf8dc7821d26f77171c44485fb8d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946935"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight での Apache Ambari の使用法

HDInsight は、クラスターのデプロイと管理に Apache Ambari を使用します。 Ambari エージェントは、ヘッド ノード、ワーカー ノード、Zookeeper、エッジ ノード (存在する場合) といったすべてのノードで実行されます。 Ambari サーバーは、ヘッド ノード (hn0 または hn1) でのみ実行されます。 Ambari サーバーのインスタンスは、一度に 1 つしか実行できません。 これは、HDInsight フェールオーバー コントローラーによって制御されます。 ヘッド ノードの 1 つが再起動またはメンテナンスのために停止している場合は、もう 1 つのヘッド ノードがアクティブになり、2 番目のヘッド ノードの Ambari サーバーが開始されます。

すべてのクラスター構成は、[Ambari UI](./hdinsight-hadoop-manage-ambari.md) を通じて実行する必要があります。ローカルの変更はすべて、ノードの再起動時に上書きされます。

## <a name="failover-controller-services"></a>フェールオーバー コントローラー サービス

HDInsight フェールオーバー コントローラーは、現在のアクティブ ヘッド ノードを指す、ヘッド ノード ホストの IP アドレスを更新する役割も担います。 すべての Ambari エージェントは、その状態とハートビートをヘッド ノード ホストに報告するように構成されています。 フェールオーバー コントローラーは、クラスター内のすべてのノードで実行されている一連のサービスです。それらが実行されていない場合、ヘッド ノードのフェールオーバーが正しく機能しない可能性があり、Ambari サーバーにアクセスしようとすると HTTP 502 が発生します。

どのヘッド ノードがアクティブかを確認するための 1 つの方法としては、クラスター内のいずれかのノードに SSH 接続し、`ping headnodehost` を実行して、IP を 2 つのヘッド ノードのものと比較します。

フェールオーバー コントローラー サービスが実行されていない場合は、ヘッド ノードのフェールオーバーが正しく行われない可能性があり、それにより、Ambari サーバーが実行されない可能性があります。 フェールオーバー コントローラー サービスが実行されているかどうかを確認するには、次を実行します。

```bash
ps -ef | grep failover
```

## <a name="logs"></a>ログ

アクティブなヘッド ノードでは、Ambari サーバーのログを次の場所で確認できます。

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

クラスター内のどのノードでも、Ambari エージェントのログは次の場所で確認できます。

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>サービス開始シーケンス

起動時のサービス開始の順序は次のとおりです。

1. Hdinsight エージェントが、フェールオーバー コントローラー サービスを開始します。
1. フェールオーバー コントローラー サービスが、アクティブなヘッド ノード上のすべてのノードと Ambari サーバーで Ambari エージェントを開始します。

## <a name="ambari-database"></a>Ambari データベース

HDInsight は、Ambari サーバーのデータベースとして機能するデータベースを SQL Database 内部で作成します。 既定の[サービス レベルは S0](../azure-sql/database/elastic-pool-scale.md) です。

クラスターを作成するときにワーカー ノードの数が 16 を超えるクラスターの場合は、S2 がデータベースのサービス レベルです。

## <a name="takeaway-points"></a>要点

問題を回避するためにサービスを再起動しようとしている場合を除き、ambari-server または ambari-agent サービスを手動で開始/停止しないでください。 フェールオーバーを強制するには、アクティブなヘッド ノードを再起動します。

クラスター ノード上の構成ファイルはいずれも手動で変更しないでください。その操作は、Ambari UI が自動的に実行します。

## <a name="property-values-in-esp-clusters"></a>ESP クラスターのプロパティ値

HDInsight 4.0 Enterprise セキュリティ パッケージ クラスターでは、変数の区切り記号としてコンマではなくパイプ (`|`) を使用します。 例を次に示します。

```
Property Key: hive.security.authorization.sqlstd.confwhitelist.append
Property Value: environment|env|dl_data_dt
```

## <a name="next-steps"></a>次のステップ

* [Apache Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
