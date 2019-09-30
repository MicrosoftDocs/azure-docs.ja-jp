---
title: Azure Data Explorer クラスターに適した VM SKU を選択する
description: この記事では、Azure Data Explorer クラスターに最適な SKU サイズを選択する方法について説明します。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 8293fd2d84189cc1f1df3564abbfdcbf86e3543e
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186741"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターに適した VM SKU を選択する 

変化するワークロードのために新しいクラスターを作成するか、またはクラスターを最適化する場合、Azure Data Explorer では、選択できる複数の仮想マシン (VM) SKU が提供されます。 これらの VM は、すべてのワークロードのための最適なコストを提供できるように慎重に選択されています。 

データ管理クラスターのサイズと VM SKU は、Azure Data Explorer サービスによって完全に管理されます。 これらは、エンジンの VM サイズやインジェスト ワークロードなどの要因によって決定されます。 

エンジン クラスターの VM SKU は、[クラスターをスケールアップ](manage-cluster-vertical-scaling.md)することによっていつでも変更できます。 最初のシナリオに適した最小の SKU サイズから始めることが最善です。 クラスターをスケールアップすると、新しい VM SKU でクラスターが再作成されている間の最大 30 分のダウンタイムが発生することに注意してください。

> [!TIP]
> コンピューティングの[予約インスタンス (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) は、Azure Data Explorer クラスターに適用されます。  

この記事では、さまざまな VM SKU オプションについて説明した後、最適な選択を行うために役立つ技術的な詳細を提供します。

## <a name="select-a-cluster-type"></a>クラスターの種類を選択する

Azure Data Explorer には、次の 2 種類のクラスターが用意されています。

* **運用**: 運用クラスターには、エンジン クラスターとデータ管理クラスター用の 2 つのノードが含まれており、Azure Data Explorer の [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) のもとで運用されます。

* **Dev/Test (SLA なし)** : Dev/Test クラスターには、エンジン クラスター用の 1 つの D11 v2 ノードと、データ管理クラスター用の 1 つの D1 ノードがあります。 このクラスターの種類は、インスタンスの数が少なく、エンジン マークアップ料金が必要ないため、最も低いコスト構成です。 冗長性がないため、このクラスター構成には SLA がありません。

## <a name="sku-types"></a>SKU の種類

Azure Data Explorer クラスターを作成する場合は、計画されたワークロードのための*最適な* VM SKU を選択してください。 次の 2 つの Azure Data Explorer SKU ファミリから選択できます。

* **D v2**: D SKU はコンピューティングに最適化されており、次の 2 つのフレーバーで提供されます。
    * VM 自体
    * Premium Storage ディスクにバンドルされた VM

* **LS**: L SKU はストレージに最適化されています。 同等の価格の D SKU に比べてはるかに大きな SSD サイズになります。

使用可能な SKU の種類の主な違いを次の表で説明します。
 
| Attribute | D SKU | L SKU |
|---|---|---
|**小規模な SKU**|最小サイズは 2 つのコアを含む D11|最小サイズは 4 つのコアを含む L4 |
|**可用性**|すべてのリージョンで利用できます (DS+PS バージョンの可用性はさらに制限されます)|一部のリージョンで利用できます |
|**コアあたり、GB&nbsp;キャッシュあたりのコスト**|D SKU では高く、DS+PS バージョンでは低くなります|従量課金制オプションで最も低い |
|**予約インスタンス (RI) の価格**|高割引 (3 年のコミットメントで&nbsp;55% 超)|低割引 (3 年のコミットメントで&nbsp;20%) |  

## <a name="select-your-cluster-vm"></a>クラスターの VM を選択する 

クラスター VM を選択するには、[垂直方向のスケーリングを構成します](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

選択できるさまざまな VM SKU オプションを使用して、シナリオのパフォーマンスとホット キャッシュの要件に合わせてコストを最適化できます。 
* クエリの量が多いときに最適なパフォーマンスが必要な場合、理想的な SKU はコンピューティング最適化です。 
* クエリの負荷が比較的低いときに大量のデータにクエリを実行する必要がある場合は、ストレージ最適化 SKU が、コストを削減しながら引き続き優れたパフォーマンスを提供するために役立ちます。

小さな SKU ではクラスターあたりのインスタンスの数が制限されるため、RAM の多いより大きな VM を使用することをお勧めします。 RAM リソースに対する需要の多い一部のクエリの種類 (`joins` を使用するクエリなど) には、より多くの RAM が必要です。 そのため、スケーリング オプションを検討している場合は、インスタンスを追加してスケールアウトするのではなく、より大きな SKU にスケールアップすることをお勧めします。

## <a name="vm-options"></a>VM のオプション

Azure Data Explorer クラスター VM の技術仕様を次の表で説明します。

|**Name**| **カテゴリ** | **SSD サイズ** | **コア** | **RAM** | **Premium Storage ディスク (1&nbsp;TB)**| **クラスターあたりの最小インスタンス数** | **クラスターあたりの最大インスタンス数**
|---|---|---|---|---|---|---|---
|D11 v2| コンピューティング最適化 | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (1 である開発/テスト SKU を除く)
|D12 v2| コンピューティング最適化 | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| コンピューティング最適化 | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| コンピューティング最適化 | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| ストレージ最適化 | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| ストレージ最適化 | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| ストレージ最適化 | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| ストレージ最適化 | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| ストレージ最適化 | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| ストレージ最適化 | 1.3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| ストレージ最適化 | 2.6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Azure Data Explorer [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) を使用して、リージョンごとの更新された VM SKU の一覧を表示できます。 
* [さまざまな SKU](/azure/virtual-machines/windows/sizes) の詳細について参照してください。 

## <a name="next-steps"></a>次の手順

* さまざまなニーズに応じて VM SKU を変更することによって、エンジン クラスターをいつでも[スケールアップまたはスケールダウン](manage-cluster-vertical-scaling.md)できます。 

* 変化する需要に応じて容量を変更するために、エンジン クラスターのサイズを[スケールインまたはスケールアウト](manage-cluster-horizontal-scaling.md)できます。

