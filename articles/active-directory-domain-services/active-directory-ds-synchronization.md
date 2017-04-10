---
title: "Azure Active Directory ドメイン サービス: 管理対象ドメインでの同期 | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの管理対象ドメインでの同期について"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 0c9a9a56e1489ee91fcc332beeef36cdc9c93dc1
ms.lasthandoff: 03/07/2017


---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD ドメイン サービスの管理対象ドメインでの同期
次の図は、Azure AD ドメイン サービスの管理対象ドメインにおける同期のしくみを示しています。

![Azure AD ドメイン サービスの同期トポロジ](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>オンプレミスのディレクトリから Azure AD テナントへの同期
Azure AD Connect Sync は、ユーザー アカウント、グループ メンバーシップ、および資格情報ハッシュの Azure AD テナントへの同期に使用されます。 UPN やオンプレミスの SID (セキュリティ識別子) などのユーザー アカウントの属性が同期されます。 Azure AD Domain Services を使用すると、NTLM および Kerberos 認証に必要な従来の資格情報ハッシュも、Azure AD テナントに同期されます。

書き戻しを構成すると、Azure AD ディレクトリで行われた変更が、オンプレミスの Active Directory に同期されます。 たとえば、Azure AD のセルフサービス パスワード変更機能を使用してパスワードを変更すると、変更されたパスワードがオンプレミスの AD ドメインに更新されます。

> [!NOTE]
> 既知のバグ/問題がすべて修正されているよう、常に最新バージョンの Azure AD Connect を使用してください。
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Azure AD テナントから管理対象ドメインへの同期
ユーザー アカウント、グループ メンバーシップ、および資格情報ハッシュは、Azure AD テナントから Azure AD Domain Services の管理対象ドメインに同期されます。 この同期プロセスは自動的に行われます。 この同期プロセスを構成、監視、または管理する必要はありません。 1 回のみ実行されるディレクトリの初期同期が完了した後は、Azure AD で行われた変更が管理対象ドメインに反映されるまで、通常は約 20 分かかります。 この同期間隔は、Azure AD で実行されたパスワードの変更や属性の変更に適用されます。

また同期プロセスは、一方向性のプロセスです。 管理対象ドメインは、カスタムの組織単位 (OU) を作成した場合を除き、主に読み取り専用です。 そのため、管理対象ドメイン内でユーザー属性、ユーザー パスワード、またはグループ メンバーシップを変更することはできません。 したがって、管理対象ドメインから Azure AD テナントに、逆方向に変更内容が同期されることはありません。

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>複数フォレストのオンプレミス環境からの同期
多くの組織が、複数のアカウント フォレストで構成された複雑なオンプレミスの ID インフラストラクチャを持っています。 Azure AD Connect では、複数フォレストの環境から Azure AD テナントへのユーザー、グループ、および資格情報ハッシュの同期をサポートしています。

これに対し、Azure AD テナントは非常にシンプルでフラットな名前空間です。 Azure AD によって保護されたアプリケーションにユーザーが確実にアクセスできるようにするには、異なるフォレストのユーザー アカウント間における UPN の競合を解決する必要があります。 Azure AD Domain Services の管理対象ドメインは Azure AD テナントとよく似ています。 したがって、管理対象ドメイン内の OU はフラットな構造になっています。 ユーザーとグループは、オンプレミス ドメインとフォレストのいずれで同期されたかに関係なく、すべて "AADDC Users" コンテナー内に格納されます。 オンプレミスで階層状の OU 構造を構成している場合があります。 それでも、管理対象ドメインは、単純でフラットな OU 構造のままです。

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>除外項目 - 管理対象ドメインに同期されないもの
次のオブジェクトや属性は、Azure AD テナントまたは管理対象ドメインに同期されません。

* **除外対象の属性:** Azure AD Connect を使用して、オンプレミス ドメインから Azure AD テナントへの同期から、特定の属性を除外することができます。 これらの除外対象の属性は、管理対象ドメインでは使用できません。
* **グループ ポリシー:** オンプレミス ドメインで構成されたグループ ポリシーは、管理対象ドメインに同期されません。
* **SYSVOL 共有:** 同様に、オンプレミス ドメインの SYSVOL 共有の内容も、管理対象ドメインに同期されません。
* **コンピューター オブジェクト:** オンプレミス ドメインに参加したコンピューターのコンピューター オブジェクトは、管理対象ドメインに同期されません。 これらのコンピューターは管理対象ドメインと信頼関係がなく、オンプレミス ドメインにのみ属します。 管理対象ドメイン内にあるコンピューター オブジェクトは、管理対象ドメインに明示的にドメイン参加させたコンピューターのものだけです。
* **ユーザーとグループの SidHistory 属性:** オンプレミス ドメインのプライマリ ユーザーおよびプライマリ グループの SID は、管理対象ドメインに同期されます。 ただし、ユーザーとグループの既存の SidHistory 属性については、オンプレミス ドメインから管理対象ドメインに同期されません。
* **組織単位 (OU) の構造:** オンプレミス ドメインで定義された組織単位は、管理対象ドメインに同期されません。 管理対象ドメインには、2 つの組み込み OU があります。 既定では、管理対象ドメインはフラットな OU 構造をしています。 ただし、[管理対象ドメイン内にカスタム OU を作成](active-directory-ds-admin-guide-create-ou.md)することができます。

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>特定の属性が管理対象ドメインに同期される方法
次の表では、一般的な属性の一部を示し、これらが管理対象ドメインに同期される方法を説明します。

| 管理対象ドメイン内の属性 | から | メモ |
|:--- |:--- |:--- |
| UPN |Azure AD テナントのユーザーの UPN 属性 |Azure AD テナントの UPN 属性は、そのまま管理対象ドメインに同期されます。 そのため、UPN を使用することが、管理対象ドメインにサインインする最も確実な方法になります。 |
| SAMAccountName |Azure AD テナントのユーザーに設定、または自動生成された mailNickname 属性 |SAMAccountName 属性は、Azure AD テナントの mailNickname 属性を同期元とします。 複数のユーザー アカウントで mailNickname 属性が同じ場合は、SAMAccountName が自動生成されます。 ユーザーの mailNickname または UPN プレフィックスが 20 文字を超える場合は、SAMAccountName 属性の 20 文字以下の制限を満たすために SAMAccountName が自動生成されます。 |
| パスワード |Azure AD テナントのユーザー パスワード |NTLM または Kerberos 認証に必要な資格情報ハッシュ (補足の資格情報とも呼ばれます) は、Azure AD テナントから同期されます。 Azure AD テナントが同期されたテナントの場合は、これらの資格情報の同期元はオンプレミス ドメインになります。 |
| プライマリ ユーザーおよびグループの SID |自動生成 |ユーザーおよびグループ アカウントのプライマリ SID は、管理対象ドメインで自動生成されます。 この属性は、オンプレミス AD ドメインのオブジェクトのプライマリ ユーザーおよびグループの SID とは一致しません。 この不一致の原因は、管理対象ドメインがオンプレミス ドメインとは異なる SID の名前空間を持つためです。 |
| ユーザーとグループの SID 履歴 |オンプレミスのプライマリ ユーザーおよびグループの SID |管理対象ドメインのユーザーおよびグループの SidHistory 属性は、オンプレミス ドメインの対応するプライマリ ユーザーまたはグループの SID と一致するように設定されています。 この機能により、リソースを再度 ACL 処理する必要がなくなるため、オンプレミスのアプリケーションをリフト アンド シフト方式で管理対象ドメインに簡単に移行することができます。 |

> [!NOTE]
> **UPN 形式を使用した管理対象ドメインへのサインイン:** 管理対象ドメインの一部ユーザー アカウントの SAMAccountName 属性が自動生成される場合があります。 複数のユーザーで mailNickname 属性が同じだったり、ユーザーの UPN プレフィックスが最大文字数を超えている場合は、これらのユーザーのSAMAccountName が自動生成されることがあります。 そのため SAMAccountName 形式 (例: "CONTOSO100\joeuser") は、ドメインにサインインするうえで常に確実な方法というわけではありません。 ユーザーの自動生成された SAMAccountName が、UPN プレフィックスとは異なる場合があります。 管理対象ドメインに確実にサインインするには、UPN 形式 (例: 「joeuser@contoso100.com」) を使用します。
>
>

### <a name="attribute-mapping-for-user-accounts"></a>ユーザー アカウントの属性のマッピング
次の表は、Azure AD テナントのユーザー オブジェクトの特定の属性が、管理対象ドメインの対応する属性にどのように同期されるかを示しています。

| Azure AD テナントのユーザー属性 | 管理対象ドメインのユーザー属性 |
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
次の表は、Azure AD テナントのグループ オブジェクトの特定の属性が、管理対象ドメインの対応する属性にどのように同期されるかを示しています。

| Azure AD テナントのグループ属性 | 管理対象ドメインのグループ属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (自動生成される場合があります) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>管理対象ドメインから Azure AD テナントに同期されないオブジェクト
この記事の前のセクションで説明したとおり、管理対象ドメインから Azure AD テナントへの同期はありません。 ただし、管理対象ドメイン内に[カスタムの組織単位 (OU) を作成](active-directory-ds-admin-guide-create-ou.md)することができます。 これらのカスタム OU 内には、ほかの OU、ユーザー、グループ、またはサービス アカウントを作成することもできます。 カスタム OU 内に作成されたオブジェクトは、いずれも Azure AD テナントに同期されません。 これらのオブジェクトは管理対象ドメイン内でのみ使用することができます。 そのため、Azure AD PowerShell コマンドレット、Azure AD Graph API、または Azure AD の管理 UI を使用しても、これらのオブジェクトは表示されません。

## <a name="related-content"></a>関連コンテンツ
* [機能 - Azure AD Domain Services](active-directory-ds-features.md)
* [デプロイ シナリオ - Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Azure AD Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)
* [Azure AD ドメイン サービスの使用開始](active-directory-ds-getting-started.md)

