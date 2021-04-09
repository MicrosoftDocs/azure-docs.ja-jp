---
title: Azure DDoS Protection Standard によって軽減される攻撃の種類
description: Azure DDoS Protection Standard によって、どの種類の攻撃から保護されるかについて説明します。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8b213755aeed1590df3d1f5e91f44796c13c7711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94991776"
---
# <a name="types-of-ddos-attacks-overview"></a>DDoS 攻撃の種類の概要

DDoS Protection Standard は、次の種類の攻撃を軽減できます。

- **帯域幅消費型攻撃**: これらの攻撃は、膨大な量の一見正当なトラフィックをネットワーク層に送り込みます。 これには、UDP フラッド、増幅フラッド、およびその他の偽装されたパケットのフラッドが含まれます。 DDoS Protection Standard は、Azure のグローバル ネットワーク スケールを自動的に使用し、この潜在的に数ギガバイトに及ぶ攻撃を吸収して除去し、これらの攻撃を軽減します。
- **プロトコル攻撃**: これらの攻撃は、レイヤー 3 とレイヤー 4 のプロトコル スタック内の弱点を悪用して、ターゲットをアクセス不可にします。 これには、SYN フラッド攻撃、リフレクション攻撃、およびその他のプロトコル攻撃が含まれます。 DDoS Protection Standard は、クライアント側と対話して悪意のあるトラフィックと正当なトラフィックを区別し、悪意のあるトラフィックをブロックしてこれらの攻撃を軽減します。 
- **リソース (アプリケーション) レイヤー攻撃**: これらの攻撃は、ホスト間のデータ転送を妨害するために Web アプリケーション パケットをターゲットにします。 これには、HTTP プロトコル違反、SQL インジェクション、クロスサイト スクリプティング、およびその他のレイヤー 7 攻撃が含まれます。 Azure [Application Gateway Web アプリケーション ファイアウォール](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)などの Web アプリケーション ファイアウォールを DDoS Protection Standard と共に使用することにより、これらの攻撃に対する防御が提供されます。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) ではサードパーティの Web アプリケーション ファイアウォール製品も入手できます。

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

DDoS Protection Standard は、仮想マシン、ロード バランサー、アプリケーション ゲートウェイに関連付けられたパブリック IP アドレスを含む、仮想ネットワーク内のリソースを保護します。 Application Gateway Web アプリケーション ファイアウォール、またはパブリック IP がある仮想ネットワークにデプロイされたサードパーティー製の Web アプリケーション ファイアウォールと組み合わせれば、DDoS Protection Standard は、レイヤー 3 からレイヤー 7 までの完全なリスク軽減機能を提供できます。

## <a name="next-steps"></a>次のステップ

- [DDoS 保護プランの作成](manage-ddos-protection.md)方法について説明します。