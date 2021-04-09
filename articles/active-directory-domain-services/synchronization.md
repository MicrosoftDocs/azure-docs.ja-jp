---
title: Azure AD Domain Services の同期のしくみ | Microsoft Docs
description: Azure AD テナントまたはオンプレミス Active Directory Domain Services 環境から Azure Active Directory Domain Services のマネージド ドメインへのオブジェクトと資格情報の同期プロセスのしくみについて説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 41ba337765b4a0a93be52f08ae6656707cf7aa73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618809"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services のマネージド ドメイン内でのオブジェクトと資格情報の同期のしくみ

Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメイン内のオブジェクトと資格情報は、ドメイン内でローカルに作成するか、Azure Active Directory (Azure AD) テナントから同期することができます。 Azure AD DS を初めてデプロイするときに、一方向の自動同期が構成され、Azure AD からオブジェクトがレプリケートされます。 この一方向の同期は引き続きバックグラウンドで実行され、Azure AD からの変更を反映して Azure AD DS マネージド ドメインを最新の状態に保ちます。 Azure AD DS から Azure AD への同期は行われません。

ハイブリッド環境では、Azure AD Connect を使用して、オンプレミスの AD DS ドメインのオブジェクトと資格情報を Azure AD に同期できます。 これらのオブジェクトが Azure AD に正常に同期されると、自動バックグラウンド同期によって、マネージド ドメインを使用するアプリケーションでそれらのオブジェクトと資格情報を使用できるようになります。

ADFS を使用したフェデレーション認証のために、オンプレミスの AD DS と Azure AD が構成されている場合、Azure DS で使用できる (現在の、または有効な) パスワード ハッシュはありません。 フェデレーション認証が実装される前に作成された Azure AD ユーザー アカウントには古いパスワード ハッシュがある可能性がありますが、これはオンプレミスのパスワードのハッシュと一致しないと考えられます。 そのため、Azure AD DS はユーザーの資格情報を検証できません。

次の図は、Azure AD DS、Azure AD、およびオプションのオンプレミスの AD DS 環境間で同期がどのように行われるかを示しています。

![Azure AD Domain Services のマネージド ドメインでの同期の概要](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Azure AD から Azure AD DS への同期

ユーザー アカウント、グループ メンバーシップ、および資格情報ハッシュは、Azure AD から Azure AD DS に一方向で同期されます。 この同期プロセスは自動的に行われます。 この同期プロセスを構成、監視、または管理する必要はありません。 Azure AD ディレクトリ内のオブジェクトの数に応じて、初期同期には数時間から数日かかることがあります。 初期同期が完了した後、Azure AD 内で加えられた変更 (パスワードや属性の変更など) は、Azure AD DS に自動的に同期されます。

Azure AD で作成されたユーザーは、Azure AD でパスワードを変更するまで Azure AD DS に同期されません。 このパスワード変更プロセスによって、Kerberos 認証と NTLM 認証に使用されるパスワード ハッシュが Azure AD に生成されて保存されます。 Azure AD DS でユーザーを正常に認証するには、パスワード ハッシュが必要です。

設計上、同期プロセスは一方向です。 Azure AD DS から Azure AD への変更の逆方向の同期は行われません。 マネージド ドメインは、作成できるカスタムの組織単位 (OU) を除き、主に読み取り専用です。 マネージド ドメイン内でユーザー属性、ユーザー パスワード、またはグループ メンバーシップを変更することはできません。

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>属性の同期と Azure AD DS へのマッピング

次の表では、一般的な属性の一部と、これらが Azure AD DS に同期される方法を示します。

| Azure AD DS 内の属性 | source | Notes |
|:--- |:--- |:--- |
| UPN | Azure AD テナント内のユーザーの *UPN* 属性 | Azure AD テナントの UPN 属性は、そのまま Azure AD DS に同期されます。 マネージド ドメインにサインインする最も確実な方法は、UPN を使用することです。 |
| SAMAccountName | Azure AD テナントのユーザーに設定、または自動生成された *mailNickname* 属性 | *SAMAccountName* 属性は、Azure AD テナントの *mailNickname* 属性を同期元とします。 複数のユーザー アカウントで *mailNickname* 属性が同じ場合は、*SAMAccountName* が自動生成されます。 ユーザーの *mailNickname* または *UPN* プレフィックスが 20 文字を超える場合は、*SAMAccountName* 属性の 20 文字以下の制限を満たすために *SAMAccountName* が自動生成されます。 |
| パスワード | Azure AD テナントのユーザー パスワード | NTLM または Kerberos 認証に必要な従来のパスワード ハッシュは、Azure AD テナントから同期されます。 Azure AD Connect を使用して Azure AD テナントがハイブリッド同期用に構成されている場合、これらのパスワード ハッシュはオンプレミスの AD DS 環境から供給されます。 |
| プライマリ ユーザーおよびグループの SID | 自動生成 | ユーザーおよびグループ アカウントのプライマリ SID は、Azure AD DS 内に自動生成されます。 この属性は、オンプレミスの AD DS 環境内のオブジェクトのプライマリ ユーザーおよびグループの SID とは一致しません。 この不一致の原因は、マネージド ドメインにオンプレミス AD DS ドメインとは異なる SID 名前空間があることです。 |
| ユーザーとグループの SID 履歴 | オンプレミスのプライマリ ユーザーおよびグループの SID | Azure AD DS 内のユーザーおよびグループの *SidHistory* 属性は、オンプレミスの AD DS 環境内の対応するプライマリ ユーザーまたはグループの SID と一致するように設定されています。 この機能により、リソースを再度 ACL 処理する必要がなくなるため、オンプレミスのアプリケーションを Azure AD DS にリフトアンドシフトすることが簡単になります。 |

> [!TIP]
> **UPN 形式を使用したマネージド ドメインへのサインイン** マネージド ドメイン内の一部ユーザー アカウントに対して `AADDSCONTOSO\driley` などの *SAMAccountName* 属性が自動生成される場合があります。 ユーザーの自動生成された *SAMAccountName* が、UPN プレフィックスとは異なる場合があるため、常に信頼できるサインイン方法ではありません。
>
> たとえば、複数のユーザーで *mailNickname* 属性が同じだったり、ユーザーの UPN プレフィックスが最大文字数を超えている場合は、これらのユーザーの *SAMAccountName* が自動生成されることがあります。 マネージド ドメインに確実にサインインするには、`driley@aaddscontoso.com` などの UPN 形式を使用します。

### <a name="attribute-mapping-for-user-accounts"></a>ユーザー アカウントの属性のマッピング

次の表は、Azure AD 内のユーザー オブジェクトの特定の属性が、Azure AD DS 内の対応する属性にどのように同期されるかを示しています。

| Azure AD 内のユーザー属性 | Azure AD DS 内のユーザー属性 |
|:--- |:--- |
| accountEnabled |userAccountControl (ACCOUNT_DISABLED ビットの設定または解除) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| employeedId |employeeId |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (自動生成される場合があります) |
| manager |manager |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (DONT_EXPIRE_PASSWORD ビットの設定または解除) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>グループの属性のマッピング

次の表は、Azure AD 内のグループ オブジェクトの特定の属性が、Azure AD DS 内の対応する属性にどのように同期されるかを示しています。

| Azure AD 内のグループ属性 | Azure AD DS 内のグループ属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (自動生成される場合があります) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>オンプレミスの AD DS から Azure AD と Azure AD DS への同期

Azure AD Connect は、オンプレミスの AD DS 環境から Azure AD へのユーザー アカウント、グループ メンバーシップ、および資格情報ハッシュの同期に使用されます。 UPN やオンプレミスのセキュリティ識別子 (SID) などのユーザー アカウントの属性が同期されます。 Azure AD DS を使用してサインインするために、NTLM および Kerberos 認証に必要な従来のパスワード ハッシュも、Azure AD に同期されます。

> [!IMPORTANT]
> Azure AD Connect は、オンプレミスの AD DS 環境との同期のためにのみインストールおよび構成する必要があります。 マネージド ドメインに Azure AD Connect をインストールして元の Azure AD にオブジェクトを同期することはサポートされていません。

ライトバックを構成すると、Azure AD からの変更がオンプレミスの AD DS 環境に同期されます。 たとえば、ユーザーが Azure AD のセルフサービス パスワード管理を使用してパスワードを変更すると、パスワードがオンプレミスの AD DS 環境内で更新されます。

> [!NOTE]
> 既知のバグ/問題がすべて修正されているよう、常に最新バージョンの Azure AD Connect を使用してください。

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>複数フォレストのオンプレミス環境からの同期

多くの組織には、複数のフォレストを含む、かなり複雑なオンプレミスの AD DS 環境があります。 Azure AD Connect では、複数フォレストの環境から Azure AD へのユーザー、グループ、および資格情報ハッシュの同期がサポートされます。

Azure AD には、はるかに単純なフラット型名前空間があります。 Azure AD によって保護されたアプリケーションにユーザーが確実にアクセスできるようにするには、異なるフォレストのユーザー アカウント間における UPN の競合を解決する必要があります。 マネージド ドメインでは、Azure AD と同様に、フラットな OU 構造が使用されます。 階層的な OU 構造をオンプレミスで構成した場合でも、ユーザー アカウントとグループは、異なるオンプレミス ドメインまたはフォレストから同期されても、すべて *AADDC Users* コンテナー内に格納されます。 マネージド ドメインでは、階層的な OU 構造がフラット化されます。

前に説明したように、Azure AD DS から Azure AD への同期は行われません。 Azure AD DS に[カスタム組織単位 (OU) を作成](create-ou.md)し、そのカスタム OU 内にユーザー、グループ、またはサービス アカウントを作成することができます。 カスタム OU 内に作成されたオブジェクトは、いずれも Azure AD に同期されません。 これらのオブジェクトはマネージド ドメイン内でのみ使用可能で、Azure AD PowerShell コマンドレット、Microsoft Graph API、または Azure AD 管理 UI を使用して表示することはできません。

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Azure AD DS に同期されないもの

次のオブジェクトまたは属性は、オンプレミスの AD DS 環境から Azure AD または Azure AD DS には同期されません。

* **除外対象の属性:** Azure AD Connect を使用して、オンプレミスの AD DS 環境から Azure AD への同期から、特定の属性を除外することを選択できます。 これらの除外された属性は、Azure AD DS では使用できません。
* **グループ ポリシー:** オンプレミスの AD DS 環境内で構成されているグループ ポリシーは、Azure AD DS に同期されません。
* **sysvol フォルダー:** オンプレミスの AD DS 環境内の *Sysvol* フォルダーの内容は、Azure AD DS に同期されません。
* **コンピューター オブジェクト:** オンプレミスの AD DS 環境に参加しているコンピューターのコンピューター オブジェクトは、Azure AD DS に同期されません。 これらのコンピューターは、マネージド ドメインとの信頼関係がなく、オンプレミスの AD DS 環境にのみ属しています。 Azure AD DS では、マネージド ドメインに明示的にドメイン参加させたコンピューターのコンピューター オブジェクトのみが表示されます。
* **ユーザーとグループの SidHistory 属性:** オンプレミスの AD DS 環境からのプライマリ ユーザーおよびプライマリ グループの SID は、Azure AD DS に同期されます。 ただし、ユーザーとグループの既存の *SidHistory* 属性は、オンプレミスの AD DS 環境から Azure AD DS に同期されません。
* **組織単位 (OU) の構造:** オンプレミスの AD DS 環境内で定義されている組織単位は、Azure AD DS と同期しません。 Azure AD DS には、ユーザー用に 1 つとコンピューター用に 1 つ、合計 2 つの組み込み OU があります。 マネージド ドメインにはフラットな OU 構造があります。 [マネージド ドメイン内にカスタムの OU を作成](create-ou.md)することを選択できます。

## <a name="password-hash-synchronization-and-security-considerations"></a>パスワード ハッシュの同期とセキュリティに関する考慮事項

Azure AD DS を有効にすると、NTLM + Kerberos 認証用の従来のパスワード ハッシュが必要になります。 Azure AD にはクリア テキストのパスワードが格納されないため、既存のユーザー アカウントに対してこれらのハッシュを自動的に生成することはできません。 NTLM および Kerberos 互換のパスワード ハッシュは、生成されて格納された後、常に暗号化されて Azure AD に保存されます。

暗号化キーは、Azure AD テナントごとに一意です。 これらのハッシュは、Azure AD DS だけが復号化キーにアクセスできるように暗号化されます。 Azure AD 内の他のサービスやコンポーネントは、復号化キーにアクセスすることはできません。

その後、従来のパスワード ハッシュは、Azure AD からマネージド ドメインのドメイン コントローラーに同期されます。 Azure AD DS 内のこれらのマネージド ドメイン コントローラーのディスクは、保存時に暗号化されます。 オンプレミスの AD DS 環境にパスワードが保存され、セキュリティで保護される場合と同様に、これらのパスワード ハッシュは、これらのドメイン コントローラーに保存され、セキュリティで保護されます。

クラウドのみの Azure AD 環境では、必要なパスワード ハッシュを生成して Azure AD に保存するために、[ユーザーにパスワードをリセットまたは変更](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)するよう求めています。 Azure AD Domain Services を有効にした後に Azure AD で作成されたクラウド ユーザー アカウントの場合、パスワード ハッシュが生成され、NTLM および Kerberos 互換の形式で保存されます。 すべてのクラウド ユーザー アカウントは、Azure AD DS に同期される前にパスワードを変更する必要があります。

Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたハイブリッド ユーザー アカウントの場合、[NTLM および Kerberos 互換の形式でパスワード ハッシュを同期するように Azure AD Connect を構成](tutorial-configure-password-hash-sync.md)する必要があります。

## <a name="next-steps"></a>次のステップ

パスワード同期の仕様について詳しくは、[Azure AD Connect を使用したパスワード ハッシュ同期の方法](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)に関する記事をご覧ください。

Azure AD DS の使用を開始するには、[マネージド ドメインを作成](tutorial-create-instance.md)します。
