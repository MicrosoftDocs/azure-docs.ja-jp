---
title: Azure HDInsight クラスターの適切な VM サイズを選択する方法
description: Azure HDInsight クラスターの適切な VM サイズを選択する方法について説明します。
keywords: VM のサイズ、クラスターのサイズ、クラスター構成
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931780"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Azure HDInsight クラスターの適切な VM サイズの選択

この記事では、HDInsight クラスター内のさまざまなノードに適した VM サイズを選択する方法について説明します。 

まず、CPU 処理、RAM サイズ、ネットワーク待機時間などの仮想マシンの特性が、ワークロードの処理にどのように影響するかを理解します。 次に、アプリケーションについて考えます。使用するアプリケーションが、さまざまな VM ファミリの最適化対象にどれだけ合致しているかを検討します。 使用する予定の VM ファミリが、デプロイする予定のクラスターの種類と互換性があることを確認します。 クラスターの種類ごとの、サポート対象で推奨されるすべての VM サイズの一覧については、[Azure HDInsight でサポートされるノード構成](hdinsight-supported-node-configuration.md)に関するページを参照してください。 最後に、ベンチマーク プロセスを使用していくつかのサンプル ワークロードをテストし、そのファミリ内のどの SKU が適しているかを調べます。

ストレージの種類やクラスター サイズの選択など、クラスターのその他の側面に関する計画の詳細については、「[HDInsight クラスターの容量計画](hdinsight-capacity-planning.md)」を参照してください。

## <a name="vm-properties-and-big-data-workloads"></a>VM の特性とビッグ データのワークロード

VM のサイズと種類は、CPU の処理能力、RAM サイズ、ネットワーク待ち時間によって決まります。

- CPU: VM サイズによってコア数が決まります。 コア数が増えるほど、各ノードで実現できる並列計算の度合いが上がります。 また、VM の一部の種類は高速コアを備えています。

- RAM: VM サイズによって、VM で使用可能な RAM の容量も決まります。 処理するデータをディスクから読み取るのではなく、メモリに格納するワークロードの場合、ワーカー ノードにデータを格納できる十分なメモリがあることを確認します。

- ネットワーク: ほとんどの種類のクラスターでは、そのクラスターで処理されるデータは、ローカル ディスク上に存在するのではなく、Data Lake Storage や Azure Storage などの外部ストレージ サービスに存在します。 ノードの VM とストレージ サービス間のネットワーク帯域幅とスループットを考慮します。 通常は、VM のサイズが大きいほど、VM が使用できるネットワーク帯域幅が増加します。 詳細については、[VM サイズの概要](../virtual-machines/sizes.md)に関する記事をご覧ください。

## <a name="understanding-vm-optimization"></a>VM の最適化について

Azure の仮想マシン ファミリは、さまざまなユース ケースに適合するよう最適化されています。 次の表で、最も一般的なユース ケースの一部と、それに合致する VM ファミリを確認することができます。

| Type                     | サイズ           |    説明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [エントリ レベル](../virtual-machines/sizes-general.md)          | A、Av2  | 開発とテストのような、エントリ レベルのワークロードに最適な CPU パフォーマンスとメモリ構成を備えています。 A シリーズは経済的で、Azure の使用を開始するための低コストなオプションとなっています。 |
| [汎用](../virtual-machines/sizes-general.md)          | D、DSv2、Dv2  | バランスのとれた CPU 対メモリ比。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。 |
| [コンピューティング最適化](../virtual-machines/sizes-compute.md)        | F           | 高い CPU 対メモリ比。 トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリケーション サーバーに適しています。        |
| [メモリの最適化](../virtual-machines/sizes-memory.md)         | Esv3、Ev3  | 高いメモリ対 CPU 比。 リレーショナル データベース サーバー、中～大規模のキャッシュ、およびメモリ内分析に適しています。                 |

- HDInsight がサポートされるリージョンで提供されている VM インスタンスの価格については、[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)に関するページを参照してください。

## <a name="cost-saving-vm-types-for-light-workloads"></a>軽いワークロードに向けたコスト節約型の VM の種類

処理要件が厳しくない場合は、[F シリーズ](https://azure.microsoft.com/blog/f-series-vm-size/)が、HDInsight を使い始めるのに適した選択肢となる可能性があります。 F シリーズは時間あたりの料金が抑えられており、vCPU あたりの Azure コンピューティング ユニット (ACU) に基づく Azure ポートフォリオにおいて、最もコスト パフォーマンスに優れています。

次の表では、Fsv2 シリーズの VM で作成できるクラスターの種類とノードの種類について説明します。

| クラスターの種類 | Version | ワーカー ノード | ヘッド ノード | Zookeeper ノード |
|---|---|---|---|---|
| Spark | All | F4 以上 | no | no |
| Hadoop | All | F4 以上 | no | no |
| Kafka | All | F4 以上 | no | no |
| hbase | All | F4 以上 | no | no |
| LLAP | disabled | no | no | no |
| Storm | disabled | no | no | no |
| ML Service | HDI 3.6 のみ | F4 以上 | no | no |

F シリーズの各 SKU の仕様を確認するには、[F シリーズの VM サイズ](https://azure.microsoft.com/blog/f-series-vm-size/)に関するページを参照してください。

## <a name="benchmarking"></a>ベンチマーク

ベンチマークは、シミュレートされたワークロードをさまざまな VM で実行し、運用環境のワークロードに対してどれだけのパフォーマンスが発揮されるかを測定するプロセスです。 

VM SKU とクラスター サイズのベンチマークの詳細については、[Azure HDInsight のクラスターの容量計画](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure HDInsight でサポートされるノード構成](hdinsight-supported-node-configuration.md)
- [Azure の Linux 仮想マシンのサイズ](../virtual-machines/sizes.md)