---
title: Azure AD Domain Services:ネットワークのガイドライン | Microsoft Docs
description: Azure Active Directory Domain Services のネットワークに関する考慮事項
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/01/2017
ms.author: ergreenl
ms.openlocfilehash: 3eba9f504f98d250ad60de3b4dd775effaad2040
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185552"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD Domain Services のネットワークに関する考慮事項
## <a name="how-to-select-an-azure-virtual-network"></a>Azure 仮想ネットワークを選択する方法
次のガイドラインは、Azure AD Domain Services で使用する仮想ネットワークを選択する際に役立ちます。

### <a name="type-of-azure-virtual-network"></a>Azure 仮想ネットワークの種類
* **Resource Manager の仮想ネットワーク**:Azure AD Domain Services は、Azure Resource Manager を使って作成された仮想ネットワークでは有効にできます。
* 従来の Azure 仮想ネットワークで Azure AD Domain Services を有効にすることはできません。
* Azure AD Domain Services が有効になっている仮想ネットワークに他の仮想ネットワークを接続することができます。 詳細については、「[ネットワーク接続](active-directory-ds-networking.md#network-connectivity)」を参照してください。

### <a name="azure-region-for-the-virtual-network"></a>仮想ネットワークの Azure リージョン
* Azure AD Domain Services のマネージド ドメインは、サービスを有効にすることを選択した仮想ネットワークと同じ Azure リージョンにデプロイされます。
* Azure AD Domain Services でサポートされている Azure リージョンの仮想ネットワークを選択します。
* Azure AD Domain Services を使用できる Azure リージョンを確認するには、 [リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/) に関するページを参照してください。

### <a name="requirements-for-the-virtual-network"></a>仮想ネットワークの要件
* **Azure ワークロードへの近さ**:Azure AD Domain Services にアクセスする必要のある仮想マシンを現在ホストしている (または今後ホストする予定の) 仮想ネットワークを選択します。 マネージド ドメインとは異なる仮想ネットワークにワークロードがデプロイされている場合は、仮想ネットワークを接続することも選択できます。
* **カスタムまたは持ち込みの DNS サーバー**:仮想ネットワーク用にカスタムの DNS サーバーが構成されていないことを確認します。 カスタム DNS サーバーの例は、仮想ネットワークにデプロイした Windows Server VM で実行されている Windows Server DNS のインスタンスです。 Azure AD Domain Services は、仮想ネットワーク内にデプロイされているカスタム DNS サーバーと統合されません。
* **同じドメイン名を持つ既存のドメイン**:その仮想ネットワークで使用できるドメインと同じ名前のドメインがないことを確認します。 たとえば、選択した仮想ネットワークで既に利用可能な "contoso.com" という名前のドメインがあると仮定します。 その後、その仮想ネットワークでこれと同じドメイン名 (つまり "contoso.com") で、Azure AD Domain Services のマネージド ドメインを有効にしようとします。 Azure AD Domain Services を有効にしようとすると、エラーが発生します。 このエラーの原因は、仮想ネットワークのドメイン名で名前が競合していることにあります。 この場合、Azure AD Domain Services のマネージド ドメインを設定するには、別の名前を使用する必要があります。 または、既存のドメインのプロビジョニングを解除してから、Azure AD ドメイン サービスの有効化に進みます。

> [!WARNING]
> Domain Services は、有効にした後、別の仮想ネットワークに移動できません。
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>サブネットの選択に関するガイドライン

![Recommended subnet design](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Azure AD Domain Services は、Azure 仮想ネットワーク内の**別の専用サブネット**にデプロイします。
* マネージド ドメインの専用サブネットに NSG を適用しないでください。 この専用サブネットに NSG を適用する必要がある場合は、**ドメインのサービス提供および管理に必要なポートがブロックされていない**ことを確認してください。
* マネージド ドメインの専用サブネット内で使用できる IP アドレスの数を過度に制限しないでください。 このような制限により、サービスがマネージド ドメインに対して 2 つのドメイン コントローラーを利用できなくなります。
* 仮想ネットワークの**ゲートウェイ サブネットでは Azure AD Domain Services を有効にしないでください**。

> [!WARNING]
> Azure AD Domain Services が有効になっているサブネットに NSG を関連付けると、Microsoft によるドメインのサービス提供および管理機能が中断される可能性があります。 さらに、Azure AD テナントとマネージド ドメインの間の同期が中断されます。 **SLA は、Azure AD Domain Services によるドメインの更新と管理をブロックする NSG が適用されているデプロイには適用されません。**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Azure AD Domain Services に必要なポート
Azure AD Domain Services によるマネージド ドメインのサービス提供および管理には、次のポートが必要です。 マネージド ドメインを有効にしたサブネットに対してこれらのポートがブロックされていないことを確認してください。

| ポート番号 | 必須 | 目的 |
| --- | --- | --- |
| 443 | 必須 |Azure AD テナントとの同期 |
| 5986 | 必須 | ドメインの管理 |
| 3389 | 必須 | ドメインの管理 |
| 636 | 省略可能 | マネージド ドメインへのセキュリティで保護された LDAP (LDAPS) アクセス |

**ポート 443 (Azure AD との同期)**
* マネージド ドメインと Azure AD ディレクトリを同期するために使用されます。
* NSG でこのポートへのアクセスを許可する必要があります。 このポートにアクセスできない場合、マネージド ドメインは Azure AD ディレクトリと同期されません。 ユーザーのパスワードがマネージド ドメインと同期されていないために、ユーザーがサインインできないことがあります。
* このポートへの受信アクセスを、Azure の IP アドレス範囲に属する IP アドレスに限定できます。 Azure の IP アドレス範囲は、以下の規則で示されている PowerShell の範囲とは異なることに注意してください。

**Port 5986 (PowerShell リモート処理)**
* PowerShell のリモート処理を使用してマネージド ドメインの管理タスクを実行するために使用されます。
* NSG でこのポートを利用するアクセスを許可する必要があります。 このポートにアクセスできない場合、マネージド ドメインの更新、構成、バックアップおよび監視は行えません。
* すべての新しいドメインまたは Azure Resource Manager 仮想ネットワークを使用するドメインでは、このポートへの受信アクセスを次のソース IP アドレスに制限できます。52.180.179.108、52.180.177.87、13.75.105.168、52.175.18.134、52.138.68.41、52.138.65.157、104.41.159.212、104.45.138.161、52.169.125.119、52.169.218.0、52.187.19.1、52.187.120.237、13.78.172.246、52.161.110.169、52.174.189.149、40.68.160.142、40.83.144.56、13.64.151.161、52.180.183.67、52.180.181.39、52.175.28.111、52.175.16.141、52.138.70.93、52.138.64.115、40.80.146.22、40.121.211.60、52.138.143.173、52.169.87.10、13.76.171.84、52.187.169.156、13.78.174.255、13.78.191.178、40.68.163.143、23.100.14.28、13.64.188.43、23.99.93.197
* クラシック仮想ネットワークを使用するドメインでは、このポートへの受信アクセスを次のソース IP アドレスに制限できます。52.180.183.8、23.101.0.70、52.225.184.198、52.179.126.223、13.74.249.156、52.187.117.83、52.161.13.95、104.40.156.18、104.40.87.209
* マネージド ドメインのドメイン コントローラーは、通常はこのポートをリッスンしません。 サービスは、管理操作またはメンテナンス操作をマネージド ドメインに対して実行する必要がある場合にのみ、マネージド ドメイン コントローラー上のこのポートを開きます。 操作が完了すると、サービスはすぐにマネージド ドメイン コントローラー上のこのポートを停止します。

**ポート 3389 (リモート デスクトップ)**
* マネージド ドメインのドメイン コントローラーへのリモート デスクトップ接続に使用されます。
* 受信アクセスを次のソース IP アドレスに制限できます。207.68.190.32/27、13.106.78.32/27、13.106.174.32/27、13.106.4.96/27
* このポートも、マネージド ドメイン上でほとんど無効な状態を保持します。 管理タスクと監視タスクは PowerShell のリモート処理を使用して実行されるため、この仕組みは継続的には使用されません。 このポートは、Microsoft が高度なトラブルシューティングのためにマネージド ドメインへのリモート接続が必要になるような頻度の低いイベントでのみ使用されます。 トラブルシューティングの操作が完了すると、ポートはただちに閉じられます。

**ポート 636 (Secure LDAP)**
* インターネット経由でマネージド ドメインへの Secure LDAP によるアクセスを有効にするために使用されます。
* NSG でこのポートを開くかどうかは任意です。 インターネット経由で Secure LDAP によるアクセスを有効にしている場合にのみ、ポートを開きます。
* このポートへの受信アクセスを、Secure LDAP での接続が見込まれる送信元 IP アドレスに限定できます。


## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-networks-nsg.md) には、仮想ネットワークの VM インスタンスに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) 規則が含まれています。 NSG は、サブネットまたはそのサブネット内の個々の VM インスタンスと関連付けることができます。 NSG がサブネットに関連付けられている場合、ACL 規則はそのサブネット内のすべての VM インスタンスに適用されます。 また、NSG を直接 VM に関連付けることにより、その個々の VM に対するトラフィックをさらに制限できます。

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Azure AD Domain Services を使用する仮想ネットワークのサンプル NSG
次の表は、Azure AD Domain Services のマネージド ドメインを使用して仮想ネットワークを構成できるサンプル NSG を示しています。 この規則によって、必須ポートからの受信トラフィックでマネージド ドメインへの修正プログラムや更新プログラムを適用し、Microsoft による監視を可能にしています。 既定の 'DenyAll' ルールは、インターネットから入ってくるその他すべてのトラフィックに適用されます。

また、この NSG では、インターネット経由での、セキュリティで保護された LDAP アクセスをロック ダウンする方法も示しています。 インターネット経由でのマネージド ドメインへのセキュリティで保護された LDAP アクセスを行えないようにしている場合は、この規則についてはスキップしてください。 この NSG には、指定した IP アドレスから TCP ポート 636 経由で入ってくる LDAPS アクセスのみを許可するルール セットが含まれています。 指定した IP アドレスからインターネット経由で入ってくる LDAPS アクセスを許可する NSG ルールには、DenyAll NSG ルールより高い優先度が設定されています。

![サンプル NSG。セキュリティで保護された LDAPS を利用し、インターネット経由でアクセスします。](./media/active-directory-domain-services-alerts/default-nsg.png)

**詳細** - [ネットワーク セキュリティ グループの作成](../virtual-network/manage-network-security-group.md)


## <a name="network-connectivity"></a>ネットワーク接続
Azure AD Domain Services のマネージド ドメインは、Azure の 1 つの仮想ネットワーク内のみで有効にすることができます。

### <a name="scenarios-for-connecting-azure-networks"></a>Azure ネットワークを接続するためのシナリオ
次のいずれかのデプロイ シナリオで Azure 仮想ネットワークを接続してマネージド ドメインを使用します。

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>複数の Azure 仮想ネットワークでマネージド ドメインを使う
Azure AD Domain Services を有効にした Azure 仮想ネットワークには他の Azure 仮想ネットワークを接続できます。 この VPN/VNet ピアリング接続により、ワークロードが他の仮想ネットワークにデプロイされているマネージド ドメインを使用できます。

![Classic virtual network connectivity](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Resource Manager ベースの仮想ネットワークでマネージド ドメインを使用する
Resource Manager ベースの仮想ネットワークを、Azure AD Domain Services を有効にした Azure クラシック仮想ネットワークに接続できます。 この接続により、ワークロードが Resource Manager ベースの仮想ネットワークにデプロイされているマネージド ドメインを使用できます。

![Resource Manager to classic virtual network connectivity](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>ネットワーク接続オプション
* **仮想ネットワーク ピアリングを使用した VNet 間接続**:仮想ネットワーク ピアリングとは、同じリージョンに存在する 2 つの仮想ネットワークを Azure のバックボーン ネットワークを介して接続する機構です。 ピアリングされた 2 つの仮想ネットワークは、あらゆる接続において、見かけ上 1 つのネットワークとして機能します。 これらの仮想ネットワークはあくまで個別のリソースとして管理されますが、そこに存在する仮想マシンは互いに、プライベート IP アドレスを使用して直接通信を行うことができます。

    ![Virtual network connectivity using peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [関連情報 - 仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)

* **サイト間 VPN 接続を使用した VNet 間接続**:仮想ネットワーク間 (VNet 間) の接続は、仮想ネットワークをオンプレミスのサイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。

    ![Virtual network connectivity using VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [関連情報 - VPN ゲートウェイを使用した仮想ネットワークの接続](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>関連コンテンツ
* [Azure 仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)
* [クラシック デプロイ モデルで VNet 対 VNet 接続を構成する](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Azure ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)
* [ネットワーク セキュリティ グループの作成](../virtual-network/manage-network-security-group.md)
