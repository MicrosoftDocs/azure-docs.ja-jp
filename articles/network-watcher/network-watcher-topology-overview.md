---
title: "Azure Network Watcher のトポロジの概要 | Microsoft Docs"
description: "このページでは、Network Watcher のトポロジ機能の概要を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fa79ab0ab80481c8969c6fa653e92b10985cab3b
ms.openlocfilehash: 42443f614b76b8180ac163b9889163021adbf048
ms.lasthandoff: 03/31/2017

---

# <a name="introduction-to-topology-in-azure-network-watcher"></a>Azure Network Watcher のトポロジの概要

トポロジは、仮想ネットワーク内のネットワーク リソースの図式を返します。 図式は、エンド ツー エンドのネットワーク接続を表すリソース間の相互接続を示します。

![トポロジの概要][1]

ポータルで、トポロジは仮想ネットワークごとにリソース オブジェクトを返します。 リレーションシップがリソース間の線で表されています。Network Watcher リージョンの外部のリソースは、リソース グループに含まれていても表示されません。 ポータルのビューに返されるリソースは、図式化されたネットワーク コンポーネントのサブセットです。 すべてのネットワーク リソースの一覧を表示するには、[PowerShell](network-watcher-topology-powershell.md) または [REST](network-watcher-topology-rest.md) を使用します。

> [!NOTE]
> トポロジを実行するリージョンごとに、Network Watcher のインスタンスが必要になります。

リソースが返されるときに、2 つのリレーションシップに基づいてリソース間の接続がモデル化されます。

- **含有** - 例: 仮想ネットワークにサブネットが含まれ、サブネットに NIC が含まれる
- **関連付け** - 例: NIC が VM に関連付けられている

### <a name="next-steps"></a>次のステップ

[Network Watcher トポロジと PowerShell](network-watcher-topology-powershell.md) に関する記事を参照し、PowerShell を使用してトポロジ ビューを取得する方法を確認する

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png

