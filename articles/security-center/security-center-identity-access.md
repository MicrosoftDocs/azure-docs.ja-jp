---
title: MFA に関する Azure Security Center のセキュリティの推奨事項
description: Azure Security Center を使用して Azure サブスクリプションに多要素認証を強制する方法について説明します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631899"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>サブスクリプションでの 多要素認証 (MFA) の実施を管理する

ユーザーの認証にパスワードのみを使用している場合は、攻撃ベクトルの可能性が残っています。 ユーザーは多くの場合、脆弱なパスワードを使用したり、複数のサービスで再利用したりします。 [MFA](https://www.microsoft.com/security/business/identity/mfa) を有効にすると、アカウントのセキュリティが強化され、しかもユーザーはそれまでと同様、シングル サインオン (SSO) でほとんどすべてのアプリケーションに対して認証を行うことができます。

組織で所有しているライセンスに基づいて Azure Active Directory (AD) ユーザーに対して MFA を有効にする方法は複数あります。 このページでは、Azure Security Center のコンテキストでそれぞれの詳細を説明します。


## <a name="mfa-and-security-center"></a>MFA と Security Center 

Security Center では MFA を重要視します。 セキュア スコアに最も貢献するセキュリティ コントロールは、**MFA を有効にする** ことです。 

[MFA の有効化] コントロールの推奨事項に従うことで、サブスクリプションのユーザーに推奨される運用方法を確実に満たすことができます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

MFA を有効にし、Security Center の 2 つの推奨事項に準拠するには、セキュリティの既定値、ユーザーごとの割り当て、条件付きアクセス (CA) ポリシーの 3 つの方法があります。 これらの各オプションについては、以下で説明します。

### <a name="free-option---security-defaults"></a>無料オプション - セキュリティの既定値
Azure AD の無償版を使用している場合は、[セキュリティの既定値](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)を使用して、テナントで多要素認証を有効にします。

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>Microsoft 365 Business、E3、または E5 のお客様の MFA
Microsoft 365 をご利用のお客様は、**ユーザーごとの割り当て** をご利用いただけます。 このシナリオでは、Azure AD MFA は、すべてのユーザーの、すべてのサインイン イベントに対して有効にするか無効にするかのどちらかとなります。 ユーザーのサブセットに対して、または特定のシナリオ下で多要素認証を有効にすることはできません。管理は、Office 365 ポータルを通じて行います。

### <a name="mfa-for-azure-ad-premium-customers"></a>Azure AD Premium のお客様向けの MFA
ユーザー エクスペリエンスを向上させるには、**条件付きアクセス (CA) ポリシー** オプションを利用するために Azure AD Premium P1 または P2 にアップグレードします。 CA ポリシーを構成するには、[Azure Active Directory (AD) テナントのアクセス許可](../active-directory/roles/permissions-reference.md)が必要です。

CA ポリシーでは以下が必要です。
- MFA の強制
- Microsoft Azure 管理アプリ ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) またはすべてのアプリを含める
- Microsoft Azure 管理アプリ ID を除外しない

**Azure AD Premium P1** のお客様は、Azure AD CA を使用して、ビジネス要件に合わせて特定のシナリオやイベントの際に多要素認証をユーザーに求めることができます。 この機能を含むその他のライセンスには、Enterprise Mobility + Security E3、Microsoft 365 F1、Microsoft 365 E3 があります。

**Azure AD Premium P2** では、最も強力なセキュリティ機能と向上したユーザー エクスペリエンスを提供します。 このライセンスでは、Azure AD Premium P1 の機能に[リスクベースの条件付きアクセス](../active-directory/conditional-access/howto-conditional-access-policy-risk.md)が追加されます。 リスクベースの CA は、ユーザーのパターンに適応し、多要素認証の回数を最小限に抑えます。 この機能を含むその他のライセンスには、Enterprise Mobility + Security E5 や Microsoft 365 E5 があります。

詳細については、[Azure の条件付きアクセスに関するドキュメント](../active-directory/conditional-access/overview.md)をご覧ください。

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>多要素認証 (MFA) が有効になっていないアカウントを識別する

MFA が有効になっていないユーザー アカウントの一覧は、[Security Center 推奨事項の詳細] ページから、または Azure Resource Graph を使用して表示できます。

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Azure portal で MFA が有効になっていないアカウントを表示する
[推奨事項の詳細] ページで、 **[異常なリソース]** 一覧からサブスクリプションを選択するか、 **[アクションの実行]** を選択すると、一覧が表示されます。

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Azure Resource Graph を使用して MFA が有効になっていないアカウントを表示する
MFA が有効になっていないアカウントを確認するには、次の Azure Resource Graph クエリを使用します。 このクエリによって、"サブスクリプションの所有者アクセス許可を持つアカウントで MFA を有効にする必要があります" という推奨事項があるすべての異常なリソース (アカウント) が返されます。 

1. **Azure Resource Graph エクスプローラー** を開きます。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

1. 次のクエリを入力し、 **[クエリの実行]** を選択します。

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData` プロパティによって、MFA が適用されていないアカウントのアカウント オブジェクト ID の一覧が表示されます。 

    > [!NOTE]
    > アカウントは、アカウント所有者のプライバシーを保護するために、アカウント名ではなくオブジェクト ID として表示されます。

> [!TIP]
> 別の方法として、Security Center の REST API メソッド [Assessments - Get](/rest/api/securitycenter/assessments/get) を使用できます。


## <a name="faq---mfa-in-security-center"></a>Security Center の MFA に関する FAQ

- [既に CA ポリシーを使用して MFA を強制しています。Azure Security Center の推奨事項がまだ表示されるのはなぜですか。](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [サードパーティの MFA ツールを使用して MFA を強制しています。Azure Security Center の推奨事項がまだ表示されるのはなぜですか。](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Security Center で、サブスクリプションに対するアクセス許可がないユーザー アカウントに "MFA が必要" と表示されるのはなぜですか?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [PIM を使用して MFA を強制しています。PIM アカウントが非準拠として表示されるのはなぜですか。](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [一部のアカウントを除外または破棄することはできますか?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Security Center の ID とアクセスの保護には制限がありますか?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>既に CA ポリシーを使用して MFA を強制しています。 Security Center の推奨事項が引き続き表示されるのはなぜですか?
推奨事項がまだ生成されている理由を調査するには、MFA CA ポリシーで次の構成オプションを確認します。

- アカウントは、MFA CA ポリシーの **[ユーザー]** セクション (または **[グループ]** セクションのいずれかのグループ) に含まれています
- Azure 管理アプリ ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) またはすべてのアプリが、MFA CA ポリシーの **[アプリ]** セクションに含まれています
- Azure 管理アプリ ID は、MFA CA ポリシーの **[アプリ]** セクションでは除外されません

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>サードパーティの MFA ツールを使用して MFA を強制しています。 Security Center の推奨事項が引き続き表示されるのはなぜですか?
Security Center の MFA の推奨事項では、サードパーティの MFA ツール (DUO など) はサポートされていません。

推奨事項が組織に無関係である場合は、「[セキュリティ スコアからのリソースと推奨事項の除外](exempt-resource.md)」で説明されているように、それらを「軽減済み」としてマークすることを検討してください。 [推奨事項を無効化](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)することもできます。

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Security Center で、サブスクリプションに対するアクセス許可がないユーザー アカウントに "MFA が必要" と表示されるのはなぜですか?
Security Center の MFA に関する推奨事項は、[Azure RBAC](../role-based-access-control/role-definitions-list.md) ロールと [Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md) ロールに関する情報を参照してください。 どのアカウントにもこれらのロールがないことを確認します。

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>PIM を使用して MFA を強制しています。 PIM アカウントが非準拠として表示されるのはなぜですか?
現在、Security Center の MFA に関する推奨事項では、PIM アカウントはサポートされていません。 これらのアカウントは、[ユーザー] または [グループ] セクションで CA ポリシーに追加できます。

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>一部のアカウントを除外または破棄することはできますか?
MFA が使用されていない一部のアカウントを除外する機能は、現在はサポートされていません。  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Security Center の ID とアクセスの保護には制限がありますか?
Security Center の ID とアクセスの保護にはいくつかの制限があります。

- 600 を超えるアカウントを持つサブスクリプションでは、ID に関する推奨事項は利用できません。 このような場合、これらの推奨事項は [利用できない評価] の下に表示されます。
- クラウド ソリューション プロバイダー (CSP) パートナーの管理エージェントでは、ID に関する推奨事項は利用できません。
- ID に関する推奨事項では、特権 ID 管理 (PIM) システムによって管理されているアカウントは識別されません。 PIM ツールを使用している場合、**アクセスとアクセス許可の管理** コントロールに不正確な結果が表示されることがあります。


## <a name="next-steps"></a>次の手順
その他の Azure リソースの種類に適用される推奨事項の詳細については、次の記事をご覧ください。

- [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)