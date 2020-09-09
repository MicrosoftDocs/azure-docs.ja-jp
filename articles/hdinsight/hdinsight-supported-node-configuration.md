---
title: Azure HDInsight でサポートされるノード構成
description: HDInsight クラスター ノードの最小構成と推奨構成について説明します。
keywords: VM のサイズ、クラスターのサイズ、クラスター構成
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: 0bf5559590b66400fc4fc4dc27ea88c3522effb1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646621"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight の既定および推奨のノード構成

この記事では、Azure HDInsight クラスターの既定および推奨のノード構成について説明します。

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>クラスターの既定および最小推奨のノード構成と仮想マシンのサイズ

次の表は、HDInsight クラスターの既定および推奨の仮想マシン (VM) のサイズを示しています。  この情報は、HDInsight クラスターをデプロイする PowerShell または Azure CLI スクリプトを作成するときに、使用する VM のサイズを把握するために必要です。

クラスターで 32 以上のワーカー ノードが必要になる場合、少なくとも 8 コア、14 GB のヘッド ノードのサイズを選択してください。

データ ディスクがあるクラスターの種類は、高速書き込み機能が有効な Kafka および HBase クラスターのみです。 HDInsight は、これらのシナリオで P30 と S30 のディスク サイズをサポートしています。 その他のすべてのクラスターの種類について、HDInsight では、クラスターにマネージド ディスク領域が用意されています。 2019 年 11 月 7 日以降、新しく作成されるクラスター内の各ノードのマネージド ディスク サイズは 128 GB です。 これは変更できません。

このドキュメントで使用されているすべての最小推奨 VM の種類の仕様を次の表にまとめます。

| サイズ              | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 16             / 16x500           | 4/3,000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 32            / 32x500           | 8/6,000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000/750/375                                          | 64             / 64x500           | 8/12,000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16 / 16x500                         | 4/3,000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32 / 32x500                       | 8/6,000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 64 / 64x500                       | 8/12,000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000/20/10                                           | 2 / 2x500               | 2/250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000/40/20                                           | 4 / 4x500               | 2/500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000/80/40                                           | 8 / 8x500               | 4/1,000                     |

各 VM の種類の仕様の詳細については、次のドキュメントを参照してください。

* [汎用仮想マシンのサイズ:Dv2 シリーズ 1-5](../virtual-machines/dv2-dsv2-series.md)
* [メモリ最適化済み仮想マシンのサイズ:Dv2 シリーズ 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [汎用仮想マシンのサイズ:Av2 シリーズ 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>ブラジル南部と西日本を除くすべてのサポートされているリージョン

> [!Note]
> PowerShell やその他のスクリプトで使用する SKU 識別子を取得するには、次の表のすべての VM SKU の先頭に `Standard_` を追加します。 たとえば、`D12_v2` は `Standard_D12_v2` になります。

| クラスターの種類 | Hadoop | hbase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| ヘッド: 既定の VM サイズ | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2、 <br/>D13_v2* | D12_v2 | D3_v2 |
| ヘッド: 最小推奨 VM サイズ | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2、 <br/>D13_v2* | D12_v2 | D3_v2 |
| worker: 既定の VM サイズ | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 (ブローカーあたり 2 S30 ディスク) |
| ワーカー: 最小推奨 VM サイズ | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: 既定の VM サイズ |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: 最小推奨 VM サイズ |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML サービス: 既定の VM サイズ |  |  |  |  |  | D4_v2 |  |
| ML サービス: 最小推奨 VM サイズ |  |  |  |  |  | D4_v2 |  |

\* = Spark Enterprise セキュリティ パッケージ (ESP) クラスターの VM サイズ

### <a name="brazil-south-and-japan-west-only"></a>ブラジル南部および西日本のみ

| クラスターの種類 | Hadoop | hbase | Interactive Query | Storm | Spark | ML サービス |
|---|---|---|---|---|---|---|
| ヘッド: 既定の VM サイズ | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| ヘッド: 最小推奨 VM サイズ | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| worker: 既定の VM サイズ | D4 | D4 | D14 | D3 | D13 | D4 |
| ワーカー: 最小推奨 VM サイズ | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: 既定の VM サイズ |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: 最小推奨 VM サイズ |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML サービス: 既定の VM サイズ |  |  |  |  |  | D4 |
| ML サービス: 最小推奨 VM サイズ |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - ヘッドは、Storm クラスター タイプでは *Nimbus* と呼ばれます。
> - Worker は、Storm クラスター タイプでは *Supervisor* と呼ばれます。
> - Worker は、HBase クラスター タイプでは *Region* と呼ばれます。

## <a name="next-steps"></a>次のステップ

* [HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは](hdinsight-component-versioning.md)
