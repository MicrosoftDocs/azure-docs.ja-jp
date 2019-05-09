---
title: Azure NetApp Files のリソース制限 | Microsoft Docs
description: NetApp アカウント、容量プール、ボリューム、スナップショット、委任されたサブネットの制限など、Azure NetApp Files のリソース制限について説明します。
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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668160"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files のリソース制限

Azure NetApp Files のリソース制限を理解すると、ボリュームの管理に役立ちます。

- Azure サブスクリプションごとに、最大 10 個の NetApp アカウントを作成できます。
- NetApp アカウントごとに、最大 25 個の容量プールを作成できます。
- 各容量プールが属することができる NetApp アカウントは 1 つだけです。  
- 1 つの容量プールの最小サイズは 4 TiB で、最大サイズは 500 TiB です。 
- 各容量プールで、最大 500 個のボリュームを保持できます。
- 1 つのボリュームの最小サイズは 100 GiB で、最大サイズは 92 TiB です。
- 各ボリュームに、最大 255 個のスナップショットを格納できます。
- 各 Azure 仮想ネットワーク (VNet) で、1 つのサブネットだけを Azure NetApp Files に委任できます。

**次のステップ**

[Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
