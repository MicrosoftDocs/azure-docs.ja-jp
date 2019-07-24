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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672578"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files のメトリック

Azure NetApp Files では、割り当て済みストレージ、実際のストレージ使用量、ボリュームのスループット、IOPS、および待機時間に関するメトリックが提供されます。 これらのメトリックを分析することで、NetApp アカウントの使用パターンとボリュームのパフォーマンスの理解を深めることができます。  

## <a name="capacity_pools"></a>容量プールの使用状況メトリック

- *ボリューム プールの割り当てサイズ*  
    これは、プロビジョニングされた容量プールのサイズ (GiB) です。  
- *ボリューム プールの使用済みの割り当て*  
    これは、特定の容量プールのボリューム クォータ (GiB) の合計です (つまり、容量プールでのボリュームのプロビジョニング済みサイズの合計)。 これは、ボリュームの作成中に選択したサイズです。  
- *ボリューム プールの論理サイズの合計*  
    これは、容量プールのボリューム全体で使用される論理領域 (GiB) の合計です。  
- *ボリューム プールのスナップショット サイズの合計*  
    これは、スナップショットで使用される増分の論理領域の合計です。  

## <a name="volumes"></a>ボリュームの使用状況メトリック

- *ボリュームの割り当てサイズ*   
    これは、プロビジョニングされたボリュームのサイズ (クォータ) です (GiB)。  
- *ボリュームの論理サイズ*   
    これは、ボリュームで使用される論理領域の合計です (GiB)。 このサイズには、アクティブなファイル システムとスナップショットで使用される論理領域が含まれます。  
- *ボリューム スナップショットのサイズ*   
    これは、ボリューム内のスナップショットで使用される増分の論理領域です。  

## <a name="next-steps"></a>次の手順

* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
