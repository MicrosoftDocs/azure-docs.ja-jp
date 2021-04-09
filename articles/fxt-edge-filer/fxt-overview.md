---
title: Microsoft Azure FXT Edge Filer の概要
description: ハイ パフォーマンス コンピューティング用のアクティブ アーカイブおよびファイル アクセラレーター ソリューションである、Azure FXT Edge Filer ハイブリッド ストレージ キャッシュについて説明します
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 72308bf64246a8041bd40fa557280014a13c95eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218784"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Azure FXT Edge Filer ハイブリッド ストレージ キャッシュとは何か

Azure FXT Edge Filer は、ハイ パフォーマンス コンピューティング (HPC) タスクに対して、高速ファイル アクセスとアクティブ アーカイブを提供するハイブリッド ストレージ キャッシュ アプライアンスです。

それは複数のデータ ソースで動作し、データ ソースの格納場所を問いません (ローカル データ センター内、リモート、またはクラウド上)。 Azure FXT Edge Filer では、多様なストレージ システム内のデータに対して、統一された名前空間を提供できます。

クラスター化されたファイル システムとして連携する 3 台以上の FXT Edge Filer ハードウェア デバイスによって、キャッシュが提供されます。 必要なハードウェアの購入については、Microsoft の担当者にお問い合わせください。

詳細については、「[Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/)」の製品情報とデータ シートをご覧ください。

## <a name="use-cases"></a>ユース ケース

Azure FXT Edge Filer では、以下のようなワークフローに最も適した生産性が強化されます。

* 読み取り負荷の高いファイル アクセス ワークフロー
* NFSv3 または SMB2 プロトコル
* CPU のコア数が1,000 から 100,000 の コンピューティング ファーム

### <a name="nas-optimization-and-scaling"></a>NAS の最適化とスケーリング

Azure FXT Edge Filer キャッシュを使用して、既存の NetApp と Dell EMC Isilon NAS システムにスムーズにアクセスできます。 Azure Blob や他のクラウド ストレージを追加して、クライアント側でのデータ アクセス プロセスの修正なしでスケーラビリティを提供できます。

### <a name="wan-caching"></a>WAN キャッシュ

Azure FXT Edge Filer を使用して、パワー ユーザーが他の場所に格納されているデータを必要とするときに、高速ファイル アクセスをサポートできます。 一元化されたデータ センター内でバックアップと他のデータ管理システムを維持しながら、アクセスを提供します。

### <a name="active-archive-in-azure-blob"></a>Azure Blob でのアクティブ アーカイブ

Azure FXT Edge Filer をアクセス ポイントとして使用して、データ センターをクラウド ストレージに拡張します。

## <a name="features"></a>特徴

2 種類のハードウェア モデルを利用できます。

| モデル | DRAM | NVMe SSD | ネットワーク ポート |
|-------|------|----------|---------------|
| FXT 6600 | 1,536 GB | 25.6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12.8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |

## <a name="next-steps"></a>次のステップ

* Azure FXT Edge Filer の詳細については、[仕様](fxt-specs.md)または[インストールのチュートリアル](fxt-install.md)をご覧ください。
* Azure FXT Edge Filer の購入方法については、[Azure FXT Edge Filer 製品ページ](https://azure.microsoft.com/services/fxt-edge-filer/)をご覧ください。
