---
title: Azure NetApp Files のパフォーマンスに関する考慮事項 | Microsoft Docs
description: クォータとスループット制限の関係や、ボリューム クォータを動的に増減する方法など、Azure NetApp Files のパフォーマンスについて説明します。
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
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713390"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp Files のパフォーマンスに関する考慮事項

自動 QoS を使用するボリュームの[スループット制限](azure-netapp-files-service-levels.md)は、ボリュームに割り当てられたクォータと選択されたサービス レベルの組み合わせで決まります。 手動 QoS を使用するボリュームの場合は、スループット制限を個別に定義できます。 Azure NetApp Files についてパフォーマンス計画を立てるときは、いくつかの考慮事項を把握している必要があります。 

## <a name="quota-and-throughput"></a>クォータとスループット  

スループット制限は、実現される実際のパフォーマンスの 1 つの決定要因でしかありません。  

一般的なストレージのパフォーマンスに関する考慮事項 (読み取りと書き込みの組み合わせ、転送サイズ、ランダム パターンまたはシーケンシャル パターンなど) とその他の多くの要因が、実現されるパフォーマンス全体に寄与します。  

テストで観測された最大の実験的スループットは、4,500 MiB/秒 です。  Premium ストレージ層では、70.31 TiB の自動 QoS ボリューム クォータにより、このパフォーマンス レベルを達成するのに十分高いスループット制限がプロビジョニングされます。  

自動 QoS ボリュームの場合、70.31 TiB を超えるボリューム クォータ量の割り当てを検討している場合は、追加データを格納するために追加のクォータをボリュームに割り当てることができます。 ただし、追加されたクォータによって、実際のスループットがさらに増加することはありません。  

同じ経験的スループット上限が、手動 QoS を使用するボリュームにも適用されます。 ボリュームに割り当てることができる最大スループットは、4,500 MiB/秒です。

## <a name="automatic-qos-volume-quota-and-throughput"></a>自動 QoS ボリューム クォータとスループット

このセクションでは、自動 QoS の種類を使用したボリュームのクォータ管理とスループットについて説明します。

### <a name="overprovisioning-the-volume-quota"></a>ボリューム クォータのオーバープロビジョニング

ワークロードのパフォーマンスがスループット制限にバインドされている場合は、より高いスループット レベルを設定してより高いパフォーマンスを達成するために、自動 QoS ボリューム クォータをオーバープロビジョニングすることが可能です。  

たとえば、Premium ストレージ層の自動 QoS ボリュームに 500 GiB のデータのみ格納されているが、128 MiB/秒 のスループットが必要である場合は、クォータを 2 TiB に設定し、それに応じてスループット レベルが設定されるようにすることができます (1 TB あたり 64 MiB/秒 * 2 TiB = 128 MiB/秒)。  

より高いスループットを達成するために一貫してボリュームをオーバープロビジョニングしている場合は、代わりに、手動 QoS ボリュームを使用するか、使用するサービス レベルを上げることを検討してください。  上記の例では、Ultra ストレージ層を代わりに使用することで、自動 QoS ボリューム クォータの半分で同じスループット制限を達成することができます (Tib あたり 128 MiB/秒 * 1 TiB = 128 MiB/秒)。

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>ボリューム クォータの動的な増加または削減

パフォーマンス要件が本質的に一時的なものである場合、またはパフォーマンスのニーズを一定期間増加した場合は、動的にボリューム クォータを増減して瞬時にスループット制限を調整できます。  次の考慮事項に注意してください。 

* ボリューム クォータの増減は、IO を一時停止せずに行うことができ、ボリュームへのアクセスが中断されたり影響を受けたりすることはありません。  

    クォータの調整は、ボリュームに対するアクティブな I/O トランザクション中に行うことができます。  ボリューム クォータは、ボリュームに格納されている論理データ量未満にまで削減することはできない点に注意してください。

* ボリューム クォータが変更されると、スループット制限の対応する変更はほぼ瞬時に実行されます。 

    この変更によってボリュームのアクセスや I/O が中断されたり影響を受けたりすることはありません。  

* ボリューム クォータを調整するには、容量プール サイズの変更が必要になることがあります。  

    容量プール サイズの調整は、ボリュームの可用性や I/O に影響を与えることなく動的に行うことができます。

## <a name="manual-qos-volume-quota-and-throughput"></a>手動 QoS ボリューム クォータとスループット 

手動 QoS ボリュームを使用する場合は、スループットを各ボリュームに個別に割り当てることができるため、より高いスループットを実現するためにボリューム クォータをオーバープロビジョニングする必要はありません。 ただし、パフォーマンスのニーズに応じて十分なスループットを備えた容量プールが事前にプロビジョニングされていることを確認する必要があります。 容量プールのスループットは、そのサイズとサービス レベルに応じてプロビジョニングされます。 詳細については、「[Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files パフォーマンス計算ツール](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
- [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md)
