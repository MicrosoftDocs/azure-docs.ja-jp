---
title: Azure 仮想ネットワーク (クラシック) をアフィニティ グループからリージョンに移行する | Microsoft Docs
description: 仮想ネットワーク (クラシック) をアフィニティ グループからリージョンに移行する方法について説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648637"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>仮想ネットワーク (クラシック) をアフィニティ グループからリージョンに移行する

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは Resource Manager デプロイ モデルを使用することをお勧めします。

アフィニティ グループを使うと、同じアフィニティ グループ内で作成したリソースが、近距離にある複数サーバーによって物理的にホストされるため、これらのリソースはより迅速に通信できるようになります。 これまでアフィニティ グループは、仮想ネットワーク (クラシック) を作成するための要件でした。 当時は、仮想ネットワーク (クラシック) を管理するネットワーク マネージャー サービスが機能できたのは、一連の物理サーバーまたはスケール ユニット内のみでした。 アーキテクチャの改善により、ネットワーク管理のスコープがリージョンに拡大しました。

このようなアーキテクチャの機能強化により、アフィニティ グループは仮想ネットワーク (クラシック) の推奨要件や必須要件ではなくなりました。 仮想ネットワーク (クラシック) に対するアフィニティ グループの使用は、リージョンによって置き換えられます。 リージョンに関連付けられた仮想ネットワーク (クラシック) は、リージョン仮想ネットワークと呼ばれます。

一般に、アフィニティ グループを使わないことが推奨されます。 仮想ネットワークの要件とは別に、以前はコンピューティング (クラシック) およびストレージ (クラシック) などのリソースが互いに近くに配置されるよう、アフィニティ グループを使用することも重要でした。 しかし、現在の Azure のネットワーク アーキテクチャでは、このような配置要件は必要なくなりました。

> [!IMPORTANT]
> 仮想ネットワークをアフィニティ グループに関連付けて作成することは技術的には引き続き可能ですが、これを行うだけの理由はありません。 ネットワーク セキュリティ グループなど、仮想ネットワークの多くの機能は、リージョン仮想ネットワークを使う場合にのみ利用可能で、アフィニティ グループに関連付けられた仮想ネットワークでは利用できません。
> 
> 

## <a name="edit-the-network-configuration-file"></a>ネットワーク構成ファイルを編集する

1. ネットワーク構成ファイルをエクスポートします。 PowerShell または Azure コマンド ライン インターフェイス (CLI) 1.0 を使ってネットワーク構成ファイルをエクスポートする方法については、「[ネットワーク構成ファイルを使用した仮想ネットワークの構成](virtual-networks-using-network-configuration-file.md#export)」をご覧ください。
2. ネットワーク構成ファイルを編集して、**AffinityGroup** を **Location** に置き換えます。 **Location** には、Azure の[リージョン](https://azure.microsoft.com/regions)を指定します。
   
   > [!NOTE]
   > **Location** は、仮想ネットワーク (クラシック) に関連付けられたアフィニティ グループに対して指定したリージョンです。 たとえば、仮想ネットワーク (クラシック) が米国西部に位置するアフィニティ グループに関連付けられている場合、移行するときに、この **Location** が米国西部 (West US) をポイントする必要があります。 
   > 
   > 
   
    ネットワーク構成ファイルの次の行を編集して、値を置き換えます。 
   
    **古い値:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **新しい値:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. 変更を保存し、ネットワーク構成を Azure に [インポート](virtual-networks-using-network-configuration-file.md#import) します。

> [!NOTE]
> この移行では、サービスにダウンタイムは発生しません。
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>アフィニティ グループに VM (クラシック) がある場合の対処方法
現在アフィニティ グループ内にある VM (クラシック) を、アフィニティ グループから削除する必要はありません。 VM をデプロイすると、単一のスケール ユニットにデプロイされます。 アフィニティ グループは、新しい VM のデプロイの際に利用できる一連の VM サイズを制限できますが、デプロイ済みの既存の VM は、VM がデプロイされているスケール ユニット内で使用できる VM サイズに既に制限されています。 VM はスケール ユニットに既にデプロイされているため、VM をアフィニティ グループから削除しても VM に影響はありません。
