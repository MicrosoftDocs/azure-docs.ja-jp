---
title: Azure VMware Solution by CloudSimple - 安全なプライベート クラウド
description: Azure VMware Solution by CloudSimple の安全なプライベート クラウドを作成する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e70745cd6e2f6a2a13581052f65e014bd0d0481
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899169"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>プライベート クラウド環境をセキュリティで保護する方法

Azure のプライベート クラウド、CloudSimple サービス、CloudSimple ポータルに対してロールベースのアクセス制御 (RBAC) を定義します。  プライベート クラウドの vCenter にアクセスするためのユーザー、グループ、ロールは、VMware SSO を使用して指定します。  

## <a name="azure-rbac-for-cloudsimple-service"></a>CloudSimple サービスの Azure RBAC

CloudSimple サービスの作成には、Azure サブスクリプションの **所有者** ロールまたは **共同作成者** ロールが必要です。  既定では、すべての所有者および共同作成者が CloudSimple サービスを作成し、CloudSimple ポータルにアクセスして、プライベート クラウドを作成したり管理したりすることができます。  作成できる CloudSimple サービスは、各リージョンにつき 1 つだけです。  特定の管理者にアクセスを限定するには、次の手順に従ってください。

1. Azure portal で新しい **リソース グループ** に CloudSimple サービスを作成します。
2. リソース グループの Azure RBAC を指定します。
3. ノードを購入し、CloudSimple サービスと同じリソース グループを使用します。

以後、CloudSimple サービスの表示と CloudSimple ポータルの起動は、このリソース グループの **所有者** と **共同作成者** の特権を持つユーザーに限定されます。

詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。

## <a name="rbac-for-private-cloud-vcenter"></a>プライベート クラウドの vCenter の RBAC

プライベート クラウドを作成すると、vCenter SSO ドメインに既定のユーザー `CloudOwner@cloudsimple.local` が作成されます。  CloudOwner ユーザーには、vCenter を管理するための特権が与えられます。 vCenter SSO には、さまざまなユーザーにアクセス権を付与するための ID ソースが別途追加されます。  vCenter には、新たにユーザーを追加する際に使用できる定義済みのロールとグループが設定されます。

### <a name="add-new-users-to-vcenter"></a>vCenter に新しいユーザーを追加する

1. プライベート クラウドの **CloudOwner\@cloudsimple.local** ユーザーの [権限をエスカレート](escalate-private-cloud-privileges.md)します。
2. **CloudOwner\@cloudsimple.local** を使用して vCenter にサインインします
3. [vCenter シングル サインオン ユーザーを追加](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)します。
4. [vCenter シングル サインオン グループ](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)にユーザーを追加します。

定義済みのロールとグループについて詳しくは、「[VMware vCenter の CloudSimple プライベート クラウド アクセス許可モデル](learn-private-cloud-permissions.md)」の記事を参照してください。

### <a name="add-new-identity-sources"></a>新しい ID ソースを追加する

プライベート クラウドの vCenter SSO ドメインに使用する ID プロバイダーを別途追加できます。  ID プロバイダーは認証の働きをし、また、vCenter SSO グループはユーザーの承認の働きをします。

* プライベート クラウドの vCenter の [ID プロバイダーとして Active Directory を使用](set-vcenter-identity.md)する。
* プライベート クラウドの vCenter の [ID プロバイダーとして Azure AD を使用](azure-ad.md)する。

1. プライベート クラウドの **CloudOwner\@cloudsimple.local** ユーザーの [権限をエスカレート](escalate-private-cloud-privileges.md)します。
2. **CloudOwner\@cloudsimple.local** を使用して vCenter にサインインします
3. ID プロバイダーから [vCenter シングル サインオン グループ](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)にユーザーを追加します。

## <a name="secure-network-on-your-private-cloud-environment"></a>プライベート クラウド環境のネットワークをセキュリティで保護する

プライベート クラウド環境のネットワーク セキュリティは、ネットワーク アクセスのセキュリティを保護し、リソース間のネットワーク トラフィックをコントロールすることで制御されます。

### <a name="access-to-private-cloud-resources"></a>プライベート クラウド リソースへのアクセス

プライベート クラウドの vCenter とリソースへのアクセスは、セキュリティで保護されたネットワーク接続上で行われます。

* **[ExpressRoute 接続](on-premises-connection.md)** 。 オンプレミス環境との間には、ExpressRoute によって待ち時間の短い高帯域幅の安全な接続が確保されます。  オンプレミスのサービス、ネットワーク、ユーザーは、この接続を使用してプライベート クラウドの vCenter にアクセスすることができます。
* **[サイト間 VPN ゲートウェイ](vpn-gateway.md)** 。 サイト間 VPN により、オンプレミスから安全なトンネルを介してプライベート クラウドのリソースにアクセスできます。  どのオンプレミス ネットワークがプライベート クラウドへのネットワーク トラフィックを送受信できるかは、ユーザーが指定します。
* **[ポイント対サイト VPN ゲートウェイ](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** 。 プライベート クラウドの vCenter に対して迅速にリモート アクセスするには、ポイント対サイト VPN 接続を使用します。

### <a name="control-network-traffic-in-private-cloud"></a>プライベート クラウドのネットワーク トラフィックを制御する

プライベート クラウドのネットワーク トラフィックは、ファイアウォール テーブルとファイアウォール規則によって制御します。  ファイアウォール テーブルに定義された規則の組み合わせに基づいて、送信元ネットワーク アドレス (IP アドレス) と送信先ネットワーク アドレス (IP アドレス) との間のネットワーク トラフィックを制御できます。

1. [ファイアウォール テーブル](firewall.md#add-a-new-firewall-table)を作成します。
2. ファイアウォール テーブルに[規則を追加](firewall.md#create-a-firewall-rule)します。
3. [ファイアウォール テーブルを VLAN/サブネット](firewall.md#attach-vlans-subnet)に接続します。
