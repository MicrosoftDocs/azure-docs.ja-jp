---
title: Apache Spark Thrift での RpcTimeoutException - Azure HDInsight
description: Apache Spark Thrift サーバーを使用して大規模なデータ セットを処理すると、502 エラーが発生する
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932709"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>シナリオ:Azure HDInsight での Apache Spark Thrift サーバーの RpcTimeoutException

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

Spark アプリケーションは、次の例に示すように、`org.apache.spark.rpc.RpcTimeoutException` 例外とメッセージ (`Futures timed out`) で失敗します。

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` および `overhead limit exceeded` のエラーは、次の例のように、`sparkthriftdriver.log` にも表示されることがあります。

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

これらのエラーは、データ処理中のメモリ リソースの不足により発生します。 Java ガベージ コレクション プロセスが開始すると、Spark アプリケーションが応答しなくなる可能性があります。 クエリはタイムアウトし始め、処理を停止します。 `Futures timed out` エラーは、クラスターの負荷が重大であることを示しています。

## <a name="resolution"></a>解決方法

ワーカー ノードを追加するか、既存のクラスター ノードのメモリ容量を増やすことで、クラスターのサイズを大きくします。 データ パイプラインを調整して、一度に処理されるデータの量を減らすこともできます。

`spark.network.timeout` は、すべてのネットワーク接続のタイムアウトを制御します。 ネットワーク タイムアウトを増やすと、重要な操作を完了するために使用できる時間を増やすことができますが、問題が完全には解決されません。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]