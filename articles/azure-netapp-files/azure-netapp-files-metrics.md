---
title: Azure NetApp Files のメトリック | Microsoft Docs
description: Azure NetApp Files では、割り当て済みストレージ、実際のストレージ使用量、ボリュームの IOPS、および待機時間に関するメトリックが提供されます。 これらのメトリックを使用して、使用状況とパフォーマンスを把握します。
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 7e6ab90010d4379c1640f73a8deeba874e601daf
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513810"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files のメトリック

Azure NetApp Files では、割り当て済みストレージ、実際のストレージ使用量、ボリュームの IOPS、および待機時間に関するメトリックが提供されます。 これらのメトリックを分析することで、NetApp アカウントの使用パターンとボリュームのパフォーマンスの理解を深めることができます。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量プールの使用状況メトリック

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- "*ボリュームに割り当てられたプールのサイズ*"  
    特定の容量プールのボリューム クォータの合計 (GiB) です (つまり、容量プールでのボリュームのプロビジョニング済みサイズの合計)。  
    このサイズは、ボリュームの作成中に選択したサイズです。  
- "*プールで消費されたサイズ*"  
    容量プールのボリューム全体で使用された論理領域の合計 (GiB) です。  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>ボリュームの使用状況メトリック

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- "*ボリュームで消費されたサイズ*"   
    ボリュームで使用された論理領域の合計 (GiB) です。  
    このサイズには、アクティブなファイル システムとスナップショットで使用される論理領域が含まれます。  
- "*ボリューム スナップショットのサイズ*"   
   ボリューム内のスナップショットで使用される増分の論理領域です。  

## <a name="performance-metrics-for-volumes"></a>ボリュームのパフォーマンス メトリック

- *AverageReadLatency*   
    ボリュームからの読み取りの平均時間 (ミリ秒) です。
- *AverageWriteLatency*   
    ボリュームからの書き込みの平均時間 (ミリ秒) です。
- *ReadIops*   
    ボリュームへの 1 秒あたりの読み取り数です。
- *WriteIops*   
    ボリュームへの 1 秒あたりの書き込み数です。

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
