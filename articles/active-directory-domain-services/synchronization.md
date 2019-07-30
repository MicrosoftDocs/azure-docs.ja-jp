---
title: Azure Active Directory Domain Services:マネージド ドメインでの同期 | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインでの同期について
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 475817985885cdd6023e72f20ecf35a3ca582924
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472436"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインでの同期
次の図は、Azure AD Domain Services のマネージド ドメインにおける同期のしくみを示しています。

![Azure AD Domain Services での同期](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>オンプレミスのディレクトリから Azure AD テナントへの同期
Azure AD Connect Sync は、ユーザー アカウント、グループ メンバーシップ、および資格情報ハッシュの Azure AD テナントへの同期に使用されます。 UPN やオンプレミスの SID (セキュリティ識別子) などのユーザー アカウントの属性が同期されます。 Azure AD Domain Services を使用すると、NTLM および Kerberos 認証に必要な従来の資格情報ハッシュも、Azure AD テナントに同期されます。

書き戻しを構成すると、Azure AD ディレクトリで行われた変更が、オンプレミスの Active Directory に同期されます。 たとえば、Azure AD のセルフサービス パスワード管理を使用してパスワードを変更すると、変更されたパスワードがオンプレミスの AD ドメインで更新されます。

> [!NOTE]
> 既知のバグ/問題がすべて修正されているよう、常に最新バージョンの Azure AD Connect を使用してください。
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Azure AD テナントからマネージド ドメインへの同期
ユーザー アカウント、グループ メンバーシップ、および資格情報ハッシュは、Azure AD テナントから Azure AD Domain Services のマネージド ドメインに同期されます。 この同期プロセスは自動的に行われます。 この同期プロセスを構成、監視、または管理する必要はありません。 Azure AD ディレクトリ内のオブジェクトの数に応じて、初期同期には数時間から数日かかることがあります。 初期同期の完了後、Azure AD で行われた変更がマネージド ドメインで更新されるまでに約 20 から 30 分かかります。 この同期間隔は、Azure AD で実行されたパスワードの変更や属性の変更に適用されます。

また同期プロセスは、一方向性のプロセスです。 マネージド ドメインは、カスタムの組織単位 (OU) を作成した場合を除き、主に読み取り専用です。 そのため、マネージド ドメイン内でユーザー属性、ユーザー パスワード、またはグループ メンバーシップを変更することはできません。 したがって、マネージド ドメインから Azure AD テナントに、逆方向に変更内容が同期されることはありません。

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>複数フォレストのオンプレミス環境からの同期
多くの組織が、複数のアカウント フォレストで構成された複雑なオンプレミスの ID インフラストラクチャを持っています。 Azure AD Connect では、複数フォレストの環境から Azure AD テナントへのユーザー、グループ、および資格情報ハッシュの同期をサポートしています。

これに対し、Azure AD テナントは非常にシンプルでフラットな名前空間です。 Azure AD によって保護されたアプリケーションにユーザーが確実にアクセスできるようにするには、異なるフォレストのユーザー アカウント間における UPN の競合を解決する必要があります。 Azure AD Domain Services のマネージド ドメインは Azure AD テナントとよく似ています。 マネージド ドメイン内の OU はフラットな構造になっています。 ユーザー アカウントとグループは、異なるオンプレミス ドメインまたはフォレストから同期されても、すべて "AADDC Users" コンテナー内に格納されます。 オンプレミスで階層状の OU 構造を構成している場合があります。 その場合も、マネージド ドメインでは単純でフラットな OU 構造が維持されます。

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>除外項目 - マネージド ドメインに同期されないもの
次のオブジェクトや属性は、Azure AD テナントまたはマネージド ドメインに同期されません。

* **除外対象の属性:** Azure AD Connect を使用して、オンプレミス ドメインから Azure AD テナントへの同期から、特定の属性を除外することができます。 これらの除外対象の属性は、マネージド ドメインでは使用できません。
* **グループ ポリシー:** オンプレミス ドメインで構成されたグループ ポリシーは、マネージド ドメインに同期されません。
* **Sysvol 共有:** 同様に、オンプレミス ドメインの SYSVOL 共有の内容も、マネージド ドメインに同期されません。
* **コンピューター オブジェクト:** オンプレミス ドメインに参加したコンピューターのコンピューター オブジェクトは、マネージド ドメインに同期されません。 これらのコンピューターはマネージド ドメインと信頼関係がなく、オンプレミス ドメインにのみ属します。 マネージド ドメイン内にあるコンピューター オブジェクトは、マネージド ドメインに明示的にドメイン参加させたコンピューターのものだけです。
* **ユーザーとグループの SidHistory 属性:** オンプレミス ドメインのプライマリ ユーザーおよびプライマリ グループの SID は、マネージド ドメインに同期されます。 ただし、ユーザーとグループの既存の SidHistory 属性については、オンプレミス ドメインからマネージド ドメインに同期されません。
* **組織単位 (OU) の構造:** オンプレミス ドメインで定義された組織単位は、マネージド ドメインに同期されません。 マネージド ドメインには、2 つの組み込み OU があります。 既定では、マネージド ドメインはフラットな OU 構造をしています。 ただし、[マネージド ドメイン内にカスタムの OU を作成](create-ou.md)することができます。

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>特定の属性がマネージド ドメインに同期される方法
次の表では、一般的な属性の一部を示し、これらがマネージド ドメインに同期される方法を説明します。

| マネージド ドメイン内の属性 | source | メモ |
|:--- |:--- |:--- |
| UPN |Azure AD テナントのユーザーの UPN 属性 |Azure AD テナントの UPN 属性は、そのままマネージド ドメインに同期されます。 そのため、UPN を使用することが、マネージド ドメインにサインインする最も確実な方法になります。 |
| SAMAccountName |Azure AD テナントのユーザーに設定、または自動生成された mailNickname 属性 |SAMAccountName 属性は、Azure AD テナントの mailNickname 属性を同期元とします。 複数のユーザー アカウントで mailNickname 属性が同じ場合は、SAMAccountName が自動生成されます。 ユーザーの mailNickname または UPN プレフィックスが 20 文字を超える場合は、SAMAccountName 属性の 20 文字以下の制限を満たすために SAMAccountName が自動生成されます。 |
| パスワード |Azure AD テナントのユーザー パスワード |NTLM または Kerberos 認証に必要な資格情報ハッシュ (補足の資格情報とも呼ばれます) は、Azure AD テナントから同期されます。 Azure AD テナントが同期されたテナントの場合は、これらの資格情報の同期元はオンプレミス ドメインになります。 |
| プライマリ ユーザーおよびグループの SID |自動生成 |ユーザーおよびグループ アカウントのプライマリ SID は、マネージド ドメインで自動生成されます。 この属性は、オンプレミス AD ドメインのオブジェクトのプライマリ ユーザーおよびグループの SID とは一致しません。 この不一致の原因は、マネージド ドメインがオンプレミス ドメインとは異なる SID の名前空間を持つためです。 |
| ユーザーとグループの SID 履歴 |オンプレミスのプライマリ ユーザーおよびグループの SID |マネージド ドメインのユーザーおよびグループの SidHistory 属性は、オンプレミス ドメインの対応するプライマリ ユーザーまたはグループの SID と一致するように設定されています。 この機能により、リソースを再度 ACL 処理する必要がなくなるため、オンプレミスのアプリケーションをリフト アンド シフト方式でマネージド ドメインに簡単に移行することができます。 |

> [!NOTE]
> **UPN 形式を使用したマネージド ドメインへのサインイン:** マネージド ドメインの一部ユーザー アカウントの SAMAccountName 属性が自動生成される場合があります。 複数のユーザーで mailNickname 属性が同じだったり、ユーザーの UPN プレフィックスが最大文字数を超えている場合は、これらのユーザーのSAMAccountName が自動生成されることがあります。 そのため SAMAccountName 形式 (例: "CONTOSO100\joeuser") は、ドメインにサインインするうえで常に確実な方法というわけではありません。 ユーザーの自動生成された SAMAccountName が、UPN プレフィックスとは異なる場合があります。 マネージド ドメインに確実にサインインするには、UPN 形式 (例: 「joeuser@contoso100.com」) を使用します。

### <a name="attribute-mapping-for-user-accounts"></a>ユーザー アカウントの属性のマッピング
次の表は、Azure AD テナントのユーザー オブジェクトの特定の属性が、マネージド ドメインの対応する属性にどのように同期されるかを示しています。

| Azure AD テナントのユーザー属性 | マネージド ドメインのユーザー属性 |
|:--- |:--- |
| accountEnabled |userAccountControl (ACCOUNT_DISABLED ビットの設定または解除) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (自動生成される場合があります) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (DONT_EXPIRE_PASSWORD ビットの設定または解除) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>グループの属性のマッピング
次の表は、Azure AD テナントのグループ オブジェクトの特定の属性が、マネージド ドメインの対応する属性にどのように同期されるかを示しています。

| Azure AD テナントのグループ属性 | マネージド ドメインのグループ属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (自動生成される場合があります) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>パスワード ハッシュの同期とセキュリティに関する考慮事項
Azure AD Domain Services を有効にすると、Azure AD ディレクトリによってパスワード ハッシュが生成され、NTLM および Kerberos 互換の形式で保存されます。 

既存のクラウド ユーザー アカウントの場合、Azure AD ではクリア テキストのパスワードは保存されないので、これらのハッシュを自動的に生成することはできません。 そのため、Microsoft では、パスワード ハッシュを生成して Azure AD に保存するために、[クラウド ユーザーにパスワードをリセットまたは変更](active-directory-ds-getting-started-password-sync.md)するよう求めています。 Azure AD Domain Services を有効にした後に Azure AD で作成されたクラウド ユーザー アカウントの場合、パスワード ハッシュが生成され、NTLM および Kerberos 互換の形式で保存されます。 

Azure AD Connect 同期を使用してオンプレミスの AD から同期されたユーザー アカウントの場合、[NTLM および Kerberos 互換の形式でパスワード ハッシュを同期するように Azure AD Connect を構成](active-directory-ds-getting-started-password-sync-synced-tenant.md)する必要があります。

NTLM および Kerberos 互換のパスワード ハッシュは、常に暗号化されて Azure AD に保存されます。 これらのハッシュは、Azure AD Domain Services だけが復号化キーにアクセスできるように暗号化されます。 Azure AD 内の他のサービスやコンポーネントは、復号化キーにアクセスすることはできません。 暗号化キーは、Azure AD テナントごとに一意です。 Azure AD Domain Services は、マネージド ドメインのドメイン コントローラーにパスワード ハッシュを同期します。 Windows Server AD ドメイン コントローラーにパスワードが保存され、セキュリティで保護される場合と同様に、これらのパスワード ハッシュは、これらのドメイン コントローラーに保存され、セキュリティで保護されます。 これらのマネージド ドメイン コントローラーのディスクは、保存時に暗号化されます。

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>マネージド ドメインから Azure AD テナントに同期されないオブジェクト
この記事の前のセクションで説明したとおり、マネージド ドメインから Azure AD テナントへの同期はありません。 ただし、マネージド ドメイン内に[カスタムの組織単位 (OU) を作成](create-ou.md)することができます。 これらのカスタム OU 内には、ほかの OU、ユーザー、グループ、またはサービス アカウントを作成することもできます。 カスタム OU 内に作成されたオブジェクトは、いずれも Azure AD テナントに同期されません。 これらのオブジェクトはマネージド ドメイン内でのみ使用することができます。 そのため、Azure AD PowerShell コマンドレット、Azure AD Graph API、または Azure AD の管理 UI を使用しても、これらのオブジェクトは表示されません。

## <a name="related-content"></a>関連コンテンツ
* [機能 - Azure AD Domain Services](active-directory-ds-features.md)
* [デプロイ シナリオ - Azure AD Domain Services](scenarios.md)
* [Azure AD Domain Services のネットワークに関する考慮事項](network-considerations.md)
* [Azure AD ドメイン サービスの使用開始](create-instance.md)
