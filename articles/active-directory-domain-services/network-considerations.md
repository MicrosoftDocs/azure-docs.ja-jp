---
title: Azure AD Domain Services のネットワーク計画と接続 | Microsoft Docs
description: Azure Active Directory Domain Services を実行するときの仮想ネットワーク設計の考慮事項と接続に使用されるリソースについて説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: iainfou
ms.openlocfilehash: 506967fc4cecd322c694d31789cf09bec22ad3d4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617321"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Azure AD Domain Services の仮想ネットワーク設計の考慮事項と構成オプション

Azure Active Directory Domain Services (AD DS) から他のアプリケーションおよびワークロードに認証および管理サービスが提供されるため、ネットワーク接続は重要なコンポーネントです。 仮想ネットワーク リソースが適切に構成されていないと、アプリケーションとワークロードは Azure AD DS で提供される機能と通信して使用することができません。 仮想ネットワークを正しく計画している場合は、Azure AD DS が必要に応じてアプリケーションとワークロードにサービスを提供できることを確認します。

この記事では、Azure AD DS をサポートする Azure 仮想ネットワークの設計の考慮事項と要件について説明します。

## <a name="azure-virtual-network-design"></a>Azure 仮想ネットワークの設計

ネットワーク接続を提供し、アプリケーションとサービスが Azure AD DS に対して認証できるようにするには、Azure 仮想ネットワークとサブネットを使用します。 Azure AD DS を独自の仮想ネットワークにデプロイするのが理想的です。 同じ仮想ネットワーク内に別のアプリケーション サブネットを追加して、管理 VM または軽量なアプリケーション ワークロードをホストすることができます。 Azure AD DS 仮想ネットワークとピアリングされた大規模または複雑なアプリケーション ワークロードの場合は、通常、別の仮想ネットワークが最適な設計です。 以下のセクションに記載されている仮想ネットワークとサブネットの要件を満たしている場合は、その他の設計の選択肢が有効です。

Azure AD DS の仮想ネットワークを設計する際には、次の考慮事項が適用されます。

* Azure AD DS は、仮想ネットワークと同じ Azure リージョンにデプロイする必要があります。
    * 現時点では、Azure AD テナントごとにデプロイできる Azure AD DS マネージド ドメインは 1 つのみです。 Azure AD DS マネージド ドメインは、1 つのリージョンにデプロイされます。 仮想ネットワークは、必ず [Azure AD DS をサポートするリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)で作成または選択します。
* 他の Azure リージョンとアプリケーション ワークロードをホストする仮想ネットワークの距離を考慮します。
    * 待機時間を最小限に抑えるには、Azure AD DS マネージド ドメインの仮想ネットワーク サブネットの近く、または同じリージョンにコア アプリケーションを保持します。 Azure 仮想ネットワーク間には、仮想ネットワーク ピアリングまたは仮想プライベート ネットワーク (VPN) 接続を使用できます。
* 仮想ネットワークは、Azure AD DS が提供するもの以外の DNS サービスに依存することはできません。
    * Azure AD DS は独自の DNS サービスを提供しています。 これらの DNS サービス アドレスを使用するように仮想ネットワークを構成する必要があります。 追加の名前空間の名前解決は、条件付きフォワーダーを使用して実現できます。
    * カスタム DNS サーバー設定を使用して、VM などの他の DNS サーバーにクエリを送信することはできません。 仮想ネットワーク内のリソースでは、Azure AD DS から提供される DNS サービスを使用する必要があります。

> [!IMPORTANT]
> サービスを有効にした後、Azure AD DS を別の仮想ネットワークに移行することはできません。

Azure AD DS マネージド ドメインは、Azure 仮想ネットワーク内のサブネットに接続します。 次の点を考慮して、Azure AD DS 用にこのサブネットを設計します。

* Azure AD DS は、独自のサブネットにデプロイする必要があります。 既存のサブネットまたはゲートウェイ サブネットは使用しないでください。
* Azure AD DS マネージド ドメインのデプロイ時に、ネットワーク セキュリティ グループが作成されます。 このネットワーク セキュリティ グループには、サービス通信を正しく行うために必要な規則が含まれています。
    * 独自のカスタム規則を持つ既存のネットワーク セキュリティ グループを作成または使用しないでください。
* Azure AD DS には、5 から 7 個の IP アドレスが必要です。 サブネットの IP アドレス範囲でこの数のアドレスを提供できることを確認してください。
    * 使用可能な IP アドレスを制限すると、Azure AD Domain Services で 2 つのドメイン コントローラーを維持できなくなる可能性があります。

次の図の例は、Azure AD DS に独自のサブネットがあり、外部接続用にゲートウェイ サブネットがあり、アプリケーション ワークロードが仮想ネットワーク内の接続されたサブネットにある有効な設計を示しています。

![Recommended subnet design](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Azure AD DS 仮想ネットワークへの接続

前のセクションで説明したように、Azure AD Domain Services マネージド ドメインは、Azure の 1 つの仮想ネットワークにのみ作成できます。また、Azure AD テナントごとに作成できるマネージド ドメインは 1 つのみです。 このアーキテクチャに基づいて、アプリケーション ワークロードをホストする 1 つ以上の仮想ネットワークを Azure AD DS 仮想ネットワークに接続することが必要になる場合があります。

次のいずれかの方法を使用して、他の Azure 仮想ネットワークでホストされているアプリケーション ワークロードを接続できます。

* 仮想ネットワーク ピアリング
* 仮想プライベート ネットワーク (VPN)

### <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

仮想ネットワーク ピアリングとは、同じリージョンに存在する 2 つの仮想ネットワークを Azure のバックボーン ネットワークを介して接続する機構です。 グローバル仮想ネットワーク ピアリングで、Azure リージョン間の仮想ネットワークを接続できます。 ピアリングされると、2 つの仮想ネットワークでは、VM などのリソースでプライベート IP アドレスを使用して相互に直接通信できるようになります。 仮想ネットワーク ピアリングを使用すると、他の仮想ネットワークにデプロイされたアプリケーション ワークロードで Azure AD DS マネージド ドメインをデプロイできます。

![Virtual network connectivity using peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

詳細については、[Azure 仮想ネットワークのピアリングの概要](../virtual-network/virtual-network-peering-overview.md)に関するページを参照してください。

### <a name="virtual-private-networking"></a>仮想プライベート ネットワーク

仮想ネットワークをオンプレミス サイトの場所に構成する場合と同じ方法で、仮想ネットワークを別の仮想ネットワークに (VNet 間) 接続することができます。 どちらの接続でも、VPN ゲートウェイを使用して、IPsec/IKE を使用してセキュリティで保護されたトンネルを作成します。 この接続モデルを使用すると、Azure AD DS を Azure 仮想ネットワークにデプロイし、オンプレミスの場所または他のクラウドに接続することができます。

![VPN Gateway を使用した仮想ネットワーク接続](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

仮想プライベート ネットワークの使用方法の詳細については、「[Azure ポータルを使用して VNet 間 VPN ゲートウェイ接続を構成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)」を参照してください。

## <a name="name-resolution-when-connecting-virtual-networks"></a>仮想ネットワークを接続するときの名前解決

Azure AD Domain Services 仮想ネットワークに接続される仮想ネットワークには、通常、独自の DNS 設定があります。 仮想ネットワークに接続しても、接続中の仮想ネットワークが Azure AD DS マネージド ドメインから提供されるサービスを解決するための名前解決は自動的に構成されません。 アプリケーション ワークロードが Azure AD Domain Services を見つけられるように、接続している仮想ネットワークの名前解決を構成する必要があります。

名前解決を有効にするには、接続している仮想ネットワークをサポートする DNS サーバー上で条件付き DNS フォワーダーを使用するか、Azure AD Domain Services 仮想ネットワークと同じ DNS IP アドレスを使用します。

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS によって使用されるネットワーク リソース

Azure AD DS マネージド ドメインでは、デプロイ時にいくつかのネットワーク リソースが作成されます。 これらのリソースは、Azure AD DS マネージド ドメインの正常な運用と管理のために必要であり、手動で構成することはできません。

| Azure リソース                          | 説明 |
|:----------------------------------------|:---|
| ネットワーク インターフェイス カード                  | Azure AD DS は、Windows Server 上で Azure VM として実行されている 2 つのドメイン コントローラー (DC) 上でマネージド ドメインをホストします。 各 VM には、仮想ネットワークのサブネットに接続する仮想ネットワーク インターフェイスがあります。 |
| 動的な Basic パブリック IP アドレス         | Azure AD DS は、Basic SKU のパブリック IP アドレスを使用して同期および管理サービスと通信します。 パブリック IP アドレスの詳細については、「[Azure における IP アドレスの種類と割り当て方法](../virtual-network/virtual-network-ip-addresses-overview-arm.md)」を参照してください。 |
| Azure Basic Load Balancer               | Azure AD DS では、ネットワーク アドレス変換 (NAT) および負荷分散 (セキュリティで保護された LDAP と共に使用する場合) に Basic SKU のロード バランサーを使用します。 Azure Load Balancer の詳細については、[Azure Load Balancer の概要](../load-balancer/load-balancer-overview.md)に関する記事を参照してください。 |
| ネットワーク アドレス変換 (NAT) 規則 | Azure AD DS では、ロード バランサーに対して 3 つの NAT 規則が作成され、使用されます。セキュリティで保護された HTTP トラフィックに関する 1 つの規則と、セキュリティで保護された PowerShell リモート処理に関する 2 つの規則です。 |
| 負荷分散規則                     | Azure AD DS マネージド ドメインが TCP ポート 636 上のセキュリティで保護された LDAP 用に構成されている場合、トラフィックを分散する 3 つの規則がロード バランサーに対して作成され、使用されます。 |

> [!WARNING]
> Azure AD DS によって作成されたネットワーク リソースは削除しないでください。 ネットワーク リソースのいずれかを削除すると、Azure AD DS サービスの停止が発生します。

## <a name="network-security-groups-and-required-ports"></a>ネットワーク セキュリティ グループと必要なポート

[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、Azure 仮想ネットワーク内のトラフィックへのネットワーク トラフィックを許可または拒否するルールの一覧が含まれています。 ネットワーク セキュリティ グループは、サービスが認証および管理機能を提供できるようにする一連の規則を含む Azure AD DS を展開するときに作成されます。 この既定のネットワーク セキュリティ グループは、Azure AD DS のマネージド ドメインがデプロイされる仮想ネットワーク サブネットに関連付けられています。

Azure AD DS で認証と管理サービスを提供するには、次のネットワーク セキュリティ グループの規則が必要です。 Azure AD DS マネージド ドメインが展開されている仮想ネットワーク サブネットのネットワーク セキュリティ グループ規則を編集または削除しないでください。

| ポート番号 | Protocol | Source                             | Destination | Action | 必須 | 目的 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | はい      | Azure AD テナントとの同期。 |
| 3389        | TCP      | CorpNetSaw                         | Any         | Allow  | はい      | ドメインの管理。 |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | はい      | ドメインの管理。 |
| 636         | TCP      | Any                                | Any         | Allow  | いいえ       | セキュリティで保護された LDAP (LDAPS) を構成するときにのみ有効になります。 |

> [!WARNING]
> これらのネットワーク リソースと構成を手動で編集しないでください。 正しく構成されていないネットワーク セキュリティ グループまたはユーザー定義のルート テーブルを、Azure AD DS が展開されているサブネットに関連付けると、Microsoft のドメインのサービスと管理の機能が中断する可能性があります。 Azure AD テナントと Azure AD DS マネージド ドメインの間の同期も中断されます。
>
> *AllowVnetInBound*、*AllowAzureLoadBalancerInBound*、*DenyAllInBound*、*AllowVnetOutBound*、*AllowInternetOutBound*、*DenyAllOutBound* の既定の規則もネットワーク セキュリティ グループに存在します。 これらの既定の規則は編集または削除しないでください。
>
> 不適切に構成されたネットワーク セキュリティ グループやユーザー定義のルート テーブルが適用され、Azure AD DS がドメインの更新と管理をブロックするようなデプロイには、Azure SLA は適用されません。

### <a name="port-443---synchronization-with-azure-ad"></a>ポート 443 - Azure AD との同期

* Azure AD テナントを Azure AD DS のマネージド ドメインと同期するために使用します。
* このポートにアクセスできない場合、Azure AD DS マネージド ドメインは Azure AD テナントと同期できません。 パスワードへの変更が Azure AD DS マネージド ドメインに同期されないため、ユーザーはサインインできない可能性があります。
* IP アドレスへのこのポートへの受信アクセスは、既定では **AzureActiveDirectoryDomainServices** サービス タグを使用して制限されます。
* このポートからの発信アクセスは制限しないでください。

### <a name="port-3389---management-using-remote-desktop"></a>ポート 3389 - リモート デスクトップを使用した管理

* Azure AD DS マネージド ドメイン内のドメイン コントローラーへのリモート デスクトップ接続に使用されます。
* 既定のネットワーク セキュリティ グループの規則では、*CorpNetSaw* サービス タグを使用してトラフィックがさらに制限されます。
    * このサービス タグでは、Microsoft 企業ネットワーク上のセキュリティで保護されたアクセス ワークステーションのみが、Azure AD DS マネージド ドメインへのリモート デスクトップを使用できます。
    * アクセスは、管理やトラブルシューティングのシナリオなど、業務上の正当な理由でのみ許可されます。
* この規則を *[拒否]* に設定し、必要な場合にのみ *[許可]* に設定することができます。 ほとんどの管理タスクと監視タスクは、PowerShell リモート処理を使用して実行されます。 RDP は、Microsoft が高度なトラブルシューティングのためにマネージド ドメインへのリモート接続が必要になるような頻度の低いイベントでのみ使用されます。

> [!NOTE]
> このネットワーク セキュリティ グループの規則を編集しようとすると、ポータルから *CorpNetSaw* サービス タグを手動で選択することはできません。 *CorpNetSaw* サービス タグを使用する規則を手動で構成するには、Azure PowerShell または Azure CLI を使用する必要があります。

### <a name="port-5986---management-using-powershell-remoting"></a>ポート 5986 - PowerShell リモート処理を使用した管理

* PowerShell のリモート処理を使用して Azure AD DS マネージド ドメインの管理タスクを実行するために使用されます。
* このポートにアクセスできない場合、Azure AD DS マネージド ドメインの更新、構成、バックアップおよび監視は行えません。
* Resource Manager ベースの仮想ネットワークを使用する Azure AD DS マネージド ドメインでは、このポートへの受信アクセスを *AzureActiveDirectoryDomainServices* サービス タグに制限することができます。
    * クラシックベースの仮想ネットワークを使用する従来の Azure AD DS マネージド ドメインでは、このポートへの受信アクセスを次の発信元 IP アドレスに制限することができます。*52.180.183.8*、*23.101.0.70*、*52.225.184.198*、*52.179.126.223*、*13.74.249.156*、*52.187.117.83*、*52.161.13.95*、*104.40.156.18*、*104.40.87.209*。

## <a name="user-defined-routes"></a>ユーザー定義のルート

ユーザー定義ルートは、既定では作成されず、Azure AD DS が正しく機能するためには必要ありません。 ルート テーブルを使用する必要がある場合は、*0.0.0.0* ルートに変更を加えないようにしてください。 このルートを変更すると、Azure AD Domain Services が中断する可能性があります。

また、それぞれの Azure サービス タグに含まれる IP アドレスからの受信トラフィックを Azure AD Domain Services サブネットにルーティングする必要があります。 サービス タグとそれに関連付けられている IP アドレスの詳細については、「[Azure の IP 範囲とサービス タグ - パブリック クラウド](https://www.microsoft.com/en-us/download/details.aspx?id=56519)」を参照してください。

> [!CAUTION]
> これらの Azure データセンターの IP 範囲は、予告なしに変わる可能性があります。 最新の IP アドレスを取得していることを検証するプロセスを用意してください。

## <a name="next-steps"></a>次の手順

Azure AD DS で使用されるネットワーク リソースと接続オプションの詳細については、次の記事を参照してください。

* [Azure 仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)

<!-- INTERNAL LINKS -->

<!-- EXTERNAL LINKS -->
