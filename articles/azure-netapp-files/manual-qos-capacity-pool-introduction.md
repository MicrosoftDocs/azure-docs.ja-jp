---
title: Azure NetApp Files の手動 QoS 容量プール | Microsoft Docs
description: 手動 QoS 容量プールの概要について説明し、追加情報のための参先を示します。
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581127"
---
# <a name="manual-qos-capacity-pool"></a>手動 QoS 容量プール

この記事では、手動 QoS (サービスの品質) 容量プールの機能の概要について説明します。

## <a name="how-manual-qos-differs-from-auto-qos"></a>手動 QoS と自動 QoS の違い

[QoS の種類](azure-netapp-files-understand-storage-hierarchy.md#qos_types)は、容量プールの属性です。 Azure NetApp Files では、自動 (既定) と手動の 2 種類の QoS 容量プールが提供されます。  

*手動* QoS 容量プールでは、ボリュームの容量とスループットを個別に割り当てることができます。 最小および最大のスループット レベルについては、「[Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md#resource-limits)」を参照してください。 手動 QoS 容量プールで作成されたすべてのボリュームの合計スループットは、プールの合計スループットによって制限されます。 プール サイズとサービス レベルのスループットの組み合わせによって決まります。 

*自動* QoS 容量プールでは、ボリュームに割り当てられたサイズ クォータに比例して、プール内のボリュームに、スループットが自動的に割り当てられます。  

QoS の種類に関する考慮事項については、「[Azure NetApp Files のストレージ階層](azure-netapp-files-understand-storage-hierarchy.md)」と「[Azure NetApp Files のパフォーマンスに関する考慮事項](azure-netapp-files-performance-considerations.md)」を参照してください。

## <a name="example-of-using-manual-qos"></a>手動 QoS の使用例

たとえば SAP HANA システム、Oracle データベース、または複数のボリュームを必要とするその他のワークロードと共に手動 QoS 容量プールを使用するときには、容量プールを使用してこれらのアプリケーションのボリュームを作成できます。  各ボリュームでは、アプリケーションの要件を満たすサイズとスループットを個々に提供できます。  メリットの詳細については、「[手動 QoS 容量プールのボリュームにおけるスループット制限の例](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool)」を参照してください。  

## <a name="how-to-specify-the-manual-qos-type"></a>手動 QoS の種類を指定する方法

[容量プールを作成する](azure-netapp-files-set-up-capacity-pool.md)ときには、容量プールに対して、手動 QoS の種類を使用するように指定できます。  手動 QoS の種類を使用するように[既存の容量プールを変更する](manage-manual-qos-capacity-pool.md#change-to-qos)こともできます。 

容量の種類を手動 QoS に設定することは、永続的な変更です。 種類が手動 QoS である容量プールを、自動 QoS 容量プール変換することはできません。 

手動 QoS の種類を使用するには、[この機能を登録する](manage-manual-qos-capacity-pool.md#register-the-feature)必要があります。  

## <a name="next-steps"></a>次のステップ

* [手動 QoS 容量プールを管理する](manage-manual-qos-capacity-pool.md)
* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
* [ストレージの階層](azure-netapp-files-understand-storage-hierarchy.md) 
* [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
* [Azure NetApp Files のパフォーマンスに関する考慮事項](azure-netapp-files-performance-considerations.md)
* [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)
* [デュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)
* [Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)
* [容量プールに関する問題のトラブルシューティング](troubleshoot-capacity-pools.md)
