---
title: Azure Load Balancer のトラブルシューティング
description: Azure Load Balancer に関する既知の問題のトラブルシューティングを行う方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029150"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Azure Load Balancer のバックエンド トラフィック応答に関するトラブルシューティング

このページには、Azure Load Balancer に関する質問のトラブルシューティング情報が記載されています。

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Load Balancer の背後にある VM が構成済みデータ ポートのトラフィックに応答しない

バックエンド プール VM が正常として表示され、正常性プローブに応答するにもかかわらず、負荷分散に参加していない、またはデータ トラフィックに応答していない場合は、次のいずれかが原因である可能性があります。
* Load Balancer バックエンド プール VM がデータ ポートでリッスンしていない
* ネットワーク セキュリティ グループが Load Balancer バックエンド プール VM 上のポートをブロックしている  
* 同じ VM と NIC から Load Balancer にアクセスしている
* 参加している Load Balancer バックエンド プール VM からインターネット Load Balancer フロントエンドにアクセスしている

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>原因 1:Load Balancer バックエンド プール VM がデータ ポートでリッスンしていない

VM がデータ トラフィックに応答しない場合、その原因としては、参加している VM でターゲット ポートが開いていない、または VM がそのポートでリッスンしていないことが考えられます。 

**検証と解決策**

1. バックエンド VM にログインします。 
2. コマンド プロンプトを開き、次のコマンドを実行して、データ ポートでリッスンしているアプリケーションが存在しているかどうかを検証します。  
            netstat -an 
3. そのポートが "LISTENING" 状態で表示されていない場合は、適切なリスナー ポートを構成します 
4. ポートが "リッスン中" としてマークされている場合は、そのポートのターゲット アプリケーションに問題がないかどうかを確認します。

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>原因 2:ネットワーク セキュリティ グループが Load Balancer バックエンド プール VM 上のポートをブロックしている  

サブネットまたは VM で構成されている 1 つ以上のネットワーク セキュリティ グループが、発信元 IP またはポートをブロックしている場合、VM は応答できません。

パブリック ロード バランサーの場合、クライアントとロード バランサーのバックエンド VM の間の通信には、インターネット クライアントの IP アドレスが使用されます。 クライアントの IP アドレスがバックエンド VM のネットワーク セキュリティ グループで許可されていることを確認してください。

1. バックエンド VM で構成されているネットワーク セキュリティ グループを一覧表示します。 詳しくは、[ネットワーク セキュリティ グループの管理](../virtual-network/manage-network-security-group.md)に関する記事をご覧ください。
1. ネットワーク セキュリティ グループの一覧で、次を確認します。
    - データ ポートの受信または送信トラフィックで干渉が発生していないかどうか。 
    - サブネットまたは VM の NIC で、**すべて拒否** ネットワーク セキュリティ グループ ルールの優先順位が、Load Balancer プローブとトラフィックを許可する既定のルールよりも高くなっていないかどうか (ネットワーク セキュリティ グループでは、プローブ ポートである Load Balancer IP アドレス 168.63.129.16 が許可されている必要があります)
1. ルールのいずれかがトラフィックをブロックしている場合は、そのルールを削除し、データ トラフィックを許可するように再構成します。  
1. VM が正常性プローブに応答するようになったかどうかをテストします。

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>原因 3:同じ VM とネットワーク インターフェイスから Load Balancer にアクセスしている 

Load Balancer のバックエンド VM でホストされているアプリケーションが、同じネットワーク インターフェイスを介して同じバックエンド VM でホストされている別のアプリケーションにアクセスしようとすると、失敗します。このシナリオはサポートされていません。 

**解決策**: この問題を解決するには、次のいずれかの方法を使用します。
* アプリケーションごとに個別のバックエンド プール VM を構成する。 
* 各アプリケーションが独自のネットワーク インターフェイスと IP アドレスを使用していたように、デュアル NIC VM でアプリケーションを構成する。 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>原因 4:参加している Load Balancer バックエンド プール VM から内部 Load Balancer フロントエンドにアクセスしている

内部 Load Blancer が VNet 内で構成され、参加しているバックエンド VM の 1 つが内部 Load Balancer フロントエンドにアクセスしようとしている場合は、エラーが発生し、アクセス元の VM にフローがマップされます。 このシナリオはサポートされません。

**解決策**: このシナリオをブロック解除するには、プロキシの使用などいくつかの方法があります。 Application Gateway または他のサードパーティ製プロキシ (nginx や haproxy など) を評価してください。 Application Gateway の詳細については、「[Application Gateway の概要](../application-gateway/overview.md)」を参照してください

**詳細**: 内部ロード バランサーは、送信接続を内部ロード バランサーのフロントエンドに変換しません。これは、両方ともプライベート IP アドレス空間にあるためです。 パブリック ロード バランサーは、仮想ネットワーク内のプライベート IP アドレスからパブリック IP アドレスへの[送信接続](load-balancer-outbound-connections.md)を提供します。 内部ロード バランサーの場合、この方法を使用すると、変換が必要ない固有内部 IP アドレス空間内で SNAT ポートの枯渇が発生する可能性が回避されます。

副作用として、バックエンド プール内の VM からの送信フローが、そのプール内の内部ロード バランサーのフロントエンドへのフローを試み、"_かつ_"、それ自体にマップバックされている場合、フローの 2 つのレッグは一致しません。 これらは一致しないため、フローは失敗します。 フローが、フロントエンドへのフローを作成したバックエンド プール内の同じ VM にマップバックしなかった場合、フローは成功します。

フローがそれ自体にマップバックする場合、送信フローは VM からフロントエンドに発信されるように見え、対応する受信フローは VM からそれ自体に発信されるように見えます。 ゲスト オペレーティング システムの観点からは、同じフローの受信部分と送信部分は、仮想マシン内と一致しません。 TCP スタックは、同じフローのこれらの半分を、同じフローの一部と認識しません。 送信元と送信先が一致しないためです。 フローがバックエンド プール内の他の VM にマップする場合、フローの半分は一致し、VM はフローに応答できます。

このシナリオの症状は、フローがその発生元と同じバックエンドに返されるときに発生する断続的な接続のタイムアウトです。 一般的な回避策としては、内部ロード バランサーの背後にプロキシ レイヤーを挿入し、Direct Server Return (DSR) スタイル ルールを使用することなどが挙げられます。 詳細については、「[Azure Load Balancer の複数のフロントエンド](load-balancer-multivip-overview.md)」を参照してください。

ユーザーは、内部ロード バランサーを任意のサード パーティ製プロキシと組み合わせるか、HTTP/HTTPS を使用したプロキシ シナリオに内部の [Application Gateway](../application-gateway/overview.md) を使用することができます。 この問題はパブリック ロード バランサーを使って軽減できますが、結果として得られるシナリオでは [SNAT の枯渇](load-balancer-outbound-connections.md)が発生しやすくなります。 慎重に管理しない限り、この 2 番目のアプローチは避けてください。

## <a name="next-steps"></a>次のステップ

前の手順で問題を解決できない場合は、 [サポート チケット](https://azure.microsoft.com/support/options/)を開きます。