---
title: Apache Spark でのメモリ使用量の最適化 - Azure HDInsight
description: Azure HDInsight 上の Apache Spark で、メモリ使用量を最適化する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 056060f8b94747651c78c757150d5e5a5982c7af
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757780"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Apache Spark のメモリ使用量の最適化

この記事では、Azure HDInsight で最高のパフォーマンスが得られるように、お使いの Apache Spark クラスターのメモリ管理を最適化する方法について説明します。

## <a name="overview"></a>概要

Spark は、データをメモリに配置することで動作します。 そのため、メモリ リソースの管理は Spark ジョブの実行の最適化における重要な側面です。  クラスターのメモリを効率的に使用するために適用できる手法がいくつかあります。

* 小さいデータ パーティションを優先し、パーティション分割戦略でデータのサイズ、種類、および分散を要因とします。
* 既定の Java シリアル化ではなく、より新しくより効率的な [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) を検討します。
* YARN は `spark-submit` をバッチ単位で分割するため、YARN を優先的に使用します。
* Spark 構成設定を監視し、チューニングします。

参考までに、Spark のメモリ構造と一部の重要な実行プログラムのメモリ パラメーターを、次のイメージで示します。

## <a name="spark-memory-considerations"></a>Spark メモリに関する考慮事項

Apache Hadoop YARN を使用する場合は、YARN が Spark の各ノード上のすべてのコンテナーで使用されるメモリを制御します。  次の図は、重要なオブジェクトとそれらの関連性を示しています。

![YARN の Spark メモリの管理](./media/apache-spark-perf/apache-yarn-spark-memory.png)

''メモリ不足'' のメッセージに対処するには、次を試してください。

* DAG 管理シャッフルを確認します。 マップ側の reduce 処理、ソース データの事前パーティション分割 (またはバケット化)、1 つのシャッフルの最大化、および送信されるデータ量の削減によって削減します。
* 固定メモリが `GroupByKey` に制限された `ReduceByKey` を優先します。これは集計、ウィンドウ化、およびその他の機能を提供しますが、無制限のメモリ制限があります。
* 実行プログラムやパーティションでより多くの操作を実行する `TreeReduce` を、すべての操作をドライバーで実行する `Reduce` より優先します。
* 下位レベルの RDD オブジェクトではなく、DataFrames を使用します。
* "上位 N"、各種の集計、ウィンドウ化操作などのアクションをカプセル化する、ComplexTypes を作成します。

その他のトラブルシューティングの手順については、「[Azure HDInsight での Apache Spark の OutOfMemoryError 例外](apache-spark-troubleshoot-outofmemory.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Apache Spark のデータ処理を最適化する](optimize-cluster-configuration.md)
* [Apache Spark 用にデータ ストレージを最適化する](optimize-data-storage.md)
* [Apache Spark のクラスター構成を最適化する](optimize-cluster-configuration.md)
