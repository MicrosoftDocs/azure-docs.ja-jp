---
title: "Azure AD Domain Services: ネットワーク ガイドライン | Microsoft Docs"
description: "Azure Active Directory Domain Services のネットワークに関する考慮事項"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 08ea5f557498f64825da8fe03d146cace0c53526
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD Domain Services のネットワークに関する考慮事項
## <a name="how-to-select-an-azure-virtual-network"></a>Azure 仮想ネットワークを選択する方法
次のガイドラインは、Azure AD Domain Services で使用する仮想ネットワークを選択する際に役立ちます。

### <a name="type-of-azure-virtual-network"></a>Azure 仮想ネットワークの種類
* クラシック Azure 仮想ネットワークで Azure AD Domain Services を有効にすることができます。 ただし、クラシック仮想ネットワークのサポートは間もなく廃止される予定です。 新しく作成される管理対象ドメインには、Resource Manager 仮想ネットワークを使うことをお勧めします。
* Azure AD Domain Services は、Azure Resource Manager を使って作成された仮想ネットワークでは有効にできます。
* Azure AD Domain Services が有効になっている仮想ネットワークに他の仮想ネットワークを接続することはできません。 詳細については、「[ネットワーク接続](active-directory-ds-networking.md#network-connectivity)」を参照してください。
* **リージョン仮想ネットワーク**: 既存の仮想ネットワークを使用する予定がある場合は、リージョン仮想ネットワークであることを確認してください。

  * 従来のアフィニティ グループ機構を使った仮想ネットワークは、Azure AD ドメイン サービスでは使用できません。
  * Azure AD Domain Services を使用するには、 [従来の仮想ネットワークをリージョン仮想ネットワークに移行してください](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。

### <a name="azure-region-for-the-virtual-network"></a>仮想ネットワークの Azure リージョン
* Azure AD Domain Services の管理対象ドメインは、サービスを有効にすることを選択した仮想ネットワークと同じ Azure リージョンにデプロイされます。
* Azure AD Domain Services でサポートされている Azure リージョンの仮想ネットワークを選択します。
* Azure AD Domain Services を使用できる Azure リージョンを確認するには、 [リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/) に関するページを参照してください。

### <a name="requirements-for-the-virtual-network"></a>仮想ネットワークの要件
* **Azure ワークロードへの近さ**: Azure AD Domain Services にアクセスする必要のある仮想マシンを現在ホストしている (または今後ホストする予定の) 仮想ネットワークを選択します。 管理対象ドメインとは異なる仮想ネットワークにワークロードがデプロイされている場合は、仮想ネットワークを接続することも選択できます。
* **カスタムまたは持ち込みの DNS サーバー**: 仮想ネットワーク用にカスタムの DNS サーバーが構成されていないことを確認します。 カスタム DNS サーバーの例は、仮想ネットワークにデプロイした Windows Server VM で実行されている Windows Server DNS のインスタンスです。 Azure AD Domain Services は、仮想ネットワーク内にデプロイされているカスタム DNS サーバーと統合されません。
* **同じドメイン名を持つ既存のドメイン**: 仮想ネットワークで使用できるドメインと同じ名前の既存のドメインがないことを確認します。 たとえば、選択した仮想ネットワークで既に利用可能な "contoso.com" という名前のドメインがあると仮定します。 その後、その仮想ネットワークでこれと同じドメイン名 (つまり "contoso.com") で、Azure AD Domain Services の管理対象ドメインを有効にしようとします。 Azure AD Domain Services を有効にしようとすると、エラーが発生します。 このエラーの原因は、仮想ネットワークのドメイン名で名前が競合していることにあります。 この場合、Azure AD ドメイン サービスの管理対象ドメインを設定するには、別の名前を使用する必要があります。 または、既存のドメインのプロビジョニングを解除してから、Azure AD ドメイン サービスの有効化に進みます。

> [!WARNING]
> Domain Services は、有効にした後、別の仮想ネットワークに移動できません。
>
>

## <a name="network-security-groups-and-subnet-design"></a>ネットワーク セキュリティ グループとサブネットの設計
[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-networks-nsg.md) には、仮想ネットワークの VM インスタンスに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) 規則が含まれています。 NSG は、サブネットまたはそのサブネット内の個々の VM インスタンスと関連付けることができます。 NSG がサブネットに関連付けられている場合、ACL 規則はそのサブネット内のすべての VM インスタンスに適用されます。 また、NSG を直接 VM に関連付けることにより、その個々の VM に対するトラフィックをさらに制限できます。

![Recommended subnet design](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

### <a name="best-practices-for-choosing-a-subnet"></a>サブネットの選択に関するベスト プラクティス
* Azure AD Domain Services は、Azure 仮想ネットワーク内の**別の専用サブネット**にデプロイします。
* 管理対象ドメインの専用サブネットに NSG を適用しないでください。 この専用サブネットに NSG を適用する必要がある場合は、**ドメインのサービス提供および管理に必要なポートがブロックされていない**ことを確認してください。
* 管理対象ドメインの専用サブネット内で使用できる IP アドレスの数を過度に制限しないでください。 このような制限により、サービスが管理対象ドメインに対して 2 つのドメイン コントローラーを利用できなくなります。
* 仮想ネットワークの**ゲートウェイ サブネットでは Azure AD Domain Services を有効にしないでください**。

> [!WARNING]
> Azure AD Domain Services が有効になっているサブネットに NSG を関連付けると、Microsoft によるドメインのサービス提供および管理機能が中断される可能性があります。 さらに、Azure AD テナントと管理対象ドメインの間の同期が中断されます。 **SLA は、Azure AD Domain Services によるドメインの更新と管理をブロックする NSG が適用されているデプロイには適用されません。**
>
>

### <a name="ports-required-for-azure-ad-domain-services"></a>Azure AD Domain Services に必要なポート
Azure AD Domain Services による管理対象ドメインのサービス提供および管理には、次のポートが必要です。 管理対象ドメインを有効にしたサブネットに対してこれらのポートがブロックされていないことを確認してください。

| ポート番号 | 目的 |
| --- | --- |
| 443 |Azure AD テナントとの同期 |
| 3389 |ドメインの管理 |
| 5986 |ドメインの管理 |
| 636 |管理対象ドメインへのセキュリティで保護された LDAP (LDAPS) アクセス |

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Azure AD Domain Services を使用する仮想ネットワークのサンプル NSG
次の表は、Azure AD Domain Services の管理対象ドメインを使用して仮想ネットワークを構成できるサンプル NSG を示しています。 この規則は、上記の指定ポートからの受信トラフィックで、Microsoft による修正プログラムの適用、更新、監視が行われる管理対象ドメインを確認できるようにします。 既定の 'DenyAll' ルールは、インターネットから入ってくるその他すべてのトラフィックに適用されます。

また、この NSG では、インターネット経由での、セキュリティで保護された LDAP アクセスをロック ダウンする方法も示しています。 インターネット経由での管理対象ドメインへのセキュリティで保護された LDAP アクセスを行えないようにしている場合は、この規則についてはスキップしてください。 この NSG には、指定した IP アドレスから TCP ポート 636 経由で入ってくる LDAPS アクセスのみを許可するルール セットが含まれています。 指定した IP アドレスからインターネット経由で入ってくる LDAPS アクセスを許可する NSG ルールには、DenyAll NSG ルールより高い優先度が設定されています。

![サンプル NSG。セキュリティで保護された LDAPS を利用し、インターネット経由でアクセスします。](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**詳細** - [ネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)


## <a name="network-connectivity"></a>ネットワーク接続
Azure AD Domain Services の管理対象ドメインは、Azure の 1 つの仮想ネットワーク内のみで有効にすることができます。

### <a name="scenarios-for-connecting-azure-networks"></a>Azure ネットワークを接続するためのシナリオ
次のいずれかのデプロイ シナリオで Azure 仮想ネットワークを接続して管理対象ドメインを使用します。

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>複数の Azure 仮想ネットワークで管理対象ドメインを使う
Azure AD Domain Services を有効にした Azure 仮想ネットワークには他の Azure 仮想ネットワークを接続できます。 この VPN/VNet ピアリング接続により、ワークロードが他の仮想ネットワークにデプロイされている管理対象ドメインを使用できます。

![Classic virtual network connectivity](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Resource Manager ベースの仮想ネットワークで管理対象ドメインを使用する
Resource Manager ベースの仮想ネットワークを、Azure AD Domain Services を有効にした Azure クラシック仮想ネットワークに接続できます。 この接続により、ワークロードが Resource Manager ベースの仮想ネットワークにデプロイされている管理対象ドメインを使用できます。

![Resource Manager to classic virtual network connectivity](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>ネットワーク接続オプション
* **仮想ネットワーク ピアリングを使用した VNet 間接続**: 仮想ネットワーク ピアリングとは、同じリージョンに存在する 2 つの仮想ネットワークを Azure のバックボーン ネットワークを介して接続する機構です。 ピアリングされた 2 つの仮想ネットワークは、あらゆる接続において、見かけ上 1 つのネットワークとして機能します。 これらの仮想ネットワークはあくまで個別のリソースとして管理されますが、そこに存在する仮想マシンは互いに、プライベート IP アドレスを使用して直接通信を行うことができます。

    ![Virtual network connectivity using peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [関連情報 - 仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)
    
* **サイト間 VPN 接続を使用した VNet 間接続**: 仮想ネットワーク間 (VNet 間) の接続は、仮想ネットワークをオンプレミスのサイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。

    ![Virtual network connectivity using VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [関連情報 - VPN ゲートウェイを使用した仮想ネットワークの接続](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>関連コンテンツ
* [Azure 仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)
* [クラシック デプロイ モデルで VNet 対 VNet 接続を構成する](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Azure ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)
* [ネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

