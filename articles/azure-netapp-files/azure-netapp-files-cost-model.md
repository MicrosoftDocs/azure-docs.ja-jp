---
title: Azure NetApp Files のコスト モデル | Microsoft Docs
description: サービスから経費を管理するための Azure NetApp Files のコスト モデルについて説明します。
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325608"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files のコスト モデル 

Azure NetApp Files のコスト モデルを理解すると、サービスから経費を管理するのに役立ちます。 

リージョン間レプリケーションに固有のコスト モデルについては、「[リージョン間レプリケーションのコスト モデル](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)」を参照してください。

## <a name="calculation-of-capacity-consumption"></a>容量消費の計算

Azure NetApp Files は、プロビジョニングされたストレージ容量に対して課金されます。  プロビジョニングされる容量は、容量プールを作成することによって割り当てられます。  容量プールは、プロビジョニングされた GiB/月あたりの料金に基づいて、1 時間単位で課金されます。 1 つの容量プールの最小サイズは 4 TiB であり、容量プールを後から 1 TiB 単位で拡張することができます。 ボリュームは容量プール内に作成されます。  各ボリュームには、プールでプロビジョニングされた容量からデクリメントするクォータが割り当てられます。 ボリュームに割り当てることができるクォータは、最小 100 GiB から最大 100 TiB の範囲です。  

アクティブなボリュームでは、クォータに対する容量消費は論理 (有効) 容量に基づきます。

ボリュームの実際の容量消費がそのストレージ クォータを超えた場合、ボリュームは拡張を続けることができます。 実際のボリューム サイズがシステムの制限 (100 TiB) を下回っている限り、書き込みが引き続き許可されます。  

プロビジョニングされた分量に対する容量プールの合計使用容量は、プール内の全ボリュームの、割り当てられたクォータと実際の消費量のうち大きいほうの合計です。 

   ![合計使用容量の計算](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

次の図は、これらの概念を示しています。  
* プロビジョニング済み容量が 4 TiB の容量プールがあります。  プールには、3 つのボリュームが含まれています。  
    * ボリューム 1 は 2 TiB のクォータが割り当てられており、消費量は 800 GiB です。  
    * ボリューム 2 は 1 TiB のクォータが割り当てられており、消費量は 100 GiB です。  
    * ボリューム 3 は 500 GiB のクォータが割り当てられていますが、消費量は 800 GiB です (超過)。  
* 容量プールは 4 TiB の容量 (プロビジョニングされた分量) に対して測定されます。  
    3.8 TiB の容量が消費されています (ボリューム 1 と 2 は 2 TiB と 1 TiB のクォータ、ボリューム 3 は実際の消費量 800 GiB)。 200 GiB の容量が残っています。

   ![3 つのボリュームがある容量プール](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>容量消費の超過  

プールの合計使用容量がそのプロビジョニング容量を超えた時点で、データ書き込みはまだ許可されています。  猶予期間 (1 時間) が過ぎてもなお、プールの使用容量がそのプロビジョニング容量を超えている場合、プロビジョニング容量が合計使用容量を超えるまでプール サイズが自動的に 1 TiB 単位で増加します。  たとえば上の図で、ボリューム 3 が拡張し続けて実際の消費量が 1.2 TiB に達した場合、猶予期間の後、プールは自動的に 5 TiB にサイズ変更されます。  その結果、プロビジョニングされたプール容量 (5 TiB) が使用容量 (4.2 TiB) を上回ります。  

容量プールのサイズは、ボリュームの需要に合わせて自動的に拡張されますが、ボリューム サイズが減少したときに自動的に縮小されることはありません。 ボリューム サイズが減少した後に容量プールのサイズを縮小する場合 (ボリュームのデータ クリーンアップ後など) は、_手動_ で容量プールのサイズを小さくする必要があります。

## <a name="manual-changes-of-the-pool-size"></a>プール サイズの手動変更  

プール サイズを手動で増減できます。 ただし、次の制約が適用されます。
* サービスの最小および最大制限  
    [リソース制限](azure-netapp-files-resource-limits.md)に関する記事を参照してください。
* 初回の 4 TiB (最小容量) 購入後の増減単位は 1 TiB
* 最小請求単位は 1 時間
* プールのプロビジョニング サイズをプールの合計使用容量よりも小さくすることはできません。
* 手動 QoS が使用されている容量プールの場合、プール サイズを小さくできるのは、サイズとサービス レベルによって、全ボリュームの実際の割り当てスループットよりも大きいスループットが提供される場合のみです。

## <a name="behavior-of-maximum-size-pool-overage"></a>プールの最大サイズ超過時の動作   

容量プールの作成またはサイズ変更時の最大サイズは 500 TiB です。  容量プールの合計使用容量が 500 TiB を超えると、次の状況が発生します。
* データ書き込みは引き続き許可されます (ボリュームがシステムの最大値である 100 TiB を下回っている場合)。
* 1 時間の猶予期間が過ぎると、プールのプロビジョニング容量が合計使用容量を超えるまで、プールは 1 TiB 単位で自動的にサイズ変更されます。
* 500 TiB を超えて追加でプロビジョニングされた課金対象のプール容量は、ボリューム クォータの割り当てには使用できません。 パフォーマンス QoS 制限を拡張する目的にも使用できません。  
    パフォーマンス制限および QoS サイズ変更については、[サービス レベル](azure-netapp-files-service-levels.md)に関する記事を参照してください。

次の図は、これらの概念を示しています。
* Premium ストレージ レベルで容量が 500 TiB の容量プールがあります。 プールには、9 つのボリュームが含まれています。
    * ボリューム 1 ～ 8 には、それぞれ 60 TiB のクォータが割り当てられます。  合計使用容量は 480 TiB です。  
        各ボリュームには、スループットが 3.75 GiB/秒 (60 TiB * 64 MiB/秒) の QoS 制限があります。  
    * ボリューム 9 には 20 TiB のクォータが割り当てられます。  
        ボリューム 9 には、スループットが 1.25 GiB/秒 (20 TiB * 64 MiB/秒) の QoS 制限があります。
* ボリューム 9 は超過のシナリオです。 実際の消費量は 25 TiB です。  
    * 1 時間の猶予期間の後、容量プールは 505 TiB にサイズ変更されます。  
        ボリューム 1 ～ 8 の 8 * 60 TiB のクォータに、ボリューム 9 の実際の消費量 25 TiB を足したものが合計使用容量です。
    * 課金対象の容量は 505 TiB です。
    * ボリューム 9 のボリューム クォータは増やせません (プールの合計クォータ割り当てが 500 TiB を超えることはできないため)。
    * 追加の QoS スループットを割り当てることはできません (プールの合計 QoS は 500 TiB 基準のままなので)。

   ![9 つのボリュームがある容量プール](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>スナップショットの容量消費 

Azure NetApp Files のスナップショットの容量消費は、親ボリュームのクォータに対して課金されます。  その結果、ボリュームが属する容量プールと同じ課金レートを共有します。  ただし、アクティブ ボリュームとは違って、スナップショットの消費量は消費された増分容量に基づいて測定されます。  Azure NetApp Files のスナップショットは本質的には差分です。 データの変更率にもよりますが、スナップショットの消費容量は多くの場合、アクティブ ボリュームの論理容量を大きく下回ります。 たとえば、10 GiB の差分データのみが含まれる 500 GiB のボリュームのスナップショットがあるとします。 そのスナップショットのボリューム クォータに対して課金される容量は、500 GiB ではなく 10 GiB になります。 

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files 価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [リージョン間レプリケーションのコスト モデル](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
