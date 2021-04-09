---
title: 電子設計自動化に Azure NetApp Files を使用するメリット | Microsoft Docs
description: 半導体およびチップの設計業界のニーズを満たすために Azure NetApp Files で提供されるソリューションについて説明します。 Azure NetApp Files を使用して、電子設計自動化 (EDA) の標準的な業界ベンチマークを実行するテスト シナリオを示します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82160155"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>電子設計自動化に Azure NetApp Files を使用するメリット

市場投入までの時間 (TTM) は、半導体およびチップの設計業界にとって重要な考慮事項です。 業界では、高帯域幅と低待機時間のストレージを必要としています。 この記事では、業界のニーズを満たすために Azure NetApp Files で提供されるソリューションについて説明します。 ここでは、Azure NetApp Files を使用して、電子設計自動化 (EDA) の標準的な業界ベンチマークを実行するテスト シナリオを示します。 

## <a name="test-scenario-configurations"></a>テスト シナリオの構成

テストには、次の構成の 3 つのシナリオがあります。 

|    シナリオ    |    ボリューム    |    クライアント<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    1 つ         |    1          |    1                           |
|    2 つ         |    6          |    24                          |
|    3       |    12         |    24                          |

最初のシナリオでは、単一ボリュームで処理可能な量について検討します。  

2 番目と 3 番目のシナリオでは、単一の Azure NetApp Files エンドポイントの制限を評価します。 これらでは、I/O の上限と待機時間の潜在的なメリットについて調査します。

## <a name="test-scenario-results"></a>テスト シナリオの結果

次の表は、テスト シナリオの結果をまとめたものです。

|    シナリオ       |    I/O レート<br>  (2 ミリ秒での)     |    I/O レート<br>  (エッジでの)     |    スループット<br>  (2 ミリ秒での)     |    スループット<br>  (エッジでの)     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 ボリューム       |    39,601                 |    49,502                      |    692 MiB/秒                 |    866 MiB/秒                      |
|    6 ボリューム      |    255,613                |    317,000                     |    4,577 MiB/秒               |    5,568 MiB/秒                    |
|    12 ボリューム     |    256,612                |    319,196                     |    4,577 MiB/秒               |    5,709 MiB/秒                    |

単一ボリュームのシナリオは、基本的なアプリケーション構成を表します。 これは後続のテスト シナリオのためのベースライン シナリオです。  

6 ボリュームのシナリオは、単一ボリュームのワークロードと比べて線形増加 (600%) を示しています。  1 つの仮想ネットワーク内のすべてのボリュームは、1 つの IP アドレスを介してアクセスされます。  

12 ボリュームのシナリオは、6 ボリュームのシナリオよりも全般的に待機時間が短くなることを示しています。 ただし、これに対応した、達成可能なスループットの増加はありません。   

次のグラフは、Azure NetApp Files での EDA ワークロードの待機時間と操作レートを示しています。  

![Azure NetApp Files での EDA ワークロードの待機時間と操作レート](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

次のグラフは、Azure NetApp Files での EDA ワークロードの待機時間とスループットを示しています。  

![Azure NetApp Files での EDA ワークロードの待機時間とスループット](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>テスト シナリオのレイアウト 

次の表は、テスト シナリオのレイアウトをまとめたものです。

|    テスト シナリオ     |    ディレクトリの総数     |    ファイルの総数     |
|----------------------|------------------------------------|------------------------------|
|    1 ボリューム          |    88,000                          |    880,000                   |
|    6 ボリューム         |    568,000                         |    5,680,000                 |
|    12 ボリューム        |    568,000                         |    5,680,000                 |

完全なワークロードは、同時に実行する機能フェーズと物理フェーズが混在したものです。 これは、ある EDA ツールのセットから別のセットへの一般的なフローを表します。   

機能フェーズは初期仕様と論理設計で構成されています。 物理フェーズは、論理設計が物理チップに変換されるときに発生します。 サインオフ フェーズとテープアウト フェーズで最終的なチェックが実行され、設計は製造ファウンドリに送らまれす。  

機能フェーズには、順次およびランダムの読み取りおよび書き込み I/O が混在しています。 機能フェーズは、ファイルの統計やアクセスの呼び出しなど、メタデータを集中的に使用します。 メタデータ操作では、サイズは実質的に存在しませんが、読み取りおよび書き込み操作は 1 K 未満から 16 K の間です。ほとんどの読み取りは 4 K から 16 K であり、ほとんどの書き込みは 4 K 以下です。 物理フェーズは、連続した読み取りおよび書き込みの操作で構成され、32 K と 64 K の操作サイズが混在しています。  

上のグラフでは、ほとんどのスループットは、ワークロードの順次物理フェーズからのものです。 I/O は、小規模でランダムな、メタデータを集中的に使用する機能フェーズからのものです。 両方のフェーズが並行して発生します。 

結論として、Azure コンピューティングと EDA 設計用の Azure NetApp Files を組み合わせてスケーラブルな帯域幅を確保することができます。 

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files を使用したソリューション アーキテクチャ](azure-netapp-files-solution-architectures.md)
