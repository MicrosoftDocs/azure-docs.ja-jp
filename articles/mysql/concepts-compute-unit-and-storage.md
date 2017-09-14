---
title: "Azure Database for MySQL のコンピューティング ユニットについて | Microsoft Docs"
description: "Azure DB for MySQL: この記事では、コンピューティング ユニットの概念と、ワークロードが最大コンピューティング ユニットに達した場合に何が起こるかについて説明します。"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/23/2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: a82c283df688d36cd284973312e276f30ed893c0
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Azure Database for MySQL のコンピューティング ユニットについて
この記事では、コンピューティング ユニットの概念と、ワークロードが最大コンピューティング ユニットに達した場合に何が起こるかについて説明します。

## <a name="what-are-compute-units"></a>コンピューティング ユニットとは
コンピューティング ユニットは、単一の Azure Database for MySQL サーバーで使用できることが保証される CPU 処理スループットの測定値で、 CPU とメモリ リソースを組み合わせた測定値です。 一般に、50 個のコンピューティング ユニットは、コアの半分と同等です。 100 個のコンピューティング ユニットは、1 つのコアと同等です。 2000 個のコンピューティング ユニットは、サーバーで使用可能な保証された処理スループットの 20 個のコアと同等です。

コンピューティング ユニットあたりのメモリ量は、Basic および Standard 価格レベル用に最適化されています。 パフォーマンス レベルを上げてコンピューティング ユニットを 2 倍にすると、その単一の Azure Database for MySQL が利用できるリソース セットを 2 倍にした場合と同じ効果が得られます

たとえば、Standard 800 コンピューティング ユニットでは、Standard 100 コンピューティング ユニット構成よりも 8 倍の CPU スループットとメモリを実現します。 ただし、Standard 100 コンピューティング ユニットの CPU スループットは、Basic 100 コンピューティング ユニットと同じですが、Standard 価格レベルで事前構成されたメモリ量は、Basic 価格レベルで構成されているメモリ量の 2 倍になります。 そのため、Standard 価格レベルでは、同じコンピューティング ユニットが選択された Basic 価格レベルより、ワークロードのパフォーマンスが向上し、トランザクションの待ち時間が減少します。

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>ワークロードで必要とされるコンピューティング ユニットの数を判断する方法
オンプレミスまたは仮想マシンで実行されている既存の MySQL サーバーを移行することを検討している場合、コンピューティング ユニットの数を判断するには、ワークロードに必要な処理スループットのコア数を見積もります。 

既存のオンプレミスまたは仮想マシン サーバーが現在使用しているのが 4 コアの場合は (CPU のハイパースレッド数を除く)、まず Azure Database for MySQL サーバー用に 400 コンピューティング ユニットを構成します。 コンピューティング ユニットのスケールアップとスケールダウンは、ワークロードのニーズに応じて動的に行うことができ、事実上、アプリケーションのダウンタイムは発生しません。 

Azure Portal でメトリック グラフを監視するか、コンピューティング ユニットを測定する Azure CLI コマンドを記述します。 監視する関連メトリックは、コンピューティング ユニットの割合とコンピューティング ユニットの制限です。

>[!IMPORTANT]
> 記憶域 IOPS が最大まで完全に利用されていないことがわかった場合は、コンピューティング ユニット使用率の監視も検討してください。 コンピューティング ユニットを増やすと、CPU またはメモリの制限により、パフォーマンスのボトルネックが少なくなるため、IO スループットが向上する可能性があります。

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>コンピューティング ユニットが最大数に達した場合に起こること
選択した価格レベルとパフォーマンス レベルの最大限度までデータベース ワークロードを実行するように、パフォーマンス レベルが調整、制御され、リソースが提供されます。 

ワークロードがコンピューティング ユニットまたはプロビジョニング済み IOPS のいずれかの上限に達した場合、許可される最大レベルでリソースを引き続き利用できますが、クエリの待ち時間が長くなる場合があります。 上限に達してもエラーにはなりませんが、ワークロードが遅くなり、遅延が深刻になった場合はクエリのタイムアウトが発生します。 

ワークロードが、接続数の上限に達すると、明示的なエラーが発生します。 リソース制限の詳細については、「[Azure Database for MySQL の制限事項](concepts-limits.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
価格レベルの詳細については、[Azure Database for MySQL の価格レベル](./concepts-service-tiers.md)に関するページをご覧ください。

