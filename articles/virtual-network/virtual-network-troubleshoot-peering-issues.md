---
title: 仮想ネットワーク ピアリングの問題をトラブルシューティングする
description: ほとんどの仮想ネットワーク ピアリングの問題を解決するのに役立つ手順。
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 608f3d72aecb0c6d53325c0d082b1779822e9c80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200526"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>仮想ネットワーク ピアリングの問題をトラブルシューティングする

このトラブルシューティング ガイドでは、ほとんどの[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)の問題の解決に役立つ手順を提供します。

![仮想ネットワーク ピアリングの図](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>2 つの仮想ネットワーク間に仮想ネットワーク ピアリングを構成する

仮想ネットワークが同じサブスクリプションまたは異なるサブスクリプションにありますか?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>仮想ネットワークが同じサブスクリプション内にある

同じサブスクリプションにある仮想ネットワークの仮想ネットワーク ピアリングを構成するには、次の記事にある方法を使用します。

* 仮想ネットワークが *同じリージョン* にある場合は、「[ピアリングの作成](./virtual-network-manage-peering.md#create-a-peering)」を参照してください。
* 仮想ネットワークが *異なるリージョン* にある場合は、「[仮想ネットワーク ピアリング](./virtual-network-peering-overview.md)」を参照してください。 

> [!Note]
> 次のリソースについては、グローバル仮想ネットワーク ピアリング経由での接続が機能しません。 
>
> * Basic 内部ロードバランサー (ILB) SKU の背後にある仮想マシン (VM)
> * Redis Cache (Basic ILB SKU を使用)
> * Application Gateway v1 (Basic ILB SKU を使用)
> * 仮想マシン スケール セット (Basic ILB SKU を使用)
> * Azure Service Fabric クラスター (Basic ILB SKU を使用)
> * SQL Server Always On (Basic ILB SKU を使用)
> * PowerApps 用 Azure App Service 環境 (Basic ILB SKU を使用)
> * Azure API Management (Basic ILB SKU を使用)
> * Azure Active Directory Domain Service (Azure AD DS) (Basic ILB SKU を使用)

詳細については、グローバル ピアリングの「[要件と制約](./virtual-network-peering-overview.md#requirements-and-constraints)」を参照してください。

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>仮想ネットワークが異なるサブスクリプションまたは Active Directory テナントにある

異なるサブスクリプションまたは Active Directory テナント内の仮想ネットワークの仮想ネットワーク ピアリングを構成するには、[Azure CLI の異なるサブスクリプションでのピアリングの作成](./create-peering-different-subscriptions.md#cli)に関する記事を参照してください。

> [!Note]
> ネットワーク ピアリングを構成するには、両方のサブスクリプションで **ネットワーク共同作成者** のアクセス許可を持っている必要があります。 詳細については、[ピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページを参照してください。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>オンプレミス リソースを使用するハブスポーク トポロジで仮想ネットワーク ピアリングを構成する

![オンプレミスのスポークを使用した仮想ネットワーク ピアリングの図](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>サイト間接続または ExpressRoute 接続の場合

次の手順に従います: [仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="for-point-to-site-connections"></a>ポイント対サイト接続の場合

1. 次の手順に従います: [仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 仮想ネットワーク ピアリングが確立または変更された後、ポイント対サイト クライアントでスポーク仮想ネットワークへの更新されたルートを取得できるように、ポイント対サイト パッケージをダウンロードして再びインストールします。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>ハブスポーク トポロジ仮想ネットワークを使用して仮想ネットワーク ピアリングを構成する

![仮想ネットワーク スポークを使用した仮想ネットワーク ピアリングの図](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>仮想ネットワークが同じリージョン内にある


1. ハブ仮想ネットワークで、ネットワーク仮想アプライアンス (NVA) を構成します。
1. スポーク仮想ネットワークで、ユーザー定義ルートに次ホップの種類 "ネットワーク仮想アプライアンス" を適用します。

詳細については、「[サービス チェイニング](./virtual-network-peering-overview.md#service-chaining)」を参照してください。

> [!Note]
> NVA の設定に関するヘルプが必要な場合は、[NVA のベンダーにお問い合わせください](https://mskb.pkisolutions.com/kb/2984655)。

NVA デバイスの設定とルーティングのトラブルシューティングに関するヘルプについては、「[Azure でのネットワーク仮想アプライアンスの問題](./virtual-network-troubleshoot-nva.md)」を参照してください。

### <a name="the-virtual-networks-are-in-different-regions"></a>仮想ネットワークが異なるリージョン内にある

グローバル仮想ネットワーク ピアリング経由の転送がサポートされるようになりました。 次のリソースについては、グローバル仮想ネットワーク ピアリング経由での接続が機能しません。

* Basic ILB SKU の背後にある VM
* Redis Cache (Basic ILB SKU を使用)
* Application Gateway (Basic ILB SKU を使用)
* スケール セット (Basic ILB SKU を使用)
* Service Fabric クラスター (Basic ILB SKU を使用)
* SQL Server Always On (Basic ILB SKU を使用)
* App Service Environment (Basic ILB SKU を使用)
* API Management (Basic ILB SKU を使用)
* Azure AD DS (Basic ILB SKU を使用)

グローバル ピアリングの要件と制約の詳細については、「[仮想ネットワーク ピアリング](./virtual-network-peering-overview.md#requirements-and-constraints)」を参照してください。

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>ピアリングした 2 つの仮想ネットワーク間の接続に関する問題のトラブルシューティング

必要な[ロールとアクセス許可](virtual-network-manage-peering.md#permissions)があるアカウントで、[Azure portal](https://portal.azure.com/) にサインインします。 仮想ネットワークを選択して、 **[ピアリング]** を選び、 **[状態]** フィールドを確認します。 状態はどうなっていますか?

### <a name="the-peering-status-is-connected"></a>ピアリングの状態は [接続済み]

この問題のトラブルシューティングを行うには:

1. ネットワーク トラフィック フローを確認する:

   [接続のトラブルシューティング](../network-watcher/network-watcher-connectivity-overview.md)と、ソース VM から宛先 VM への [IP フロー検証](../network-watcher/network-watcher-ip-flow-verify-overview.md)を使用して、トラフィック フローの干渉の原因となっている NSG または UDR があるかどうかを判断します。

   ファイアウォールまたは NVA を使用している場合: 
   1. この手順の完了後に復元できるように、UDR パラメーターをドキュメント化します。
   2. 次ホップとして NVA を指すソース VM サブネットまたは NIC から UDR を削除します。 ソース VM から、NVA をバイパスしている宛先への直接の接続を確認します。 この手順がうまくいかない場合は、[NVA トラブルシューティング ツール](./virtual-network-troubleshoot-nva.md)に関するページを参照してください。

2. ネットワーク トレースを取得する: 
   1. 宛先 VM でネットワーク トレースを開始します。 Windows の場合は、**Netsh** を使用できます。 Linux の場合は、**TCPDump** を使用します。
   2. ソースから宛先 IP への **TcpPing** または **PsPing** を実行します。

      これは **TcpPing** コマンドの例です: `tcping64.exe -t <destination VM address> 3389`

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
   > * SQL Server Always On (Basic ILB SKU を使用)
   > * App Service Environment (Basic ILB SKU を使用)
   > * API Management (Basic ILB SKU を使用)
   > * Azure AD DS (Basic ILB SKU を使用)

詳細については、グローバル ピアリングの「[要件と制約](./virtual-network-peering-overview.md#requirements-and-constraints)」を参照してください。

### <a name="the-peering-status-is-disconnected"></a>ピアリングの状態は [切断]

この問題を解決するには、両方の仮想ネットワークからピアリングを削除し、再作成します。

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>ハブスポーク仮想ネットワークとオンプレミス リソースの間の接続に関する問題のトラブルシューティング

ネットワークでサードパーティの NVA または VPN ゲートウェイが使用されていますか?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>自分のネットワークでサードパーティの NVA または VPN ゲートウェイが使用されている

サードパーティの NVA または VPN ゲートウェイに影響する接続問題のトラブルシューティングを行う場合は、次の記事を参照してください。

* [NVA トラブルシューティング ツール](./virtual-network-troubleshoot-nva.md)
* [サービス チェーン](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>自分のネットワークでサードパーティの NVA や VPN ゲートウェイが使用されていない

ハブ仮想ネットワークとスポーク仮想ネットワークには VPN ゲートウェイがありますか?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>ハブ仮想ネットワークとスポーク仮想ネットワークの両方に VPN ゲートウェイがある

リモート ゲートウェイの使用はサポートされていません。

スポーク仮想ネットワークに既に VPN ゲートウェイがある場合、スポーク仮想ネットワークでは **[リモート ゲートウェイを使用する]** オプションはサポートされません。 これは、仮想ネットワーク ピアリングの制限によるものです。

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>ハブ仮想ネットワークとスポーク仮想ネットワークの両方に VPN ゲートウェイがない

サイト間接続または Azure ExpressRoute 接続の場合は、オンプレミスからリモート仮想ネットワークへの接続に関する問題の、以下に示す主な原因を確認します。

* ゲートウェイがある仮想ネットワークで **[転送されたトラフィックを許可する]** チェック ボックスがオンになっていることを確認します。
* ゲートウェイがない仮想ネットワークで **[リモート ゲートウェイを使用する]** チェック ボックスがオンになっていることを確認します。
* オンプレミス デバイスを調べ、それらすべてにリモート仮想ネットワーク アドレス空間が追加されていることを確認するようにネットワーク管理者に依頼します。

ポイント対サイト接続の場合:

* ゲートウェイがある仮想ネットワークで **[転送されたトラフィックを許可する]** チェック ボックスがオンになっていることを確認します。
* ゲートウェイがない仮想ネットワークで **[リモート ゲートウェイを使用する]** チェック ボックスがオンになっていることを確認します。
* ポイント対サイト クライアント パッケージをダウンロードして再インストールします。 新しくピアリングされた仮想ネットワーク ルートでは、ポイント対サイト クライアントにルートが自動的に追加されません。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>同じリージョン内のスポーク仮想ネットワーク間のハブスポーク ネットワーク接続に関する問題のトラブルシューティング

ハブ ネットワークに NVA がある必要があります。 NVA が次ホップとして設定されているスポークで UDR を構成し、ハブ仮想ネットワークで **[転送されたトラフィックを許可する]** を有効にします。

詳細については、「[サービス チェイニング](./virtual-network-peering-overview.md#service-chaining)」を参照し、選択した [NVA ベンダー](https://mskb.pkisolutions.com/kb/2984655)とこれらの要件について相談してください。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>異なるリージョンのスポーク仮想ネットワーク間のハブスポーク ネットワーク接続に関する問題のトラブルシューティング

グローバル仮想ネットワーク ピアリング経由の転送がサポートされるようになりました。 次のリソースについては、グローバル仮想ネットワーク ピアリング経由での接続が機能しません。

* Basic ILB SKU の背後にある VM
* Redis Cache (Basic ILB SKU を使用)
* Application Gateway (Basic ILB SKU を使用)
* スケール セット (Basic ILB SKU を使用)
* Service Fabric クラスター (Basic ILB SKU を使用)
* SQL Server Always On (Basic ILB SKU を使用)
* App Service Environment (Basic ILB SKU を使用)
* API Management (Basic ILB SKU を使用)
* Azure AD DS (Basic ILB SKU を使用)

詳細については、グローバル ピアリングの「[要件と制約](./virtual-network-peering-overview.md#requirements-and-constraints)」、および[さまざまな VPN トポロジ](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2)に関するページを参照してください。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Web アプリとスポーク仮想ネットワークの間のハブスポーク ネットワーク接続に関する問題のトラブルシューティング

この問題のトラブルシューティングを行うには:

1. Azure portal にサインインします。 
1. Web アプリで **[ネットワーク]** を選択し、 **[VNet 統合]** を選びます。
1. リモート仮想ネットワークが表示されているかどうかを確認します。 リモート仮想ネットワークのアドレス空間を手動で入力します ( **[ネットワークの同期]** と **[ルートの追加]** )。

詳細については、次の記事を参照してください。

* [アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md)
* [ポイント対サイト VPN ルーティングについて](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>仮想ネットワーク ピアリング構成エラー メッセージのトラブルシューティング 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>現在のテナント `<TENANT ID>` は、リンクされているサブスクリプションにアクセスする権限がありません

この問題を解決するには、「[ピアリングの作成 - Azure CLI](./create-peering-different-subscriptions.md#cli)」を参照してください。

### <a name="not-connected"></a>[未接続]

この問題を解決するには、両方の仮想ネットワークからピアリングを削除し、再作成します。

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Databricks 仮想ネットワークをピアリングできませんでした

この問題を解決するには、 **[Azure Databricks]** から仮想ネットワーク ピアリングを構成し、 **[リソース ID]** を使用してターゲット仮想ネットワークを指定します。 詳細については、「[Databricks 仮想ネットワークからリモート仮想ネットワークへのピアリング](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2)」を参照してください。

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>リモート仮想ネットワークにゲートウェイがありません

この問題は、別のテナントの仮想ネットワークをピアリングし、後で `Use Remote Gateways` を構成する場合に発生します。 Azure portal の制限により、別のテナントの仮想ネットワークに仮想ネットワーク ゲートウェイが存在するかどうかを検証できません。

この問題を解決するには、次の 2 つの方法があります。

 * ピアリングを削除し、新しいピアリングを作成するときに `Use Remote Gateways` オプションをアクティブにします。
 * Azure portal ではなく、PowerShell または CLI を使用して `Use Remote Gateways` を有効にします。

## <a name="next-steps"></a>次のステップ

* [Azure VM 間の接続に関する問題のトラブルシューティング](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
