---
title: "Azure 仮想ネットワークのアフィニティ グループからリージョンへの移行 | クラシック | Microsoft Docs"
description: "仮想ネットワークをアフィニティ グループからリージョンに移行する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: f74379c3e310a7bbcf659ef610727bff19639022
ms.openlocfilehash: c495af3e818758cc5fe99af9b5f07506a16b59ef


---
# <a name="how-to-migrate-a-virtual-network-from-an-affinity-group-to-a-region"></a>仮想ネットワークをアフィニティ グループからリージョンに移行する方法
アフィニティ グループを使用すると、同じアフィニティ グループ内で作成したリソースが、近距離にある複数サーバーによって物理的にホストされるため、これらのリソースはより迅速に通信できるようになります。 これまでアフィニティ グループは、Virtual Network (VNet) を作成するための要件でした。 当時は、VNet を管理するネットワーク マネージャー サービスが機能できたのは、一連の物理サーバーまたはスケール ユニット内のみでした。 アーキテクチャの改善により、ネットワーク管理のスコープがリージョンに拡大しました。

このようなアーキテクチャの機能強化により、アフィニティ グループは仮想ネットワークの推奨要件や必須要件ではなくなりました。 VNet で使用されていたアフィニティ グループは、リージョンに置き換わりつつあります。 リージョンに関連付けられている VNets は、リージョン VNet と呼ばれます。

また、通常はアフィニティ グループを使用しないことが推奨されます。 VNet の要件とは別に、以前はコンピューティングおよびストレージなどのリソースが互いに近くに配置されるよう、アフィニティ グループを使用することも重要でした。 しかし、現在の Azure のネットワーク アーキテクチャでは、このような配置要件は必要なくなりました。 アフィニティ グループを使用する必要のあるその他の特殊な場合については、 [アフィニティ グループと VM](#Affinity-groups-and-VMs) を参照してください。

## <a name="creating-and-migrating-to-regional-vnets"></a>リージョン VNet の作成と移行
今後、新しい VNet を作成するときは *リージョン*を使用します。 これは、管理ポータルでオプションとして表示されます。 ネットワーク構成ファイルでは *Location*と表示されます。

> [!IMPORTANT]
> 仮想ネットワークをアフィニティ グループに関連付けて作成することは技術的には引き続き可能ですが、これを行うだけの理由はありません。 ネットワーク セキュリティ グループなど多くの新機能は、リージョン VNet を使用する場合にのみ利用可能で、アフィニティ グループに関連付けられた仮想ネットワークでは利用できません。
> 
> 

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>現在アフィニティ グループに関連付けられている VNet について
現在アフィニティ グループに関連付けられている VNet は、リージョン VNet に移行できます。 リージョン VNet に移行するには、次の手順に従います。

1. ネットワーク構成ファイルをエクスポートします。 PowerShell または管理ポータルを使用できます。 管理ポータルの使用方法については、 [ネットワーク構成ファイルを使用した VNet の構成](virtual-networks-using-network-configuration-file.md)を参照してください。
2. ネットワーク構成ファイルを編集して、古い値を新しい値に置き換えます。 
   
   > [!NOTE]
   > **Location** は、VNet に関連付けられたアフィニティ グループに対して指定したリージョンです。 たとえば、VNet が米国西部に位置するアフィニティ グループに関連付けられている場合、移行するときに、この Location が米国西部 (West US) をポイントする必要があります。 
   > 
   > 
   
    ネットワーク構成ファイルの次の行を編集して、値を置き換えます。 
   
    **古い値:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **新しい値:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. 変更を保存し、ネットワーク構成を Azure に [インポート](virtual-networks-using-network-configuration-file.md) します。

> [!NOTE]
> この移行では、サービスにダウンタイムは発生しません。
> 
> 

## <a name="affinity-groups-and-vms"></a>アフィニティ グループと VM
前述のように、一般的に VM ではアフィニティ グループは推奨されなくなりました。 一連の VM 間のネットワーク レイテンシ待ち時間を最小に抑える必要がある場合にのみ、アフィニティ グループを使用してください。 アフィニティ グループに VM を配置すると、すべての VM が同じコンピューティング クラスターまたはスケール ユニットに配置されることになります。

アフィニティ グループを使用する場合は、次の&2; つの (おそらくはマイナスの) 結果が生じる可能性があります。

* 一連の VM サイズは、コンピューティング スケール ユニットによって提供される一連の VM サイズに制限されます。
* 新しい VM を割り当てられない可能性が高くなります。 これは、アフィニティ グループの特定のスケール ユニットが容量を使い切った場合に発生します。

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>アフィニティ グループに VM がある場合の対処方法
現在アフィニティ グループ内にある VM を、アフィニティ グループから削除する必要はありません。

VM をデプロイすると、単一のスケール ユニットにデプロイされます。 アフィニティ グループは、新しい VM のデプロイの際に利用できる一連の VM サイズを制限できますが、デプロイ済みの既存の VM は、VM がデプロイされているスケール ユニット内で使用できる VM サイズに既に制限されています。 このため、アフィニティ グループから VM を削除しても影響はありません。




<!--HONumber=Feb17_HO1-->


