---
title: Azure Firewall に関する FAQ
description: Azure Firewall に関する FAQ
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 6/21/2019
ms.author: victorh
ms.openlocfilehash: 933b4167f25db5a01cf1160f5e781a1fe31afc6b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304596"
---
# <a name="azure-firewall-faq"></a>Azure Firewall に関する FAQ

## <a name="what-is-azure-firewall"></a>Azure Firewall とは

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 完全にステートフルなサービスとしてのファイアウォールで、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えています。 サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure Firewall ではどのような機能がサポートされていますか?

* サービスとしてのステートフル ファイアウォール
* 無制限のクラウド スケーラビリティを備えたビルトインの高可用性
* FQDN フィルタリング
* FQDN のタグ
* ネットワーク トラフィックのフィルタリング規則
* 送信 SNAT サポート
* 受信 DNAT のサポート
* Azure サブスクリプションと VNET をまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。
* Azure Monitor との完全統合によるログ記録と分析

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure Firewall の一般的なデプロイ モデルを教えてください

Azure Firewall は任意の仮想ネットワークにデプロイできますが、通常、お客様は中央の仮想ネットワークにデプロイし、ハブ アンド スポーク モデルでこれに他の仮想ネットワークをピアリングします。 こうすることで、この中央のファイアウォールの仮想ネットワークを指すように、ピアリングされた仮想ネットワークから既定のルートを設定できます。 グローバル VNet ピアリングはサポートされていますが、リージョン間の潜在的なパフォーマンスと待機時間の問題のため、推奨されません。 最適なパフォーマンスのため、リージョンごとに 1 つのファイアウォールをデプロイしてください。

このモデルの利点は、異なるサブスクリプションの複数のスポーク VNET に対して制御を集中的に実行できる点です。 VNet ごとに個別にファイアウォールを設置する必要がないため、コストも削減できます。 コスト削減は、カスタマーのトラフィック パターンに基づいて関連するピアリング コストに対して計測する必要があります。

## <a name="how-can-i-install-the-azure-firewall"></a>Azure Firewall のインストール方法を教えてください

Azure portal、PowerShell、REST API、またはテンプレートを使用して、Azure Firewall を設定できます。 手順については、「[チュートリアル: Azure portal を使用して Azure Firewall をデプロイして構成する](tutorial-firewall-deploy-portal.md)」を参照してください。

## <a name="what-are-some-azure-firewall-concepts"></a>Azure Firewall の概念をいくつか教えてください。

Azure Firewall は、ルールとルール コレクションをサポートしています。 ルール コレクションは、同じ順序と優先度を共有するルールのセットです。 ルール コレクションは、その優先度の順に実行されます。 ネットワーク ルール コレクションはアプリケーション ルール コレクションよりも優先されます。また、すべてのルールは終了されます。

次の 3 つの種類のルール コレクションがあります。

* "*アプリケーション ルール*": サブネットからアクセスできる完全修飾ドメイン名 (FQDN) を構成します。
* "*ネットワーク ルール*": 送信元アドレス、プロトコル、宛先ポート、および送信先アドレスを含むルールを構成します。
* *NAT ルール*:着信接続を許可する DNAT ルールを構成します。

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure Firewall は受信トラフィック フィルター処理をサポートしていますか?

Azure Firewall は受信と送信のフィルター処理をサポートしています。 受信保護は、非 HTTP/S プロトコル用です。 たとえば、RDP、SSH、および FTP プロトコルです。

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure Firewall では、どのログ記録および分析サービスがサポートされていますか?

Azure Firewall は、ファイアウォール ログの表示と分析について Azure Monitor と統合されています。 Log Analytics、Azure Storage、または Event Hubs にログを送信できます。 Log Analytics や、Excel や Power BI などのさまざまなツールで分析できます。 詳細については、[Azure Firewall のログを監視する方法に関するチュートリアル](tutorial-diagnostics.md)を参照してください。

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure Firewall の動作は、マーケットプレースの NVA などの既存のサービスとどのように異なりますか?

Azure Firewall は、特定のお客様のシナリオに対応できる基本的なファイアウォール サービスです。 サード パーティ製の NVA と Azure Firewall を組み合わせることが想定されています。 よりよい連携が重要な優先項目です。

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF と Azure Firewall の違いは何ですか?

Web アプリケーション ファイアウォール (WAF) は、一般的な脆弱性やその悪用から Web アプリケーションの受信保護を一元的に行う Application Gateway の機能です。 Azure Firewall は、非 HTTP/S プロトコル (例: RDP、SSH、FTP など) の受信保護、すべてのポートとプロトコルに対する送信ネットワークレベルの保護、送信 HTTP/S に対するアプリケーションレベルの保護を提供します。

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>ネットワーク セキュリティ グループ (NSG) と Azure Firewall の違いは何ですか?

Azure Firewall サービスは、ネットワーク セキュリティ グループの機能を補完します。 全体で、優れた "多層防御" ネットワーク セキュリティを実現します。 ネットワーク セキュリティ グループには、分散ネットワーク レイヤーのトラフィック フィルター機能があり、この機能によって各サブスクリプションの仮想ネットワーク内のリソースに対するトラフィックを制限します。 Azure Firewall は、完全にステートフルな一元化されたネットワーク ファイアウォールです。さまざまなサブスクリプションと仮想ネットワーク全体にネットワークレベルとアプリケーションレベルの保護を提供します。

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Azure Firewall サブネットでネットワーク セキュリティ グループ (NSG) はサポートされていますか?

Azure Firewall は、NIC レベル NSG (表示不可) によるプラットフォーム保護など、複数の保護レイヤーがあるマネージド サービスです。  サブネット レベル NSG は Azure Firewall サブネットで必要なく、サービスの中断を防ぐために無効にされています。

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>サービス エンドポイントに Azure Firewall を設定するにはどうすればよいですか?

PaaS サービスへのアクセスをセキュリティで保護するには、サービス エンドポイントをお勧めします。 Azure Firewall サブネット内のサービス エンドポイントを有効にし、接続されているスポーク仮想ネットワーク上ではそれらを無効にすることを選択できます。 このようにして、サービス エンドポイントのセキュリティとすべてのトラフィックの一元的ログ記録という、両方の機能の長所を生かすことができます。

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall の価格を教えてください

「[Azure Firewall の価格](https://azure.microsoft.com/pricing/details/azure-firewall/)」を参照してください。

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Azure Firewall の停止と起動の方法を教えてください

Azure PowerShell の *deallocate* メソッドと *allocate* メソッドを使用できます。

例:

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
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> ファイアウォールとパブリック IP を、元のリソース グループとサブスクリプションに再割り当てする必要があります。

## <a name="what-are-the-known-service-limits"></a>既知のサービスの制限には何がありますか?

Azure Firewall サービスの制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md#azure-firewall-limits)」を参照してください。

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Azure Firewall では、ハブ仮想ネットワークで 2 つのスポーク仮想ネットワーク間のネットワーク トラフィックを転送したりフィルター処理したりできますか?

はい。ハブ仮想ネットワークで Azure Firewall を使用して、2 つのスポーク仮想ネットワーク間のトラフィックをルーティングしたりフィルター処理したりできます。 各スポーク仮想ネットワークのサブネットを正常に動作させるためには、このシナリオで既定のゲートウェイとして Azure Firewall をポイントする UDR が必要です。

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure Firewall では、同じ仮想ネットワーク (またはピアリングされた仮想ネットワーク) のサブネット間のネットワーク トラフィックを転送したりフィルター処理したりできますか?

はい。 ただし、同じ VNET 内のサブネット間でトラフィックをリダイレクトするよう UDR を構成する場合、さらに注意が必要です。 UDR のターゲット プレフィックスとして VNET アドレス範囲を使用する場合、これにより、Azure Firewall インスタンスを介して、同じサブネット内で一方のマシンから他方のマシンにすべてのトラフィックがルーティングされることになります。 これを回避するために、次ホップ タイプ **VNET** を使用して UDR にサブネットのルートを組み込みます。 これらのルートの管理は、手間がかかり、誤りが発生する可能性もあります。 内部ネットワークのセグメント化の推奨される方法は、UDR を必要としないネットワーク セキュリティ グループを使用する方法です。

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure Firewall で、プライベート ネットワーク間のアウトバウンド SNAT は実行されますか?

宛先 IP アドレスが [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) のプライベート IP 範囲である場合、Azure Firewall は SNAT を行いません。 組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>サポートされているネットワーク仮想アプライアンスに、トンネリング/チェーンが強制されますか。

既定では強制トンネル処理はサポートされていませんが、サポートの支援により有効にすることができます。

Azure Firewall には、インターネットへの直接接続が必要です。 AzureFirewallSubnet が BGP 経由のオンプレミス ネットワークへの既定のルートを学習する場合は、インターネットへの直接接続を保持するために、**NextHopType** の値を **Internet** に設定した 0.0.0.0/0 UDR でこれを上書きする必要があります。 既定では、Azure Firewall はオンプレミス ネットワークへの強制トンネリングをサポートしません。

ただし、オンプレミス ネットワークへの強制トンネリングが必要な構成の場合、Microsoft は状況に応じてサポートします。 サポートにお問い合わせいただければ、お客様の状況を確認させていただきます。 受け付けると、Microsoft ではサブスクリプションを許可し、必要なファイアウォールのインターネット接続が確保されていることを確認します。

## <a name="are-there-any-firewall-resource-group-restrictions"></a>ファイアウォール リソース グループの制限はありますか。

はい。 ファイアウォール、サブネット、VNet、パブリック IP アドレスすべては同じリソース グループに属していなければなりません。

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>インバウンド ネットワーク トラフィックの DNAT を構成する際、そのトラフィックを許可するための、対応するネットワーク ルールも構成しなければならないのでしょうか。

いいえ。 NAT ルールは、変換されたトラフィックを許可するための対応するネットワーク ルールを暗黙的に追加します。 この動作は、変換されたトラフィックに一致する拒否ルールを使用してネットワーク ルール コレクションを明示的に追加することで、オーバーライドすることができます。 Azure Firewall ルール処理ロジックの詳細については、「[Azure Firewall ルール処理ロジック](rule-processing.md)」を参照してください。

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>アプリケーション ルールのターゲット FQDN でワイルドカードはどのように機能しますか。

* **.contoso.com** を構成した場合、これにより、*何らかの値*.contoso.com は許可されますが、contoso.com (ドメインの頂点) は許可されません。 ドメインの頂点を許可する場合は、それをターゲット FQDN として明示的に構成する必要があります。

## <a name="what-does-provisioning-state-failed-mean"></a>"*プロビジョニング状態: 失敗*" はどのような意味ですか。

構成の変更が適用されるたびに、Azure Firewall は、その基になるすべてのバックエンド インスタンスを更新しようとします。 まれに、これらのバックエンド インスタンスの 1 つが新しい構成での更新に失敗し、更新プロセスが停止して、失敗したプロビジョニングの状態になることがあります。 Azure Firewall はまだ操作可能ですが、適用された構成は矛盾した状態になっている可能性があります。この場合、一部のインスタンスは以前の構成であり、他のインスタンスはルール セットが更新されています。 このような場合は、操作が成功してファイアウォールが "*成功*" プロビジョニング状態になるまで、もう一度構成を更新してみてください。
