---
title: Azure NetApp Files のメトリック | Microsoft Docs
description: Azure NetApp Files のメトリックについて説明します。
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848789"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files のメトリック

Azure NetApp Files では、割り当て済みストレージ、実際のストレージ使用量、ボリュームのスループット、IOPS、および待機時間に関するメトリックが提供されます。 これらのメトリックを分析することで、NetApp アカウントの使用パターンとボリュームのパフォーマンスの理解を深めることができます。  

## <a name="capacity_pools"></a>容量プールの使用状況メトリック

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *ボリューム プールの使用済みの割り当て*  
    特定の容量プールのボリューム クォータの合計 (GiB) です (つまり、容量プールでのボリュームのプロビジョニング済みサイズの合計)。  
    これは、ボリュームの作成中に選択したサイズです。  
- *ボリューム プールの論理サイズの合計*  
    容量プールのボリューム全体で使用される論理領域の合計 (GiB) です。  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>ボリュームの使用状況メトリック

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *ボリュームの論理サイズ*   
    ボリュームで使用される論理領域の合計 (GiB) です。  
    このサイズには、アクティブなファイル システムとスナップショットで使用される論理領域が含まれます。  
- *ボリューム スナップショットのサイズ*   
   ボリューム内のスナップショットで使用される増分の論理領域です。  

## <a name="performance-metrics-for-volumes"></a>ボリュームのパフォーマンス メトリック

- *AverageReadLatency*   
    ボリュームからの読み取りの平均時間 (ミリ秒)
- *AverageWriteLatency*   
    ボリュームからの書き込みの平均時間 (ミリ秒)
- *ReadIops*   
    ボリュームへの 1 秒あたりの読み取り数
- *WriteIops*   
    ボリュームへの 1 秒あたりの書き込み数

## <a name="next-steps"></a>次の手順

* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
