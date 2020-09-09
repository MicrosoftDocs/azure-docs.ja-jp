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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 5f88b4755c7b4c0b20f27065cf9de2351251bc1c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513878"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp Files のパフォーマンスに関する考慮事項

ボリュームの[スループット制限](azure-netapp-files-service-levels.md)は、ボリュームに割り当てられたクォータと選択されたサービス レベルの組み合わせで決まります。 Azure NetApp Files についてパフォーマンス計画を立てるときは、いくつかの考慮事項を把握している必要があります。 

## <a name="quota-and-throughput"></a>クォータとスループット  

スループット制限は、実現される実際のパフォーマンスの 1 つの決定要因でしかありません。  

一般的なストレージのパフォーマンスに関する考慮事項 (読み取りと書き込みの組み合わせ、転送サイズ、ランダム パターンまたはシーケンシャル パターンなど) とその他の多くの要因が、実現されるパフォーマンス全体に寄与します。  

テストで観測された最大の実験的スループットは、4,500 MiB/秒 です。  Premium ストレージ レベルでは、70.31 TiB のボリューム クォータにより、このパフォーマンス レベルを達成するのに十分高いスループット制限がプロビジョニングされます。  

70.31 TiB を超えるボリューム クォータ量の割り当てを検討している場合は、追加データを格納するために追加のクォータをボリュームに割り当てることができます。 ただし、追加されたクォータによって、実際のスループットがさらに増加することはありません。  

## <a name="overprovisioning-the-volume-quota"></a>ボリューム クォータのオーバープロビジョニング

ワークロードのパフォーマンスがスループット制限にバインドされている場合は、より高いスループット レベルを設定してより高いパフォーマンスを達成するために、ボリューム クォータをオーバープロビジョニングすることが可能です。  

たとえば、Premium ストレージ レベルのボリュームに 500 GiB のデータのみ格納されているが、128 MiB/秒 のスループットが必要である場合は、クォータを 2 TiB に設定し、それに応じてスループット レベルが設定されるようにすることができます (1 TB あたり 64 MiB/秒 * 2 TiB = 128 MiB/秒)。  

より高いスループットを達成するために一貫してボリュームをオーバープロビジョニングしている場合は、代わりに、使用するサービス レベルを上げることを検討してください。  上記の例では、Ultra ストレージ レベルを代わりに使用することで、半分のボリューム クォータで同じスループット制限を達成することができます (Tib あたり 128 MiB/秒 * 1 TiB = 128 MiB/秒)。

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>ボリューム クォータの動的な増加または削減

パフォーマンス要件が本質的に一時的なものである場合、またはパフォーマンスのニーズを一定期間増加した場合は、動的にボリューム クォータを増減して瞬時にスループット制限を調整できます。  次の考慮事項に注意してください。 

* ボリューム クォータの増減は、IO を一時停止せずに行うことができ、ボリュームへのアクセスが中断されたり影響を受けたりすることはありません。  

    クォータの調整は、ボリュームに対するアクティブな I/O トランザクション中に行うことができます。  ボリューム クォータは、ボリュームに格納されている論理データ量未満にまで削減することはできない点に注意してください。

* ボリューム クォータが変更されると、スループット制限の対応する変更はほぼ瞬時に実行されます。 

    この変更によってボリュームのアクセスや I/O が中断されたり影響を受けたりすることはありません。  

* ボリューム クォータの調整には、容量プール サイズの変更が必要です。  

    容量プール サイズの調整は、ボリュームの可用性や I/O に影響を与えることなく動的に行うことができます。

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
- [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md)