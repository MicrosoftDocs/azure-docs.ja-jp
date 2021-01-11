---
title: Azure Firewall に関する FAQ
description: Azure Firewall に関する FAQ。 Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 75435155ba1dad798d301006a30a5d5b6e96226a
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611179"
---
# <a name="azure-firewall-faq"></a>Azure Firewall に関する FAQ

## <a name="what-is-azure-firewall"></a>Azure Firewall とは

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 完全にステートフルなサービスとしてのファイアウォールで、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えています。 サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure Firewall ではどのような機能がサポートされていますか?

Azure Firewall の機能の詳細については、「[Azure Firewall の機能](features.md)」を参照してください。

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure Firewall の一般的なデプロイ モデルを教えてください

Azure Firewall は任意の仮想ネットワークにデプロイできますが、通常、お客様は中央の仮想ネットワークにデプロイし、ハブ アンド スポーク モデルでこれに他の仮想ネットワークをピアリングします。 こうすることで、この中央のファイアウォールの仮想ネットワークを指すように、ピアリングされた仮想ネットワークから既定のルートを設定できます。 グローバル VNet ピアリングはサポートされていますが、リージョン間の潜在的なパフォーマンスと待機時間の問題のため、推奨されません。 最適なパフォーマンスのため、リージョンごとに 1 つのファイアウォールをデプロイしてください。

このモデルの利点は、異なるサブスクリプションの複数のスポーク VNET に対して制御を集中的に実行できる点です。 VNet ごとに個別にファイアウォールを設置する必要がないため、コストも削減できます。 コスト削減は、カスタマーのトラフィック パターンに基づいて関連するピアリング コストに対して計測する必要があります。

## <a name="how-can-i-install-the-azure-firewall"></a>Azure Firewall のインストール方法を教えてください

Azure portal、PowerShell、REST API、またはテンプレートを使用して、Azure Firewall を設定できます。 「[チュートリアル:Azure portal を使用して Azure Firewall をデプロイして構成する](tutorial-firewall-deploy-portal.md)」を参照してください。

## <a name="what-are-some-azure-firewall-concepts"></a>Azure Firewall の概念をいくつか教えてください。

Azure Firewall は、ルールとルール コレクションをサポートしています。 ルール コレクションは、同じ順序と優先度を共有するルールのセットです。 ルール コレクションは、その優先度の順に実行されます。 ネットワーク ルール コレクションはアプリケーション ルール コレクションよりも優先されます。また、すべてのルールは終了されます。

次の 3 つの種類のルール コレクションがあります。

* "*アプリケーション ルール*": サブネットからアクセスできる完全修飾ドメイン名 (FQDN) を構成します。
* "*ネットワーク ルール*": 送信元アドレス、プロトコル、宛先ポート、および送信先アドレスを含むルールを構成します。
* *NAT ルール*:着信インターネット接続を許可する DNAT ルールを構成します。

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure Firewall は受信トラフィック フィルター処理をサポートしていますか?

Azure Firewall は受信と送信のフィルター処理をサポートしています。 受信保護は、通常、非 HTTP/S プロトコルに使用されます。 たとえば、RDP、SSH、および FTP プロトコルです。 インバウンド HTTP/S を最適に保護するには、[Azure Web アプリケーション ファイアウォール (WAF)](../web-application-firewall/overview.md) などの Web アプリケーション ファイアウォールを使用します。

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure Firewall では、どのログ記録および分析サービスがサポートされていますか?

Azure Firewall は、ファイアウォール ログの表示と分析について Azure Monitor と統合されています。 Log Analytics、Azure Storage、または Event Hubs にログを送信できます。 Log Analytics や、Excel や Power BI などのさまざまなツールで分析できます。 詳細については、[Azure Firewall のログを監視する方法に関するチュートリアル](tutorial-diagnostics.md)を参照してください。

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure Firewall の動作は、マーケットプレースの NVA などの既存のサービスとどのように異なりますか?

Azure Firewall は、特定のお客様のシナリオに対応できる基本的なファイアウォール サービスです。 サード パーティ製の NVA と Azure Firewall を組み合わせることが想定されています。 よりよい連携が重要な優先項目です。

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF と Azure Firewall の違いは何ですか?

Web アプリケーション ファイアウォール (WAF) は、一般的な脆弱性やその悪用から Web アプリケーションの受信保護を一元的に行う Application Gateway の機能です。 Azure Firewall は、非 HTTP/S プロトコル (例: RDP、SSH、FTP など) の受信保護、すべてのポートとプロトコルに対する送信ネットワークレベルの保護、送信 HTTP/S に対するアプリケーションレベルの保護を提供します。

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>ネットワーク セキュリティ グループ (NSG) と Azure Firewall の違いは何ですか?

Azure Firewall サービスは、ネットワーク セキュリティ グループの機能を補完します。 全体で、優れた "多層防御" ネットワーク セキュリティを実現します。 ネットワーク セキュリティ グループには、分散ネットワーク レイヤーのトラフィック フィルター機能があり、この機能によって各サブスクリプションの仮想ネットワーク内のリソースに対するトラフィックを制限します。 Azure Firewall は、完全にステートフルな一元化されたネットワーク ファイアウォールです。さまざまなサブスクリプションと仮想ネットワーク全体にネットワークレベルとアプリケーションレベルの保護を提供します。

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>AzureFirewallSubnet でネットワーク セキュリティ グループ (NSG) はサポートされていますか?

Azure Firewall は、NIC レベル NSG (表示不可) によるプラットフォーム保護など、複数の保護レイヤーがあるマネージド サービスです。  サブネット レベル NSG は AzureFirewallSubnet で必要なく、サービスの中断を確実に防ぐために無効にされています。

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>サービス エンドポイントに Azure Firewall を設定するにはどうすればよいですか?

PaaS サービスへのアクセスをセキュリティで保護するには、サービス エンドポイントをお勧めします。 Azure Firewall サブネット内のサービス エンドポイントを有効にし、接続されているスポーク仮想ネットワーク上ではそれらを無効にすることを選択できます。 このようにして、サービス エンドポイントのセキュリティとすべてのトラフィックの一元的ログ記録という、両方の機能の長所を活かすことができます。

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall の価格を教えてください

「[Azure Firewall の価格](https://azure.microsoft.com/pricing/details/azure-firewall/)」を参照してください。

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Azure Firewall の停止と起動の方法を教えてください

Azure PowerShell の *deallocate* メソッドと *allocate* メソッドを使用できます。

次に例を示します。

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> ファイアウォールとパブリック IP を、元のリソース グループとサブスクリプションに再割り当てする必要があります。

## <a name="what-are-the-known-service-limits"></a>既知のサービスの制限には何がありますか?

Azure Firewall サービスの制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)」を参照してください。

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Azure Firewall では、ハブ仮想ネットワークで 2 つのスポーク仮想ネットワーク間のネットワーク トラフィックを転送したりフィルター処理したりできますか?

はい。ハブ仮想ネットワークで Azure Firewall を使用して、2 つのスポーク仮想ネットワーク間のトラフィックをルーティングしたりフィルター処理したりできます。 このシナリオが適切に機能するためには、各スポーク仮想ネットワークのサブネットの UDR が、既定のゲートウェイとして Azure Firewall をポイントしている必要があります。

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure Firewall では、同じ仮想ネットワーク (またはピアリングされた仮想ネットワーク) のサブネット間のネットワーク トラフィックを転送したりフィルター処理したりできますか?

はい。 ただし、同じ VNET 内のサブネット間でトラフィックをリダイレクトするよう UDR を構成する場合、さらに注意が必要です。 UDR のターゲット プレフィックスとして VNET アドレス範囲を使用する場合、これにより、Azure Firewall インスタンスを介して、同じサブネット内で一方のマシンから他方のマシンにすべてのトラフィックがルーティングされることになります。 これを回避するために、次ホップ タイプ **VNET** を使用して UDR にサブネットのルートを組み込みます。 これらのルートの管理は、手間がかかり、誤りが発生する可能性もあります。 内部ネットワークのセグメント化について推奨される方法は、UDR を必要としないネットワーク セキュリティ グループを使用する方法です。

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure Firewall で、プライベート ネットワーク間のアウトバウンド SNAT は実行されますか?

宛先 IP アドレスが [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) のプライベート IP 範囲である場合、Azure Firewall は SNAT を行いません。 組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。 パブリック IP アドレス範囲の SNAT が**行われない**ように、Azure Firewall を構成することができます。 詳細については、「[Azure Firewall の SNAT プライベート IP アドレス範囲](snat-private-range.md)」を参照してください。

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>サポートされているネットワーク仮想アプライアンスに、トンネリング/チェーンが強制されますか。

強制トンネリングは、新しいファイアウォールを作成するときにサポートされます。 既存のファイアウォールを強制トンネリング用に構成することはできません。 詳細については、「[Azure Firewall 強制トンネリング](forced-tunneling.md)」を参照してください。

Azure Firewall には、インターネットへの直接接続が必要です。 AzureFirewallSubnet が BGP 経由のオンプレミス ネットワークへの既定のルートを学習する場合は、インターネットへの直接接続を保持するために、**NextHopType** の値を **Internet** に設定した 0.0.0.0/0 UDR でこれを上書きする必要があります。

使用する構成でオンプレミスのネットワークへの強制的なトンネリングが必要であり、インターネット上のアクセス先のターゲット IP プレフィックスを決定できる場合は、AzureFirewallSubnet 上のユーザー定義のルートを介して、次のホップとしてオンプレミスのネットワークに対してこれらの範囲を構成することができます。 または、BGP を使用してこれらのルートを定義することもできます。

## <a name="are-there-any-firewall-resource-group-restrictions"></a>ファイアウォール リソース グループの制限はありますか。

はい。 ファイアウォール、VNet、パブリック IP アドレスすべては同じリソース グループに属していなければなりません。

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>インバウンド インターネット ネットワーク トラフィックの DNAT を構成する際、そのトラフィックを許可するために、対応するネットワーク ルールも構成しなければならないのでしょうか。

いいえ。 NAT ルールは、変換されたトラフィックを許可するための対応するネットワーク ルールを暗黙的に追加します。 この動作は、変換されたトラフィックに一致する拒否ルールを使用してネットワーク ルール コレクションを明示的に追加することで、オーバーライドすることができます。 Azure Firewall ルール処理ロジックの詳細については、「[Azure Firewall ルール処理ロジック](rule-processing.md)」を参照してください。

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>アプリケーション ルールのターゲット FQDN でワイルドカードはどのように機能しますか。

現在、ワイルドカードは FQDN の左側でのみ使用できます。 たとえば、* **.contoso.com** や ***contoso.com** などです。

\* **.contoso.com** を構成した場合、これにより、*何らかの値*.contoso.com は許可されますが、contoso.com (ドメインの頂点) は許可されません。 ドメインの頂点を許可する場合は、それをターゲット FQDN として明示的に構成する必要があります。

## <a name="what-does-provisioning-state-failed-mean"></a>"*プロビジョニング状態: 失敗*" はどのような意味ですか。

構成の変更が適用されるたびに、Azure Firewall は、その基になるすべてのバックエンド インスタンスを更新しようとします。 まれに、これらのバックエンド インスタンスの 1 つが新しい構成での更新に失敗し、更新プロセスが停止して、失敗したプロビジョニングの状態になることがあります。 Azure Firewall はまだ操作可能ですが、適用された構成は矛盾した状態になっている可能性があります。この場合、一部のインスタンスは以前の構成であり、他のインスタンスはルール セットが更新されています。 このような場合は、操作が成功してファイアウォールが "*成功*" プロビジョニング状態になるまで、もう一度構成を更新してみてください。

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Azure Firewall は計画メンテナンスや予期しない障害にどのように対処しますか。
Azure Firewall は、アクティブ/アクティブ構成の複数のバックエンドノードで構成されます。  計画メンテナンスの場合は、ノードを適切に更新するための接続のドレイン ロジックがあります。  更新は、中断のリスクをさらに制限するために、Azure リージョンごとに営業時間外に予定されます。  予期しない問題の場合は、新しいノードをインスタンス化して、障害が発生したノードを置き換えます。  新しいノードへの接続は、通常、障害発生時から 10 秒以内に再確立されます。

## <a name="how-does-connection-draining-work"></a>接続のドレインはどのように機能しますか。

計画されたメンテナンスの場合、接続のドレイン ロジックにより、バックエンド ノードが適切に更新されます。 既存の接続が閉じられるまで、Azure Firewall は 90 秒間待機します。 必要に応じて、クライアントは別のバックエンド ノードへの接続を自動的に再確立できます。

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>ファイアウォール名に文字制限はありますか。

はい。 ファイアウォール名には 50 文字の制限があります。

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Azure Firewall に /26 サブネット サイズが必要なのはなぜですか。

Azure Firewall では、スケールの際により多くの仮想マシン インスタンスをプロビジョニングする必要があります。 /26 アドレス空間を使用すると、スケールに対応できる十分な数の IP アドレスをファイアウォールに使用できるようになります。

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>サービスのスケールに応じてファイアウォール サブネットのサイズを変更する必要はありますか。

いいえ。 Azure Firewall には、/26 よりも大きなサブネットは必要ありません。

## <a name="how-can-i-increase-my-firewall-throughput"></a>ファイアウォールのスループットを増やすにはどうすればよいですか。

Azure Firewall の初期スループット容量は 2.5 から 3 Gbps で、30 Gbps までスケールアウトします。 CPU 使用率とスループットに基づいて自動的にスケールアウトされます。

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Azure Firewall のスケールアウトにはどのくらいの時間がかかりますか。

Azure Firewall は、平均スループットまたは CPU 使用率が 60% になると、徐々にスケーリングされます。 既定のデプロイの最大スループットは約 2.5 ～ 3 Gbps で、その量の 60% に達したときにスケールアウトが開始されます。 スケールアウトには 5 から 7 分かかります。 

パフォーマンス テストを行うときは、少なくとも 10 ～ 15 分のテストを行い、新しく作成されたファイアウォール ノードを活用するために新しい接続を開始してください。

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Azure Firewall では Active Directory へのアクセスが既定で許可されますか。

いいえ。 Azure Firewall では、Active Directory へのアクセスは既定でブロックされます。 アクセスを許可するには、AzureActiveDirectory サービス タグを構成します。 詳しくは、「[Azure Firewall サービス タグ](service-tags.md)」をご覧ください。

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall の脅威インテリジェンス ベースのフィルター処理から、FQDN または IP アドレスを除外できますか。

はい。Azure PowerShell を使用して、それを行うことができます。

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>TCP ping や類似のツールが、そのトラフィックを許可するルールが Azure Firewall にない場合でも、ターゲット FQDN に正常に接続できるのはなぜですか。

TCP ping は実際にはターゲット FQDN に接続していません。 Azure Firewall の透過プロキシでは、ポート 80/443 で送信トラフィックがリッスンされるためにこのようなことが起こります。 TCP ping を実行すると、ファイアウォールとの接続が確立されます。これにより、パケットが破棄され、接続がログに記録されます。 この動作はセキュリティに影響しません。 ただし、混乱を避けるために、この動作に対する変更の可能性を調査しています。

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>IP グループでサポートされる IP アドレスの数に制限はありますか。

はい。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)」を参照してください。

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>別のリソース グループに IP グループを移動できますか。

いいえ。IP グループを別のリソースグループに移動することは現在サポートされていません。

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Azure Firewall の TCP アイドル タイムアウトはどうなっていますか。

ネットワーク ファイアウォールの標準的な動作では、TCP 接続が確実に維持されるようにして、アクティビティがない場合はすぐに終了するようになっています。 Azure Firewall の TCP アイドル タイムアウトは 4 分です。 この設定を構成することはできません。 アイドル時間がタイムアウト値よりも長い場合、TCP や HTTP のセッションが維持される保証はありません。 一般的な方法として、TCP keep-alive を使用します。 この方法を使用すると、接続が長時間アクティブ状態に維持されます。 詳細については、[.NET の例](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_)に関するページを参照してください。

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>パブリック IP アドレスを使用せずに Azure Firewall をデプロイできますか。

いいえ。現時点では、Azure Firewall はパブリック IP アドレスを使用してデプロイする必要があります。

## <a name="where-does-azure-firewall-store-customer-data"></a>顧客データは Azure Firewall によってどこに格納されますか?

Azure Firewall によって、顧客データがデプロイされているリージョン外に移動または格納されることはありません。
