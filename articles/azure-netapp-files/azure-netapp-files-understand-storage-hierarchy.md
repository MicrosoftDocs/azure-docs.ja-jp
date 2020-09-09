---
title: Azure NetApp Files のストレージ階層とは | Microsoft Docs
description: Azure NetApp Files のアカウント、容量プール、ボリュームを含むストレージ階層について説明します。
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
ms.topic: overview
ms.date: 08/10/2020
ms.author: b-juche
ms.openlocfilehash: 91fecbc68efec1adcee9a2c4013dea46f6da86af
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066476"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp Files のストレージ階層とは

Azure NetApp Files のボリュームを作成する前に、プロビジョニング容量のプールを購入して設定する必要があります。  容量プールを設定するには、NetApp アカウントが必要です。 ストレージ階層についての理解は、Azure NetApp Files のリソースを設定および管理するうえで役立ちます。

> [!IMPORTANT] 
> Azure NetApp Files は現在、サブスクリプション間でのリソースの移行をサポートしていません。

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp アカウント

- NetApp アカウントは、構成容量プールの管理グループとしての役割を果たします。  
- NetApp アカウントは、一般的な Azure Storage アカウントとは異なります。 
- NetApp アカウントは、範囲がリージョン単位となります。   
- 1 つのリージョンで複数の NetApp アカウントを持つことはできますが、個々の NetApp アカウントが関連付けられるリージョンは 1 つだけです。

## <a name="capacity-pools"></a><a name="capacity_pools"></a>容量プール

- 容量プールは、そのプロビジョニング容量で測定されます。  
- 容量は、購入した固定 SKU 単位でプロビジョニングされます (4 TiB 容量など)。
- 容量プールに割り当てることができるサービス レベルは 1 つだけです。  
- 各容量プールが属することができる NetApp アカウントは 1 つだけです。 ただし、1 つの NetApp アカウント内に複数の容量プールを持つことができます。  
- 1 つの容量プールを複数の NetApp アカウントにまたがって移動することはできません。   
  たとえば、下の「[ストレージ階層の概念図](#conceptual_diagram_of_storage_hierarchy)」で、容量プール 1 を米国東部の NetApp アカウントから米国西部 2 の NetApp アカウントへ移動することはできません。  
- 容量プール内のすべてのボリュームが削除されるまで、容量プールを削除できません。

## <a name="volumes"></a><a name="volumes"></a>ボリューム

- ボリュームは、論理容量消費で測定され、スケーラブルです。 
- ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。
- 各ボリュームが従属するプールは 1 つだけですが、1 つのプールは複数のボリュームを含むことができます。 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>ストレージ階層の概念図 
次の例は、Azure サブスクリプション、NetApp アカウント、容量プール、ボリュームの関係を示したものです。   

![ストレージ階層の概念図](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
- [Azure NetApp Files に登録する](azure-netapp-files-register.md)
