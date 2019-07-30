---
title: 仮想ネットワークのビジネス継続性 | Microsoft Docs
description: Azure Virtual Networks に影響を与える Azure サービスの中断が発生した場合の対処方法について説明します。
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876169"
---
# <a name="virtual-network--business-continuity"></a>Virtual Network – ビジネス継続性

## <a name="overview"></a>概要
Virtual Network (VNet) は、クラウド内のユーザーのネットワークを論理的に表したものです。 これにより、独自のプライベート IP アドレス空間を定義し、ネットワークをサブネットに分割することができます。 VNet は、Azure Virtual Machines や Cloud Services (web/worker ロール) などのコンピューティング リソースをホストする際に信頼の境界として機能します。 VNet の内部にホストされているリソースどうしは、プライベート IP で直接通信できます。 仮想ネットワークは、VPN Gateway または ExpressRoute を介してオンプレミス ネットワークにリンクできます。

VNet は、1 つのリージョンの範囲内に作成します。 同じアドレス空間を持つ VNet を異なる 2 つのリージョン (たとえば米国東部と米国西部) に*作成*できますが、2 つのアドレス空間が同じであるため、2 つを互いに接続することはできません。 

## <a name="business-continuity"></a>ビジネス継続性

アプリケーションが中断される原因はさまざまです。 リージョンで、自然災害によりネットワークが完全に切断される可能性や、いくつかのデバイスやサービスの障害により部分的に障害が発生する可能性があります。 VNet サービスへの影響は、これらの状況によって異なります。

**Q:リージョン全体の機能が停止した場合、どうすればよいでしょうか?たとえば、自然災害によりリージョンが完全に切り離されたとします。そのリージョンでホストされている仮想ネットワークはどうなりますか?**

A:サービスが中断されている間は、影響を受けたリージョン内の仮想ネットワークとリソースにアクセスできなくなります。

![単純な Virtual Network の図](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q:別のリージョンで同じ仮想ネットワークを再作成するには、どうすればよいですか。**

A:仮想ネットワークは、非常に軽量なリソースです。 Azure API を呼び出して、別のリージョンに同じアドレス空間を持つ VNet を作成することができます。 影響を受けたリージョンに存在していたのと同じ環境を再作成するには、API を呼び出して、Cloud Services の web ロールと worker ロールおよび仮想マシンを再デプロイします。 また、オンプレミス接続 (ハイブリッド デプロイなど) がある場合は、新しい VPN Gateway をデプロイし、オンプレミスのネットワークに接続する必要があります。

仮想ネットワークを作成するには、「[Create a virtual network](manage-virtual-network.md#create-a-virtual-network)(仮想ネットワークを作成する) をご覧ください。

**Q:特定のリージョンの VNet のレプリカを別のリージョンに前もって再作成できますか。**

A:はい。同じプライベート IP アドレス空間とリソースを使用して、2 つの異なるリージョンに 2 つの VNet を前もって作成できます。 VNet 内でインターネットに接続するサービスをホストしていた場合は、Traffic Manager をセットアップして、アクティブなリージョンにトラフィックを地理的に分散させることができます。 ただし、ルーティングの問題の原因となるため、同じアドレス空間を持つ 2 つの VNet をオンプレミスのネットワークに接続することはできません。 一方のリージョンで災害が発生し、VNet が失われた場合は、アドレス空間が一致する利用可能なリージョンのもう一方の VNet をオンプレミスのネットワークに接続できます。

仮想ネットワークを作成するには、「[Create a virtual network](manage-virtual-network.md#create-a-virtual-network)(仮想ネットワークを作成する) をご覧ください。

