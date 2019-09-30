---
title: JDBC/ODBC および Apache Thrift フレームワークの使用時のダウンロードの問題 - Azure HDInsight
description: Azure HDInsight で JDBC/ODBC および Apache Thrift ソフトウェア フレームワークを使用して大規模なデータ セットをダウンロードできない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 939d94d0fdf07a67f2ad151b45494f03ce184d62
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087107"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>HDInsight で JDBC/ODBC および Apache Thrift ソフトウェア フレームワークを使用して大規模なデータ セットをダウンロードできない

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

Azure HDInsight で JDBC/ODBC および Apache Thrift ソフトウェア フレームワークを使用して大規模なデータ セットをダウンロードしようとすると、次のようなエラー メッセージが表示されます。

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>原因

この例外は、シリアル化プロセスで、許可されているよりも多くのバッファー領域を使用しようとした場合に発生します。 Spark 2.0.0 では、Apache Thrift ソフトウェア フレームワークを使用してデータにアクセスするときに、`org.apache.spark.serializer.KryoSerializer` クラスを使用してオブジェクトをシリアル化します。 ネットワーク経由で送信されるデータ、またはシリアル化された形式でキャッシュされるデータには、別のクラスが使用されます。

## <a name="resolution"></a>解決策

バッファー値 `Kryoserializer` を増やします。 `spark.kryoserializer.buffer.max` という名前のキーを追加して、`Custom spark2-thrift-sparkconf` の下の spark2 構成の `2048` に設定します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
