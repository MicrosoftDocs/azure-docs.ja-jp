---
title: Secure LDAP (LDAPS) を使用して Azure AD Domain Services マネージド ドメインにバインドする | Microsoft Docs
description: Secure LDAP (LDAPS) を使用して Azure AD Domain Services マネージド ドメインにバインドする
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: df0b3d27eec478280a33be831a2431eccdf05a74
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483377"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Secure LDAP (LDAPS) を使用して Azure AD Domain Services マネージド ドメインにバインドする

## <a name="before-you-begin"></a>開始する前に
「[タスク 4 - インターネットからマネージド ドメインにアクセスできるように DNS を構成する](active-directory-ds-ldaps-configure-dns.md)」を完了します。


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>タスク 5: LDP.exe を使用して LDAP 経由でマネージド ドメインにバインドする
リモート サーバー管理ツール パッケージに含まれている LDP.exe ツールを使用すると、LDAP 経由でバインドと検索を行うことができます。

最初に LDP を開き、マネージド ドメインに接続します。 **[接続]** をクリックし、メニュー内にある **[Connect...]\(接続...\)** をクリックします。 マネージド ドメインの DNS ドメイン名を指定します。 接続に使用するポートを指定します。 LDAP 接続の場合は、ポート 389 を使用します。 LDAPS 接続の場合は、ポート 636 を使用します。 **[OK]** ボタンをクリックし、マネージド ドメインに接続します。

次に、マネージド ドメインにバインドします。 **[接続]** をクリックし、メニュー内にある **[Bind...]\(バインド...\)** をクリックします。 'AAD DC Administrators' グループに属しているユーザー アカウントの資格情報を指定します。

> [!IMPORTANT]
> Azure AD Domain Services インスタンスで NTLM パスワードハッシュ同期を無効にしている場合、ユーザー (およびサービス アカウント) は LDAP 簡易バインドを実行できません。  NTLM パスワード ハッシュ同期の無効化の詳細については、「[Azure AD Domain Services のマネージド ドメインをセキュリティで保護する](secure-your-domain.md)」をお読みください。
>
>

**[表示]** を選択し、メニュー内にある **[ツリー]** を選択します。 [ベース DN] フィールドは空白のままにして、[OK] をクリックします。 検索するコンテナーに移動したら、コンテナーを右クリックし、[検索] を選択します。

> [!TIP]
> - Azure AD から同期されたユーザーとグループは、**AADDC Users** 組織単位に保存されます。 この組織単位の検索パスは ```OU=AADDC Users,DC=CONTOSO100,DC=COM``` のようになります。
> - マネージド ドメインに参加するコンピューターのコンピューター アカウントは、**AADDC Computers** 組織単位に保存されます。 この組織単位の検索パスは ```OU=AADDC Computers,DC=CONTOSO100,DC=COM``` のようになります。
>
>

詳細については、「[LDAP query basics](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)」 (LDAP クエリの基本) を参照してください。


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>タスク 6: インターネット経由でのマネージド ドメインへの LDAPS アクセスをロック ダウンする
> [!NOTE]
> インターネット経由でのマネージド ドメインへの LDAPS アクセスを有効にしていない場合は、この構成タスクをスキップしてください。
>
>

このタスクを開始する前に、[タスク 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md) で説明されている手順を完了します。

LDAPS がインターネット経由でマネージド ドメインにアクセスできるようにすると、セキュリティ上の脅威となります。 セキュリティで保護されている LDAP に使用されるポート (ポート 636) でインターネットからマネージド ドメインに到達できます。 マネージド ドメインへのアクセスを特定の既知の IP アドレスに制限できます。 ネットワーク セキュリティ グループ (NSG) を作成し、それと Azure AD Domain Services を有効にしたサブネットを関連付けます。

次の表のサンプル NSG は、インターネット経由での Secure LDAP アクセスをロックします。 NSG のルールは、指定した IP アドレスのセットから TCP ポート 636 経由で入ってくる Secure LDAP アクセスのみを許可します。 既定の 'DenyAll' ルールは、インターネットから入ってくるその他すべてのトラフィックに適用されます。 指定した IP アドレスからインターネット経由で入ってくる LDAPS アクセスを許可する NSG ルールには、DenyAll NSG ルールより高い優先度が設定されています。

![サンプル NSG。セキュリティで保護された LDAPS を利用し、インターネット経由でアクセスします。](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**詳細** - [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)


## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](create-instance.md)
* [Azure AD Domain Services ドメインを管理する](manage-domain.md)
* [LDAP query basics](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter) (LDAP クエリの基本)
* [Azure AD Domain Services のグループ ポリシーを管理する](manage-group-policy.md)
* [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)
* [ネットワーク セキュリティ グループの作成](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>次のステップ
[マネージド ドメインでの Secure LDAP をトラブルシューティングする](tshoot-ldaps.md)
