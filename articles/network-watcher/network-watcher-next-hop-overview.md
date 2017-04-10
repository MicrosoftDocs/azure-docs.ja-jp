---
title: "Azure Network Watcher の次ホップの概要 | Microsoft Docs"
description: "このページでは、Network Watcher の次ホップ機能の概要を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 864185e62fb6c3cef4116824b36ee7e5d3447662
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Azure Network Watcher の次ホップの概要

VM からのトラフィックは、NIC に関連付けられた有効なルートをもとに、送信先に送信されます。 次ホップは、特定の仮想マシンと NIC から次ホップの種類とパケットの IP アドレスを取得します。 これにより、パケットが送信先に向かっているのか、またはトラフィックが失われているのかを判断するのに役立ちます。 ユーザーが不適切なルートを構成して、トラフィックがオンプレミスの場所または仮想アプライアンスに向けられると、接続に関する問題が起こります。 また、次ホップは関連するルート テーブルを返します。 ルートがユーザー定義のルートとして定義されている場合に次ホップをクエリすると、そのルートが返されます。 それ以外の場合、次ホップは "システム ルート" を返します。

![次ホップの概要][1]

次ホップをクエリするときに返される次ホップの種類の一覧は、以下のとおりです。

* インターネット
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* なし

### <a name="next-steps"></a>次のステップ

[VM 上の次ホップの確認](network-watcher-check-next-hop-portal.md)に関する記事にアクセスして、次ホップを使用してネットワーク接続に関する問題を検出する方法を確認する

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png














