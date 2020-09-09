---
title: Apache Spark Thrift での RpcTimeoutException - Azure HDInsight
description: Apache Spark Thrift サーバーを使用して大規模なデータ セットを処理すると、502 エラーが発生する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: a29d36c5ba6fdd51de27afa3ab4dfe1258332200
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208418"
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

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
