---
title: アクティビティ レポートを使用して AD FS アプリを Azure Active Directory に移行する | Microsoft Docs
description: Active Directory フェデレーション サービス (AD FS) のアプリケーション アクティビティ レポートを使用すると、AD FS から Azure Active Directory (Azure AD) にアプリケーションをすばやく移行できます。 AD FS 用のこの移行ツールでは、Azure AD との互換性を識別して、移行に関するガイダンスを得られます。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3665c5b82095004ddf7dc1f503b54f5164d49c7f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260064"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>AD FS アプリケーション アクティビティ レポート (プレビュー) を使用してアプリケーションを Azure AD に移行する

多くの組織では、Active Directory フェデレーション サービス (AD FS) を使用して、クラウド アプリケーションへのシングルサインオンを提供しています。 認証のために AD FS アプリケーションを Azure AD に移行すると、コスト管理、リスク管理、生産性、コンプライアンス、ガバナンスの点で特に大きなメリットが得られます。 しかし、どのアプリケーションが Azure AD と互換性があるかを把握し、具体的な移行手順を特定するには、時間がかかる場合があります。

Azure portal の AD FS アプリケーション アクティビティ レポート (プレビュー) を使用すると、Azure AD に移行できるアプリケーションをすばやく特定できます。 Azure AD との互換性に関するすべての AD FS アプリケーションの評価、問題がないかどうかの確認、個々のアプリケーションの移行の準備に関するガイダンスの提供が行われます。 AD FS アプリケーション アクティビティ レポートでは、以下が可能です。

* **AD FS アプリケーションを検出し、移行のスコープを特定する。** AD FS アプリケーション アクティビティ レポートには、過去 30 日間にアクティブなユーザー ログインがあった、組織内のすべての AD FS アプリケーションが一覧表示されます。 レポートには、アプリケーションの Azure AD への移行の準備状況が示されます。 レポートには、Office 365 など、AD FS 内の Microsoft 関連の証明書利用者は表示されません。 たとえば、"urn:federation:MicrosoftOnline" という名前の証明書利用者などです。

* **移行するアプリケーションに優先度を付ける。** 過去 1 日、7 日、または 30 日間にアプリケーションにサインインした一意のユーザー数を取得して、そのアプリケーションを移行する重要度またはリスクを判断する材料にします。
* **移行テストを実行し、問題を修正する。** アプリケーションを移行する準備ができているかどうかを判断するために、レポート サービスによって自動的にテストが実行されます。 結果は移行状態として AD FS アプリケーション アクティビティ レポートに表示されます。 AD FS 構成に Azure AD 構成との互換性がない場合は、Azure AD で構成を解決する方法について具体的なガイダンスが示されます。

AD FS アプリケーション アクティビティ データは、グローバル管理者、レポート閲覧者、セキュリティ閲覧者、アプリケーション管理者、クラウド アプリケーション管理者のいずれかの管理者ロールが割り当てられているユーザーが使用できます。

## <a name="prerequisites"></a>前提条件

* アプリケーションにアクセスするには、自分の組織で現在 AD FS が使用されている必要があります。
* 自分の Azure AD テナントで Azure AD Connect Health が有効になっている必要があります。
* Azure AD Connect Health for AD FS エージェントがインストールされている必要があります。
   * [Azure AD Connect Health の詳細を確認する](../hybrid/how-to-connect-health-adfs.md)
   * [Azure AD Connect Health のセットアップと AD FS エージェントのインストールを開始する](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Azure AD Connect Health をインストールした後、いくつかの理由により、想定しているアプリケーションの一部が表示されません。 AD FS アプリケーション アクティビティ レポートには、過去 30 日間にユーザー ログインがあった AD FS 証明書利用者のみが表示されます。 また、レポートには、Office 365 などの Microsoft 関連の証明書利用者は表示されません。

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>移行可能な AD FS アプリケーションを検出する 

AD FS アプリケーション アクティビティ レポートは、Azure portal の Azure AD の **[使用状況と分析情報]** レポートで取得できます。 AD FS アプリケーション アクティビティ レポートでは、各 AD FS アプリケーションを分析して、そのままの状態で移行できるかどうか、または追加のレビューが必要かどうかを判断します。 

1. AD FS アプリケーション アクティビティ データにアクセスできる管理者ロール (グローバル管理者、レポート閲覧者、セキュリティ閲覧者、アプリケーション管理者、またはクラウド アプリケーション管理者) を使用して [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。

3. **[アクティビティ]** で、 **[使用状況と分析情報 (プレビュー)]** を選択し、次に **[AD FS アプリケーション アクティビティ]** を選択して、組織内のすべての AD FS アプリケーションの一覧を開きます。

   ![AD FS アプリケーション アクティビティ](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. AD FS アプリケーション アクティビティの一覧に含まれている各アプリケーションについて、**移行の状態** を確認します。

   * **[移行準備完了]** は、AD FS アプリケーション構成が Azure AD で完全にサポートされており、そのままの状態で移行できることを意味します。

   * **[確認が必要です]** は、アプリケーションの設定の一部を Azure AD に移行できるものの、そのままの状態では移行できない設定を確認する必要があることを意味します。

   * **[追加の手順が必要です]** は、アプリケーションの設定の一部が Azure AD でサポートされていないため、現在の状態ではアプリケーションを移行できないことを意味します。

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>移行に向けたアプリケーションの準備状況を評価する 

1. AD FS アプリケーション アクティビティの一覧で、 **[移行の状態]** 列の状態をクリックして、移行の詳細を開きます。 合格した構成テストの概要のほか、移行の潜在的な問題が表示されます。

   ![移行の詳細](media/migrate-adfs-application-activity/migration-details.png)

2. メッセージをクリックして、追加の移行規則の詳細を開きます。 テストされるプロパティの完全な一覧については、下の「[AD FS アプリケーション構成テスト](#ad-fs-application-configuration-tests)」の表を参照してください。

   ![移行規則の詳細](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS アプリケーション構成テスト

次の表に、AD FS アプリケーションに対して実行されるすべての構成テストの一覧を示します。

|結果  |合格/警告/不合格  |説明  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> At least one non-migratable rule was detected for AdditionalAuthentication. (AdditionalAuthentication について、移行できない規則が 1 つ以上検出された)。       | 合格/警告          | 証明書利用者には、多要素認証 (MFA) を要求するための規則があります。 Azure AD に移行するには、これらの規則を条件付きアクセス ポリシーに変換します。 オンプレミス MFA を使用している場合は、Azure AD MFA に移行することをお勧めします。 [条件付きアクセスの詳細についてご確認ください](../authentication/concept-mfa-howitworks.md)。        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Relying party has AdditionalWSFedEndpoint set to true. (証明書利用者の AdditionalWSFedEndpoint が true に設定されている)。       | 合格/不合格          | AD FS の証明書利用者で、複数の WS-Fed アサーション エンドポイントが許可されています。 現在、Azure AD でサポートされているのは 1 つだけです。 この結果によって移行が妨げられている場合は、[ご連絡ください](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)。     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Relying Party has set AllowedAuthenticationClassReferences. (証明書利用者で AllowedAuthenticationClassReferences が設定されている)。       | 合格/不合格          | AD FS のこの設定では、特定の認証の種類のみを許可するようアプリケーションを構成するかどうかを指定します。 この機能を実現するには、条件付きアクセスを使用することをお勧めします。  この結果によって移行が妨げられている場合は、[ご連絡ください](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)。  [条件付きアクセスの詳細についてご確認ください](../authentication/concept-mfa-howitworks.md)。          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | 合格/不合格          | AD FS のこの設定では、SSO Cookie を無視して "**認証のためのプロンプトを毎回表示する**" ようアプリケーションが構成されているかどうかを指定します。 Azure AD では、条件付きアクセス ポリシーを使用して認証セッションを管理し、同様の動作を実現することができます。 [条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)方法を確認してください。          |
|Test-ADFSRPAutoUpdateEnabled <br> Relying Party has AutoUpdateEnabled set to true (証明書利用者で AutoUpdateEnabled が true に設定されている)       | 合格/警告          | AD FS のこの設定では、フェデレーション メタデータ内の変更に基づいてアプリケーションを自動的に更新するよう AD FS を構成するかどうかを指定します。 これは現在 Azure AD でサポートされていませんが、Azure AD へのアプリケーションの移行を妨げることはありません。           |
|Test-ADFSRPClaimsProviderName <br> Relying Party has multiple ClaimsProviders enabled (証明書利用者で複数の ClaimsProviders が有効にされている)       | 合格/不合格          | AD FS のこの設定は、どの ID プロバイダーからの要求を証明書利用者が受け入れているかを示します。 Azure AD では、Azure AD B2B を使用して外部コラボレーションを有効にすることができます。 [Azure AD B2B の詳細をご確認ください](../external-identities/what-is-b2b.md)。          |
|Test-ADFSRPDelegationAuthorizationRules      | 合格/不合格          | アプリケーションで、カスタム委任承認規則が定義されています。 これは、OpenID Connect や OAuth 2.0 などの最新の認証プロトコルを使用することで Azure AD がサポートしている WS-Trust の概念です。 [Microsoft ID プラットフォームの詳細をご確認ください](../develop/v2-protocols-oidc.md)。          |
|Test-ADFSRPImpersonationAuthorizationRules       | 合格/警告          | アプリケーションで、カスタム偽装承認規則が定義されています。 これは、OpenID Connect や OAuth 2.0 などの最新の認証プロトコルを使用することで Azure AD がサポートしている WS-Trust の概念です。 [Microsoft ID プラットフォームの詳細をご確認ください](../develop/v2-protocols-oidc.md)。          |
|Test-ADFSRPIssuanceAuthorizationRules <br> At least one non-migratable rule was detected for IssuanceAuthorization. (IssuanceAuthorization について、移行できない規則が 1 つ以上検出された)。       | 合格/警告          | アプリケーションで、AD FS にカスタム発行承認規則が定義されています。 Azure AD では、Azure AD 条件付きアクセスでこの機能をサポートしています。 [条件付きアクセスの詳細についてご確認ください](../conditional-access/overview.md)。 <br> アプリケーションに割り当てられたユーザーまたはグループによってアプリケーションへのアクセスを制限することもできます。 [アプリケーションにアクセスするユーザーとグループの割り当ての詳細についてご確認ください](./assign-user-or-group-access-portal.md)。            |
|Test-ADFSRPIssuanceTransformRules <br> At least one non-migratable rule was detected for IssuanceTransform. (IssuanceTransform について、移行できない規則が 1 つ以上検出された)。       | 合格/警告          | アプリケーションで、AD FS にカスタム発行変換規則が定義されています。 Azure AD では、トークンで発行された要求のカスタマイズをサポートしています。 詳細については、[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)に関するページを参照してください。           |
|Test-ADFSRPMonitoringEnabled <br> Relying Party has MonitoringEnabled set to true. (証明書利用者で MonitoringEnabled が true に設定されている)。       | 合格/警告          | AD FS のこの設定では、フェデレーション メタデータ内の変更に基づいてアプリケーションを自動的に更新するよう AD FS を構成するかどうかを指定します。 これは現在 Azure AD でサポートされていませんが、Azure AD へのアプリケーションの移行を妨げることはありません。           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | 合格/警告          | AD FS では、SAML トークン内の NotBefore および NotOnOrAfter の時間に基づいて時間のずれが許可されます。 Azure AD では、これは既定で自動的に処理されます。          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Relying Party has RequestMFAFromClaimsProviders set to true. (証明書利用者で RequestMFAFromClaimsProviders が true に設定されている)。       | 合格/警告          | AD FS のこの設定は、別の要求プロバイダーからのユーザーであるときの MFA の動作を決定します。 Azure AD では、Azure AD B2B を使用して外部コラボレーションを有効にすることができます。 次に、条件付きアクセス ポリシーを適用して、ゲスト アクセスを保護することができます。 [Azure AD B2B](../external-identities/what-is-b2b.md) および[条件付きアクセス](../conditional-access/overview.md)の詳細をご確認ください。          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Relying Party has SignedSamlRequestsRequired set to true (証明書利用者で SignedSamlRequestsRequired が true に設定されている)       | 合格/不合格          | アプリケーションは、SAML 要求の署名を検証するよう AD FS で構成されています。 Azure AD は、署名された SAML 要求を受け入れます。ただし、署名は検証しません。 Azure AD には、悪意のある呼び出しから保護するためのさまざまな方法があります。 たとえば、Azure AD では、アプリケーションで構成された応答 URL を使用して SAML 要求を検証します。 Azure AD は、アプリケーション用に構成された応答 URL にのみトークンを送信します。 この結果によって移行が妨げられている場合は、[ご連絡ください](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)。          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | 合格/警告         | アプリケーションでカスタム トークンの有効期間が構成されています。 AD FS での既定値は 1 時間です。 Azure AD では、条件付きアクセスを使用してこの機能をサポートしています。 詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)」を参照してください。          |
|Relying Party is set to encrypt claims. (証明書利用者が、要求を暗号化するよう設定されている)。 これは Azure AD でサポートされています       | 合格          | Azure AD では、アプリケーションに送信されるトークンを暗号化できます。 詳細については、[Azure AD SAML トークン暗号化の構成](./howto-saml-token-encryption.md)に関するページを参照してください。          |
|EncryptedNameIdRequiredCheckResult      | 合格/不合格          | アプリケーションは、SAML トークン内の nameID 要求を暗号化するよう構成されています。 Azure AD では、アプリケーションに送信されるトークン全体を暗号化できます。 特定の要求の暗号化はまだサポートされていません。 詳細については、[Azure AD SAML トークン暗号化の構成](./howto-saml-token-encryption.md)に関するページを参照してください。         |

## <a name="check-the-results-of-claim-rule-tests"></a>要求規則テストの結果を確認する

AD FS でアプリケーションの要求規則を構成している場合は、すべての要求規則について詳細な分析が提供されます。 Azure AD に移動できる要求規則と、さらに確認が必要な要求規則が表示されます。

1. AD FS アプリケーション アクティビティの一覧で、 **[移行の状態]** 列の状態をクリックして、移行の詳細を開きます。 合格した構成テストの概要のほか、移行の潜在的な問題が表示されます。

2. **[移行規則の詳細]** ページで、結果を展開して移行の潜在的な問題の詳細を表示し、追加のガイダンスを確認します。 テストされるすべての要求規則の詳細な一覧については、下の「[要求規則テストの結果を確認する](#check-the-results-of-claim-rule-tests)」の表を参照してください。

   次の例は、IssuanceTransform 規則の移行規則の詳細を示しています。 ここには、アプリケーションを Azure AD に移行する前に確認して対処する必要がある、要求の特定の部分が一覧表示されています。

   ![移行規則の詳細の追加ガイダンス](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>要求規則テスト

次の表に、AD FS アプリケーションに対して実行されるすべての要求規則テストの一覧を示します。

|プロパティ  |説明  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 条件ステートメントで、要求が特定のパターンに一致するかどうかを評価するために正規表現が使用されています。  Azure AD で同様の機能を実現するには、IfEmpty()、StartWith()、Contains() など、事前に定義された変換を使用できます。 詳細については、[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)に関するページを参照してください。          |
|UNSUPPORTED_CONDITION_CLASS      | 条件ステートメントで、発行ステートメントを実行する前に評価する必要がある複数の条件が使用されています。 Azure AD では、複数の要求値を評価できる要求の変換関数でこの機能をサポートする場合があります。  詳細については、[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)に関するページを参照してください。          |
|UNSUPPORTED_RULE_TYPE      | 要求規則を認識できませんでした。 Azure AD で要求を構成する方法の詳細については、[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)に関するページを参照してください。          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | 条件ステートメントで、Azure AD でサポートされていない発行者が使用されています。 現在、Azure AD では、Active Directory または Azure AD と異なるストアからの要求は取得されません。 これによってアプリケーションの Azure AD への移行が妨げられている場合は、[ご連絡ください](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。         |
|UNSUPPORTED_CONDITION_FUNCTION      | 条件ステートメントで、一致の数に関係なく単一の要求を発行または追加するための集計関数が使用されています。  Azure AD では、IfEmpty()、StartWith()、Contains() などの関数を使用して、ユーザーの属性を評価し、要求に使用する値を決定できます。 詳細については、[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)に関するページを参照してください。          |
|RESTRICTED_CLAIM_ISSUED      | 条件ステートメントで、Azure AD で制限されている要求が使用されています。 制限されている要求を発行することはできますが、そのソースを変更したり変換を適用したりすることはできません。 詳細については、[Azure AD の特定のアプリのトークンに出力される要求のカスタマイズ](../develop/active-directory-claims-mapping.md)に関するページを参照してください。          |
|EXTERNAL_ATTRIBUTE_STORE      | 発行ステートメントで、Active Directory と異なる属性ストアが使用されています。 現在、Azure AD では、Active Directory または Azure AD と異なるストアからの要求は取得されません。 この結果によってアプリケーションの Azure AD への移行が妨げられている場合は、[ご連絡ください](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。          |
|UNSUPPORTED_ISSUANCE_CLASS      | 発行ステートメントで、受信した要求セットに要求を追加する ADD が使用されています。 Azure AD では、これを複数の要求変換として構成できます。  詳細については、[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-claims-mapping.md)に関するページを参照してください。         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 発行ステートメントで、出力される要求の値を変換するために正規表現が使用されています。 Azure AD で同様の機能を実現するには、Extract()、Trim()、ToLower など、事前に定義された変換を使用できます。 詳細については、[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)に関するページを参照してください。          |

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>一部の AD FS アプリケーションがレポートに表示されません

 Azure AD Connect Health がインストールされているにも関わらずインストールを求めるメッセージが引き続き表示される場合、または、一部の AD FS アプリケーションがレポートに表示されない場合は、アクティブな AD FS アプリケーションがないか、AD FS アプリケーションが Microsoft アプリケーションである可能性があります。
 
 AD FS アプリケーション アクティビティ レポートには、過去 30 日間にアクティブなユーザー サインインがあった、組織内のすべての AD FS アプリケーションが一覧表示されます。 また、レポートには、Office 365 など、AD FS 内の Microsoft 関連の証明書利用者は表示されません。 たとえば、"urn:federation:MicrosoftOnline"、"microsoftonline"、" microsoft:winhello:cert:prov:server" といった名前の証明書利用者は一覧に表示されません。





## <a name="next-steps"></a>次のステップ

- [ビデオ: AD FS アクティビティ レポートを使用してアプリケーションを移行する方法](https://www.youtube.com/watch?v=OThlTA239lU)
- [Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
- [アプリへのアクセスを管理する](what-is-access-management.md)
- [Azure AD Connect フェデレーション](../hybrid/how-to-connect-fed-whatis.md)
