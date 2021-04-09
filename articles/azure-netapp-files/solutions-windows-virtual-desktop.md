---
title: Azure NetApp Files で Windows Virtual Desktop を使用する | Microsoft Docs
description: Azure NetApp Files を使用した Windows Virtual Desktop の展開について、ベスト プラクティスのガイダンスとサンプルのブループリントを提供します。
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
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 0cd1f6210fbdb74e3fd511150157dccca3e92dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932466"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Windows Virtual Desktop での Azure NetApp Files 利用のメリット 

この記事では、Azure NetApp Files を使用して Windows Virtual Desktop (WVD) を展開する場合のベスト プラクティスについて説明します。

Azure NetApp Files は、Azure の高パフォーマンスのファイル ストレージ サービスです。 最大 450,000 IOPS と 1 ミリ秒未満の待ち時間を提供することができ、非常に大規模な Windows Virtual Desktop 展開をサポートできます。 データ プレーン アクセスを維持しながら IO を一時停止することなく、帯域幅を調整し、Azure NetApp Files ボリュームのサービス レベルをオンデマンドでほぼ瞬時に変更することができます。 この機能により、WVD デプロイ スケールをコストに合わせて簡単に最適化できます。 また、ボリュームのパフォーマンスに影響を与えることなく、スペース効率の良いポイントインタイムのボリュームのスナップショットを作成することもできます。 この機能により、`~snapshot` ディレクトリからのコピーを使用して個々の [FSLogix ユーザー プロファイル コンテナー](../virtual-desktop/store-fslogix-profile.md)をロールバックしたり、ボリュームを元に戻す機能を使用してボリューム全体を一度で瞬時にロールバックしたりすることができます。  データの損失や破損からボリュームを保護するために、最大 255 (回転) のスナップショットにより、管理者は、行われた操作を取り消す機会を多く持つことができます。

## <a name="sample-blueprints"></a>サンプルのブループリント

次のサンプルのブループリントは、Windows Virtual Desktop と Azure NetApp Files の統合を示しています。 プールされたデスクトップのシナリオでは、ユーザーは[マルチセッション仮想マシン](../virtual-desktop/windows-10-multisession-faq.md#what-is-windows-10-enterprise-multi-session)を使用して、プール内の使用可能な最適なセッション ([幅優先モード](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)) ホストにリダイレクトされます。 一方、個人用デスクトップは、各ユーザーが独自の仮想マシンを持つシナリオ用に予約されています。

### <a name="pooled-desktop-scenario"></a>プールされたデスクトップのシナリオ

プールされたシナリオの場合、Windows Virtual Desktop チームでは、vCPU へのユーザー数によって次のガイダンスに従うことを[お勧めしています](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations)。 この推奨事項では、仮想マシンのサイズが指定されていないことにご注意ください。

|     ワークロードの種類     |     白    |     Medium    |     ヘビー    |
|-----------------------|--------------|---------------|--------------|
|     vCPU あたりのユーザー数    |     6        |     4         |     2        |


この推奨事項は、500 ユーザーの LoginVSI テスト (各 D16as_V4 仮想マシンに約 62 の "ナレッジ/ミディアム ユーザー" をログオンさせる) で確認されています。 

例として、D16as_V4 仮想マシンあたり 62 ユーザーの場合、Azure NetApp Files は環境あたり 6 万ユーザーが簡単にサポートされます。 D32as_v4 仮想マシンの上限を評価するテストが進行中です。 vCPU あたりの WVD ユーザーの推奨事項が D32as_v4 に当てはまる場合は、次の図に示すように、[1,000 IP VNet の制限](./azure-netapp-files-network-topologies.md)に違反する前に、12 万を超えるユーザーが 1,000 の仮想マシンに収まります。  

![Windows Virtual Desktop のプールされたデスクトップのシナリオ](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>個人用デスクトップのシナリオ 

次の図は、個人用デスクトップのシナリオでの汎用アーキテクチャの推奨事項を示しています。 ユーザーは特定のデスクトップ ポッドにマップされ、各ポッドにある仮想マシンは 1,000 弱のため、管理 VNet から IP アドレスを伝達するための余地が残されます。 Azure NetApp Files は、シングル セッション ホスト プール VNet あたり 900 以上の個人用デスクトップを簡単に処理できます。実際の仮想マシンの数は、1,000 からハブ VNet にある管理ホストの数を差し引いた数と等しくなります。 さらに個人用デスクトップが必要な場合は、次の図に示すように、ポッド (ホスト プールと仮想ネットワーク) を簡単に追加できます。 

![Windows Virtual Desktop の個人用デスクトップのシナリオ](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

このようなポッド ベースのアーキテクチャを構築する場合、ユーザーが常にユーザー プロファイルを見つけられるようにするために、ログイン時に正しいポッドにユーザーを割り当てることが重要です。 

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files を使用したソリューション アーキテクチャ](azure-netapp-files-solution-architectures.md)