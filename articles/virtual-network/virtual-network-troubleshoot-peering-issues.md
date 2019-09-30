---
title: 仮想ネットワーク ピアリングの問題をトラブルシューティングする
description: ほとんどの仮想ネットワーク ピアリングの問題を解決するのに役立つ手順。
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 68d4f55d4a382f59386e72779a5f60cfc2a65338
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091112"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>仮想ネットワーク ピアリングの問題をトラブルシューティングする

このトラブルシューティング ガイドでは、ほとんどの[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)の問題の解決に役立つ手順を提供します。

![イメージ](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>シナリオ 1:2 つの仮想ネットワーク間に仮想ネットワーク ピアリングを構成する

仮想ネットワークが同じサブスクリプションまたは異なるサブスクリプションにありますか?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>接続の種類 1:仮想ネットワークが同じサブスクリプション内にある

同じサブスクリプションにある仮想ネットワークの仮想ネットワーク ピアリングを構成するには、必要に応じて、次の記事で提供されている方法を使用します。

* 仮想ネットワークが**同じリージョン**にある場合は、[同じサブスクリプション内の仮想ネットワークのピアリングを作成する](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)手順に従います。
* 仮想ネットワークが**異なるリージョン**にある場合は、[グローバル仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を設定する手順に従います。  

> [!Note]
> 次のリソースについては、グローバル VNet ピアリング経由での接続が機能しません。 
>
> * Basic ILB SKU の背後にある VM
> * Redis Cache (Basic ILB SKU を使用)
> * Application Gateway (Basic ILB SKU を使用)
> * スケール セット (Basic ILB SKU を使用)
> * Service Fabric クラスター (Basic ILB SKU を使用)
> * SQL Always-on (Basic ILB SKU を使用)
> * App Service Environment (ASE) (Basic ILB SKU を使用)
> * API Management (Basic ILB SKU を使用)
> * Azure Active Directory Domain Service (ADDS) (Basic ILB SKU を使用)

詳細については、グローバル ピアリングの「[要件と制約](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)」を参照してください。

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>接続の種類 2:仮想ネットワークが異なるサブスクリプションまたは AD テナントにある

異なるサブスクリプションまたは Active Directory テナント内の仮想ネットワークの仮想ネットワーク ピアリングを構成するには、[Azure CLI の異なるサブスクリプションでのピアリングの作成](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)に関するページの手順に従います。

> [!Note]
> ネットワーク ピアリングを構成するには、両方のサブスクリプションで**ネットワーク共同作成者**のアクセス許可を持っている必要があります。 詳細については、[ピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページを参照してください。

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>シナリオ 2:オンプレミス リソースを使用するハブスポーク トポロジで仮想ネットワーク ピアリングを構成する

![イメージ](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>接続の種類 1:サイト間接続または ExpressRoute 接続の場合

次の手順に従います: [仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。

### <a name="connection-type-2-for-point-to-site-connections"></a>接続の種類 2:ポイント対サイト接続の場合

1. 次の手順に従います: [仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。
2. 仮想ネットワーク ピアリングが確立または変更された後、ポイント対サイト クライアントでスポーク仮想ネットワークへの更新されたルートを取得するためには、ポイント対サイト パッケージをもう一度ダウンロードしてインストールする必要があります。

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>シナリオ 3:Azure Virtual Network のハブスポーク トポロジを使用して仮想ネットワーク ピアリングを構成する

![イメージ](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>接続の種類 1:仮想ネットワークが同じリージョン内にある

ハブ仮想ネットワークでネットワーク仮想アプライアンス (NVA) を構成し、スポーク仮想ネットワークで次ホップの "ネットワーク仮想アプライアンス" が適用されたユーザー定義のルートを設定する必要があります。 詳細については、「[サービス チェイニング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)」を参照してください。

> [!Note]
> NVA の設定に関するヘルプが必要な場合は、[NVA のベンダーにお問い合わせください](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

NVA デバイスの設定とルーティングのトラブルシューティングに関するヘルプについては、「[Azure でのネットワーク仮想アプライアンスの問題](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)」を参照してください。

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>接続の種類 2:仮想ネットワークが異なるリージョン内にある

グローバル VNet ピアリング経由の転送がサポートされるようになりました。 次のリソースについては、グローバル VNet ピアリング経由での接続が機能しません。

* Basic ILB SKU の背後にある VM
* Redis Cache (Basic ILB SKU を使用)
* Application Gateway (Basic ILB SKU を使用)
* スケール セット (Basic ILB SKU を使用)
* Service Fabric クラスター (Basic ILB SKU を使用)
* SQL Always-on (Basic ILB SKU を使用)
* App Service Environment (ASE) (Basic ILB SKU を使用)
* API Management (Basic ILB SKU を使用)
* Azure Active Directory Domain Service (ADDS) (Basic ILB SKU を使用)

グローバル ピアリングの要件と制約の詳細については、「[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)」を参照してください。

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>シナリオ 4:2 つのピアリングされた仮想ネットワーク間で接続の問題が発生している

必要な[ロールとアクセス許可](virtual-network-manage-peering.md#permissions)があるアカウントで、[Azure portal](https://portal.azure.com/) にサインインします。 仮想ネットワークを選択して、 **[ピアリング]** を選び、 **[状態]** フィールドを確認します。 状態はどうなっていますか?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>接続の種類 1:ピアリングの状態が [接続済み] と表示されている

この問題のトラブルシューティングを行うには、これらの手順に従います。

1. ネットワーク トラフィック フローを確認する:

   [接続のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)と、ソース VM から宛先 VM への [IP フロー検証](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)を使用して、トラフィック フローの干渉の原因となっている NSG または UDR があるかどうかを判断します。

   ファイアウォールまたは NVA アプライアンスを使用している場合は、これらの手順に従います。 
   1. この手順の完了後に復元できるように、UDR パラメーターをドキュメント化します。
   2. 次ホップとして NVA を指すソース VM サブネットまたは NIC から UDR を削除します。 ソース VM から、NVA をバイパスしている宛先への直接の接続を確認します。 この手順が機能する場合は、[NVA のトラブルシューティング ツール](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)に関するページを参照してください。

2. ネットワーク トレースを取得する: 
   1. 宛先 VM でネットワーク トレースを開始します。 Windows の場合は、**Netsh** を使用できます。 Linux の場合は、**TCPDump** を使用します。
   2. ソースから宛先 IP への **TcpPing** または **PsPing** を実行します。

   * これは **TcpPing** コマンドの例です: `tcping64.exe -t <destination VM address> 3389`

   3. **TcpPing** が完了した後、宛先のネットワーク トレースを停止します。
   4. ソースからパケットが到着した場合、ネットワークの問題はありません。 VM ファイアウォールと、そのポートでリッスンしているアプリケーションの両方を調べ、構成の問題を特定します。

   > [!Note]
   > グローバル仮想ネットワーク ピアリング (異なるリージョン内の仮想ネットワーク) 経由では、次の種類のリソースに接続することはできません。
   >
   > * Basic ILB SKU の背後にある VM
   > * Redis Cache (Basic ILB SKU を使用)
   > * Application Gateway (Basic ILB SKU を使用)
   > * スケール セット (Basic ILB SKU を使用)
   > * Service Fabric クラスター (Basic ILB SKU を使用)
   > * SQL Always-on (Basic ILB SKU を使用)
   > * App Service Environment (ASE) (Basic ILB SKU を使用)
   > * API Management (Basic ILB SKU を使用)
   > * Azure Active Directory Domain Service (ADDS) (Basic ILB SKU を使用)

詳細については、グローバル ピアリングの「[要件と制約](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)」を参照してください。

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>接続の種類 2:ピアリングの状態が [切断] と表示されている

両方の VNet からピアリングを削除し、再作成する必要があります。

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>シナリオ 5:ハブスポーク仮想ネットワークとオンプレミス リソースとの間で接続の問題が発生している

サードパーティの NVA または VPN ゲートウェイを使用していますか?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>接続の種類 1:自分のネットワークでサードパーティの NVA または VPN ゲートウェイが使用されている

サードパーティの NVA または VPN ゲートウェイに影響する接続問題のトラブルシューティングを行う場合は、次の記事を参照してください。

* [NVA トラブルシューティング ツール](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [サービス チェーン](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>接続の種類 2:自分のネットワークでサードパーティの NVA や VPN ゲートウェイが使用されていない

ハブとスポーク仮想ネットワークの両方に VPN ゲートウェイがありますか?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>ハブとスポーク仮想ネットワークの両方に VPN ゲートウェイがある

リモート ゲートウェイの使用はサポートされていません。

VNet ピアリングに制限があるため、スポーク VNet に既に VPN ゲートウェイがある場合、スポーク VNet では **[リモート ゲートウェイを使用する]** はサポートされません。

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>ハブとスポーク仮想ネットワークの両方に VPN ゲートウェイがない

サイト間接続または ExpressRoute 接続の場合は、オンプレミスからリモート仮想ネットワークへの接続に関する問題のこれらの主な原因を確認します。

* ゲートウェイがある仮想ネットワークで **[転送されたトラフィックを許可する]** チェック ボックスがオンになっていることを確認します。
* ゲートウェイがない仮想ネットワークで **[リモート ゲートウェイを使用する]** チェック ボックスがオンになっていることを確認します。
* オンプレミス デバイスを調べ、それらすべてにリモート仮想ネットワーク アドレス空間が追加されていることを確認するようにネットワーク管理者に依頼します。

ポイント対サイト接続の場合:

* ゲートウェイがある仮想ネットワークで **[転送されたトラフィックを許可する]** チェック ボックスがオンになっていることを確認します。
* ゲートウェイがない仮想ネットワークで **[リモート ゲートウェイを使用する]** チェック ボックスがオンになっていることを確認します。
* ポイント対サイト クライアント パッケージをもう一度ダウンロードしてインストールします。 新しくピアリングされた仮想ネットワーク ルートでは、ポイント対サイト クライアントにルートが自動的に追加されせん。

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>シナリオ 6:同じリージョン内のスポーク仮想ネットワーク間でハブスポーク ネットワーク接続の問題が発生している

ハブ ネットワークに NVA がある必要があります。また、NVA が次ホップとして設定されているスポークで UDR を構成し、ハブ仮想ネットワークで **[転送されたトラフィックを許可する]** を有効にする必要があります。

詳細については、「[サービス チェイニング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)」を参照し、選択した [NVA ベンダー](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)とこれらの要件について相談してください。

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>シナリオ 7:異なるリージョンのスポーク仮想ネットワーク間でハブスポーク ネットワーク接続の問題が発生している

グローバル VNet ピアリング経由の転送がサポートされるようになりました。 次のリソースについては、グローバル VNet ピアリング経由での接続が機能しません。

* Basic ILB SKU の背後にある VM
* Redis Cache (Basic ILB SKU を使用)
* Application Gateway (Basic ILB SKU を使用)
* スケール セット (Basic ILB SKU を使用)
* Service Fabric クラスター (Basic ILB SKU を使用)
* SQL Always-on (Basic ILB SKU を使用)
* App Service Environment (ASE) (Basic ILB SKU を使用)
* API Management (Basic ILB SKU を使用)
* Azure Active Directory Domain Service (ADDS) (Basic ILB SKU を使用)

詳細については、グローバル ピアリングの「[要件と制約](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)」、および[さまざまな VPN トポロジ](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)に関するページを参照してください。

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>シナリオ 8:Web アプリとスポーク仮想ネットワークの間にハブスポーク ネットワーク接続の問題が発生している

この問題のトラブルシューティングを行うには、これらの手順に従います。

1. Azure portal にサインインします。 Web アプリに移動し、 **[ネットワーク]** を選択し、 **[VNet 統合]** を選びます。
2. リモート仮想ネットワークが表示されているかどうかを確認します。 リモート仮想ネットワークのアドレス空間を手動で入力します ( **[ネットワークの同期]** と **[ルートの追加]** )。

詳細については、次の記事を参照してください。

* [アプリを Azure 仮想ネットワークに統合する](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [ポイント対サイト VPN ルーティングについて](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>シナリオ 9:仮想ネットワーク ピアリングの構成時にエラーが発生する

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>エラー 1:現在のテナント `<TENANT ID>` は、リンクされているサブスクリプションにアクセスする権限がありません

この問題を解決するには、「[ピアリングの作成 - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)」の手順に従います。

### <a name="error-2-not-connected"></a>エラー 2:未接続

両方の VNet からピアリングを削除し、再作成する必要があります。

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>エラー 3:Databricks 仮想ネットワークをピアリングできませんでした

この問題を解決するには、 **[Azure Databricks]** ブレードから仮想ネットワーク ピアリングを構成し、 **[リソース ID]** を使用してターゲット仮想ネットワークを指定します。 詳細については、「[Databricks 仮想ネットワークからリモート仮想ネットワークへのピアリング](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure VM 間の接続に関する問題のトラブルシューティング](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)