---
title: Azure HDInsight でサポートされるノード構成
description: HDInsight クラスター ノードの最小構成と推奨構成について説明します。
keywords: VM のサイズ、クラスターのサイズ、クラスター構成
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076075"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight の既定および推奨のノード構成

この記事では、Azure HDInsight クラスターの既定および推奨のノード構成について説明します。

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>クラスターの既定および推奨のノード構成と仮想マシンのサイズ

次の表は、HDInsight クラスターの既定および推奨の仮想マシン (VM) のサイズを示しています。  この情報は、HDInsight クラスターをデプロイする PowerShell または Azure CLI スクリプトを作成するときに使用する、VM のサイズを理解する必要があります。 

クラスターで 32 以上のワーカー ノードが必要になる場合、少なくとも 8 コア、14 GB のヘッド ノードのサイズを選択してください。 

データ ディスクがあるクラスターの種類は、高速書き込み機能が有効な Kafka および HBase クラスターのみです。 HDInsight は、これらのシナリオで P30 と S30 のディスク サイズをサポートしています。

各 VM の種類の仕様の詳細については、次のドキュメントを参照してください。

* [汎用仮想マシンのサイズ:Dv2 シリーズ 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [メモリ最適化済み仮想マシンのサイズ:Dv2 シリーズ 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [汎用仮想マシンのサイズ:Av2 シリーズ 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>ブラジル南部と西日本を除くすべてのサポートされているリージョン

> [!Note]
> PowerShell やその他のスクリプトで使用する SKU 識別子を取得するには、次の表のすべての VM SKU の先頭に `Standard_` を追加します。 たとえば、`D12_v2` は `Standard_D12_v2` になります。

| クラスターの種類 | Hadoop | hbase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| ヘッド: 既定の VM サイズ | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| ヘッド: 推奨される VM サイズ | D13_v2、<br/>D14_v2、<br/>D5_v2 | D3_v2、<br/>D4_v2、<br/>D12_v2 | D13_v2、<br/>D14_v2 | A4_v2、<br/>A8_v2 | D12_v2、<br/>D13_v2、<br/>D14_v2 | D12_v2、<br/>D13_v2、<br/>D14_v2 | D3_v2、<br/>D4_v2、<br/>D12_v2 |
| worker: 既定の VM サイズ | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 (ブローカーあたり 2 S30 ディスク) |
| worker: 推奨される VM サイズ | D5_v2、<br>D12_v2、<br/>D13_v2 | D3_v2、<br/>D4_v2、<br/>D13_v2 | D13_v2、<br/>D14_v2 | D3_v2<br/>D4_v2、<br/>D12_v2 | D12_v2、<br>D13_v2、<br>D14_v2 | D4_v2、<br/>D12_v2、<br>D13_v2、<br>D14_v2 | D3_v2、<br/>D4_v2、<br/>DS3_v2、<br/>DS4_v2 |
| ZooKeeper: 既定の VM サイズ |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: 推奨される VM サイズ |  | A4_v2、 <br/>A8_v2、 <br/>A2m_v2 | A4_v2、<br/>A8_v2、<br/>A2m_v2 | A4_v2、<br/>A2_v2、<br/>A8_v2 |  | A2_v2 | A4_v2、<br/> A8_v2、<br/>A2m_v2 |
| ML サービス: 既定の VM サイズ |  |  |  |  |  | D4_v2 |  |
| ML サービス: 推奨される VM サイズ |  |  |  |  |  | D4_v2、<br/> D12_v2、<br/> D13_v2、<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>ブラジル南部および西日本のみ

| クラスターの種類 | Hadoop | hbase | Interactive Query | Storm | Spark | ML サービス |
|---|---|---|---|---|---|---|
| ヘッド: 既定の VM サイズ | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| ヘッド: 推奨される VM サイズ | D5_v2、<br/> D13_v2、<br/> D14_v2 | D3_v2、<br/> D4_v2、<br/> D12_v2 | D13_v2、<br/> D14_v2 | A4_v2、<br/> A8_v2 | D12_v2、<br/> D13_v2、<br/> D14_v2 | D12_v2、<br/> D13_v2、<br/> D14_v2 |
| worker: 既定の VM サイズ | D4 | D4 | D14 | D3 | D13 | D4 |
| worker: 推奨される VM サイズ | D5_v2、<br/> D12_v2、<br/> D13_v2 | D3_v2、<br/> D4_v2、<br/> D13_v2 | D13_v2、<br/> D14_v2 | D3_v2、<br/> D4_v2、<br/> D12_v2 | D12_v2、<br/> D13_v2、<br/> D14_v2 | D4_v2、<br/> D12_v2、<br/> D13_v2、<br/> D14_v2 |
| ZooKeeper: 既定の VM サイズ |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: 推奨される VM サイズ |  | A4_v2、<br/> A8_v2、<br/> A2m_v2 | A4_v2、<br/> A8_v2、<br/> A2m_v2 | A4_v2、<br/> A8_v2 |  | A2_v2 |
| ML サービス: 既定の VM サイズ |  |  |  |  |  | D4 |
| ML サービス: 推奨される VM サイズ |  |  |  |  |  | D4_v2、<br/> D12_v2、<br/> D13_v2、<br/> D14_v2 |

> [!NOTE]
> - ヘッドは、Storm クラスター タイプでは *Nimbus* と呼ばれます。
> - Worker は、Storm クラスター タイプでは *Supervisor* と呼ばれます。
> - Worker は、HBase クラスター タイプでは *Region* と呼ばれます。

## <a name="next-steps"></a>次の手順

* [HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは](hdinsight-component-versioning.md)
