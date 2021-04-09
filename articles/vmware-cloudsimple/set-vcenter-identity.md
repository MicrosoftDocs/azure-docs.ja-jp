---
title: Azure VMware Solution by CloudSimple - プライベート クラウドで vCenter の ID ソースを設定する
description: vCenter にアクセスする VMware 管理者を Active Directory で認証するようにプライベート クラウドの vCenter を設定する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a76fecb942c5c6da926e37149245e82dcbc4661b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899152"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Active Directory を使用するための vCenter ID ソースの設定

## <a name="about-vmware-vcenter-identity-sources"></a>VMware vCenter の ID ソースについて

VMware vCenter は、vCenter にアクセスするユーザーを認証するためのさまざまな ID ソースをサポートしています。  vCenter にアクセスする VMware 管理者を Active Directory で認証するように、CloudSimple プライベート クラウドの vCenter を設定することができます。 設定が完了したら、**cloudowner** ユーザーは ID ソースから vCenter にユーザーを追加できます。  

Active Directory ドメインとドメイン コントローラーは、次のいずれかの方法で設定できます。

* オンプレミスで実行される Active Directory ドメインとドメイン コントローラー
* ご利用の Azure サブスクリプションで仮想マシンとして Azure 上で実行される Active Directory ドメインとドメイン コントローラー
* プライベート クラウドで実行される新しい Active Directory ドメインとドメイン コントローラー
* Azure Active Directory サービス

このガイドでは、オンプレミスで実行される、またはご利用のサブスクリプションで仮想マシンとして実行される Active Directory ドメインとドメイン コントローラーを設定するタスクについて説明します。  Azure AD を ID ソースとして使用する場合、ID ソースの設定の詳細な手順については、[CloudSimple プライベート クラウドで vCenter の ID プロバイダーとして Azure AD を使用する](azure-ad.md)方法に関する記事を参照してください。

[ID ソースを追加](#add-an-identity-source-on-vcenter)する前に、一時的に [vCenter 特権をエスカレート](escalate-private-cloud-privileges.md)します。

> [!CAUTION]
> 新しいユーザーは、*Cloud-Owner-Group*、*Cloud-Global-Cluster-Admin-Group*、*Cloud-Global-Storage-Admin-Group*、*Cloud-Global-Network-Admin-Group*、または *Cloud-Global-VM-Admin-Group* にのみ追加する必要があります。  *Administrators* グループに追加されたユーザーは自動的に削除されます。  *[管理者]* グループに追加する必要があるのはサービス アカウントだけです。また、サービス アカウントを使用して vSphere Web UI にサインインすることはできません。   


## <a name="identity-source-options"></a>ID ソースのオプション

* [オンプレミスの Active Directory をシングル サインオンの ID ソースとして追加する](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [プライベート クラウド上で新しい Active Directory を設定する](#set-up-new-active-directory-on-a-private-cloud)
* [Azure 上で Active Directory を設定する](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **Active Directory (Windows 統合認証) はサポートされていません。** ID ソースとしてサポートされるのは、LDAP オプションを使った Active Directory のみです。

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>オンプレミスの Active Directory をシングル サインオンの ID ソースとして追加する

オンプレミスの Active Directory をシングル サインオンの ID ソースとして設定するには、次のものが必要です。

* オンプレミスのデータセンターからプライベート クラウドへの[サイト間 VPN 接続](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* vCenter と Platform Services Controller (PSC) に追加されるオンプレミスの DNS サーバーの IP。

Active Directory ドメインを設定するときは、次の表の情報を使用します。

| **オプション** | **説明** |
|------------|-----------------|
| **名前** | ID ソースの名前。 |
| **Base DN for users** (ユーザーのベース DN) | ユーザーのベース識別名。 |
| **ドメイン名** | ドメインの FQDN (例: example.com)。 このテキスト ボックスには IP アドレスを指定しないでください。 |
| **Domain alias** (ドメイン エイリアス) | ドメインの NetBIOS 名。 SSPI 認証を使用している場合は、Active Directory ドメインの NetBIOS 名を ID ソースのエイリアスとして追加します。 |
| **Base DN for groups** (グループのベース DN) | グループのベース識別名。 |
| **Primary Server URL** (プライマリ サーバーの URL) | ドメインのプライマリ ドメイン コントローラー LDAP サーバー。<br><br>`ldap://hostname:port` または `ldaps://hostname:port` の形式を使用します。 このポートは通常、LDAP 接続の場合は 389、LDAPS 接続の場合は 636 です。 Active Directory マルチドメイン コントローラー デプロイでは、ポートは通常、LDAP の場合は 3268、LDAPS の場合は 3269 です。<br><br>プライマリまたはセカンダリの LDAP URL で `ldaps://` を使用するときは、Active Directory サーバーの LDAPS エンドポイントに対して信頼を確立する証明書が必要です。 |
| **Secondary server URL** (セカンダリ サーバーの URL) | フェールオーバーに使用するセカンダリ ドメイン コントローラー LDAP サーバーのアドレス。 |
| **Choose certificate** (証明書の選択) | Active Directory LDAP サーバーまたは OpenLDAP サーバーの ID ソースで LDAPS を使用する場合は、URL テキスト ボックスに「`ldaps://`」と入力すると、[証明書の選択] ボタンが表示されます。 セカンダリの URL は必須ではありません。 |
| **ユーザー名** | ユーザーおよびグループのベース DN への最小限の読み取り専用アクセス権を持つドメイン内のユーザーの ID。 |
| **パスワード** | [ユーザー名] で指定したユーザーのパスワード。 |

前の表の情報がそろったら、vCenter でオンプレミスの Active Directory をシングル サインオンの ID ソースとして追加することができます。

> [!TIP]
> シングル サインオンの ID ソースの詳細については、[VMware のドキュメント ページ](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)を参照してください。

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>プライベート クラウド上で新しい Active Directory を設定する

プライベート クラウド上で新しい Active Directory ドメインを設定し、シングル サインオンの ID ソースとして使用することができます。  Active Directory ドメインは、既存の Active Directory フォレストの一部にすることも、独立したフォレストとして設定することもできます。

### <a name="new-active-directory-forest-and-domain"></a>新しい Active Directory フォレストとドメイン

新しい Active Directory フォレストとドメインを設定するには、次のものが必要です。

* 新しい Active Directory フォレストとドメインのドメイン コントローラーとして使用する、Microsoft Windows Server を実行している 1 つ以上の仮想マシン。
* 名前解決用の DNS サービスを実行している 1 つ以上の仮想マシン。

詳細な手順については、「[Windows Server 2012 の新しい Active Directory フォレストをインストールする](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)」を参照してください。

> [!TIP]
> サービスの可用性を高めるために、複数のドメイン コントローラーと DNS サーバーを設定することをお勧めします。

Active Directory フォレストとドメインを設定した後、新しい Active Directory に対する [vCenter 上の ID ソースを追加](#add-an-identity-source-on-vcenter)することができます。

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>既存の Active Directory フォレスト内の新しい Active Directory ドメイン

既存の Active Directory フォレスト内に新しい Active Directory ドメインを設定するには、次のものが必要です。

* Active Directory フォレストの場所へのサイト間 VPN 接続。
* 既存の Active Directory フォレストの名前を解決するための DNS サーバー。

詳細な手順については、「[Windows Server 2012 の新しい Active Directory 子ドメインまたはツリー ドメインをインストールする](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)」を参照してください。

Active Directory ドメインを設定した後、新しい Active Directory に対する [vCenter 上の ID ソースを追加](#add-an-identity-source-on-vcenter)することができます。

## <a name="set-up-active-directory-on-azure"></a>Azure で Active Directory を設定する

Azure で実行される Active Directory は、オンプレミスで実行される Active Directory に似ています。  Azure で実行されている Active Directory を vCenter でシングル サインオンの ID ソースとして設定するには、vCenter サーバーと PSC に、Active Directory サービスが稼働している Azure Virtual Network へのネットワーク接続が必要です。  この接続を確立するには、Active Directory Services が実行されている Azure 仮想ネットワークと CloudSimple プライベート クラウドを結ぶ、[ExpressRoute を用いた Azure Virtual Network Connection](azure-expressroute-connection.md) を使用します。

ネットワーク接続が確立されたら、[オンプレミスの Active Directory をシングル サインオンの ID ソースとして追加する](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)方法に関するページの手順を参照して、その接続を ID ソースとして追加します。  

## <a name="add-an-identity-source-on-vcenter"></a>vCenter 上の ID ソースを追加する

1. プライベート クラウドに対する[特権をエスカレート](escalate-private-cloud-privileges.md)します。

2. プライベート クラウドの vCenter にサインインします。

3. **[ホーム] > [管理]** を選択します。

    ![管理](media/OnPremAD01.png)

4. **[シングル サインオン] > [Configuration]\(構成\)** を選択します。

    ![シングル サインオン](media/OnPremAD02.png)

5. **[Identity Sources]\(ID ソース\)** タブを開き、**+** をクリックして新しい ID ソースを追加します。

    ![ID ソース](media/OnPremAD03.png)

6. **[Active Directory as an LDAP Server]\(LDAP サーバーとしての Active Directory\)** を選択して **[Next]\(次へ\)** をクリックします。

    ![Active Directory が LDAP サーバー オプションとして強調表示されているスクリーンショット。](media/OnPremAD04.png)

7. ご利用の環境の ID ソース パラメーターを指定して **[Next]\(次へ\)** をクリックします。

    ![Active Directory](media/OnPremAD05.png)

8. 設定を確認し、 **[Finish]\(完了\)** をクリックします。
