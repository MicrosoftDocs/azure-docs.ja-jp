---
title: Apache Hive での結合による OutOfMemory エラー - Azure HDInsight
description: OutOfMemory エラー "GC オーバーヘッド制限超過エラー" の処理
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0c396cde38d8cba8e1f3eaf8527429647868a0c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935953"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>シナリオ: Apache Hive での結合により Azure HDInsight で OutOfMemory エラーが発生する

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive 結合の既定の動作では、Map/Reduce 手順を行うことなく結合を実行できるように、テーブルの内容全体がメモリに読み込まれます。 Hive テーブルが大きすぎてメモリに収まりきらない場合、クエリは失敗する可能性があります。

## <a name="cause"></a>原因

十分なサイズの Hive で結合を実行すると、次のエラーが発生します。

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>解決方法

次の Hive 構成値を設定することにより、結合時に Hive で (Map/Reduce 手順を行う代わりに) テーブルがメモリに読み込まれないようにします。

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>次のステップ

この値を設定しても問題が解決しない場合は、次のいずれかにアクセスしてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。