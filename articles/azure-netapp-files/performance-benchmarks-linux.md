---
title: Azure NetApp Files の Linux 用パフォーマンス ベンチマーク | Microsoft Docs
description: Azure NetApp Files で Linux 用に提供されるパフォーマンス ベンチマークについて説明します。
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
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82614012"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files の Linux 用パフォーマンス ベンチマーク

この記事では、Azure NetApp Files で Linux 用に提供されるパフォーマンス ベンチマークについて説明します。

## <a name="linux-scale-out"></a>Linux のスケールアウト

このセクションでは、Linux ワークロードのスループットとワークロードの IOPS のパフォーマンス ベンチマークについて説明します。

### <a name="linux-workload-throughput"></a>Linux ワークロードのスループット  

次のグラフは、64 キビバイト (KiB) のシーケンシャル ワークロードと 1 TiB のワーキング セットを表しています。 これは、約 1,600 MiB/秒の純粋なシーケンシャル書き込みから約 4,500 MiB/秒の純粋なシーケンシャル読み取りまでの範囲で単一の Azure NetApp Files ボリュームが処理できることを示しています。  

このグラフは、純粋な読み取りから純粋な書き込みまで 10% ずつ低下することを示しています。 これは、さまざまな読み取りと書き込みの比率 (100%:0%、90%:10%、80%:20% など) を使用するときに想定できる値を示しています。

![Linux ワークロードのスループット](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux ワークロードの IOPS  

次のグラフは、4 キビバイト (KiB) のランダム ワークロードと 1 TiB のワーキング セットを表しています。 このグラフは、約 130,000 回の純粋なランダムな書き込みから約 460,000 回の純粋なランダム読み取りまでの範囲で単一の Azure NetApp Files ボリュームが処理できることを示しています。  

このグラフは、純粋な読み取りから純粋な書き込みまで 10% ずつ低下することを示しています。 これは、さまざまな読み取りと書き込みの比率 (100%:0%、90%:10%、80%:20% など) を使用するときに想定できる値を示しています。

![Linux ワークロードの IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux のスケールアップ  

Linux 5.3 カーネルでは、NFS の単一クライアントのスケールアウト ネットワークが可能になります (`nconnect`)。 このセクションのグラフは、NFSv3 でのクライアント側マウント オプションの検証テストの結果を示しています。 この機能は、SUSE (SLES12SP4 以降) および Ubuntu (19.10 リリース以降) で使用できます。 これは、SMB マルチチャネルと Oracle Direct NFS の両方の概念に似ています。

このグラフでは、`nconnect` をマウントされた未接続ボリュームと比較して、その利点を示しています。 グラフでは、FIO により、us-west2 Azure リージョンにある単一の D32s_v3 インスタンスからワークロードが生成されています。

### <a name="linux-read-throughput"></a>Linux の読み取りスループット  

次のグラフは、シーケンシャル読み取りにおいて `nconnect` を使用した場合に約 3,500 MiB/秒の読み取りが実現されることを示しています。これは、`nconnect` を使用していない場合の約 2.3 倍です。

![Linux の読み取りスループット](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux の書き込みスループット  

次のグラフは、シーケンシャル書き込みを示しています。 ここから、シーケンシャル書き込みに関しては `nconnect` に大きなメリットがないことがわかります。 大まかに言うと、1,500 MiB/秒は、シーケンシャル書き込みボリュームの上限と D32s_v3 インスタンスのエグレス制限の両方に該当します。

![Linux の書き込みスループット](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux の読み取り IOPS  

次のグラフは、ランダム読み取りにおいて `nconnect` を使用した場合に約 200,000 回の読み取り IOPS が実現されることを示しています。これは、`nconnect` を使用していない場合の約 3 倍です。

![Linux の読み取り IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux の書き込み IOPS  

次のグラフは、ランダム書き込みにおいて `nconnect` を使用した場合に約 135,000 回の書き込み IOPS が実現されることを示しています。これは、`nconnect` を使用していない場合の約 3 倍です。

![Linux の書き込み IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files: クラウド ストレージを最大限に活用する](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
