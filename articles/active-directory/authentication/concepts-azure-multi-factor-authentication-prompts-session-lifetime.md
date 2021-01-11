---
title: Azure Multi-Factor Authentication のプロンプトとセッションの有効期間
description: Azure Multi-Factor Authentication での再認証プロンプトの推奨構成と、セッションの有効期間の適用方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13bbea166d699acead932b1ad6779720f82090e6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919677"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>再認証プロンプトを最適化し、Azure Multi-Factor Authentication のセッションの有効期間を理解する

Azure Active Directory (Azure AD) には、ユーザーが再認証を必要とする頻度を決定する設定が複数あります。 この再認証は、パスワード、FIDO、パスワードレス Microsoft Authenticator などの第 1 要素を使用するか、多要素認証 (MFA) を実行するためである場合があります。 これらの再認証の設定は、必要に応じて、独自の環境と目的のユーザー エクスペリエンスに合わせて構成することができます。

この記事では、推奨される構成と、さまざまな設定のしくみと相互作用について説明します。

## <a name="recommended-settings"></a>推奨設定

適切な頻度でサインインするように要求することで、セキュリティと使いやすさのバランスをユーザーに付与するには、次の構成をお勧めします。

* Azure AD Premium をお使いの場合:
    * [マネージド デバイス](../devices/overview.md)または[シームレス SSO](../hybrid/how-to-connect-sso.md) を使用して、アプリケーション間でシングル サインオン (SSO) を有効にします。
    * 再認証が必要な場合は、条件付きアクセスの[サインイン頻度ポリシー](../conditional-access/howto-conditional-access-session-lifetime.md)を使用します。
    * 管理対象外のデバイスまたはモバイル デバイスからサインインするユーザーの場合は、条件付きアクセスを使用して、永続的なブラウザー セッションとサインイン頻度ポリシーを有効にします。
* Office 365 アプリのライセンスまたは Free レベルの Azure AD をお使いの場合:
    * [マネージド デバイス](../devices/overview.md)または[シームレス SSO](../hybrid/how-to-connect-sso.md) を使用して、アプリケーション間でシングル サインオン (SSO) を有効にします。
    * *[Remain signed-in] (サインインしたままの状態を続ける)* オプションを有効にしたままにして、ユーザーに同意するようガイドします。

この調査では、ほとんどのテナントに対してこれらの設定が適切であることが示されています。 これらの *[MFA を記憶する]* や *[Remain signed-in] (サインインしたままの状態を続ける)* などの設定の組み合わせによっては、ユーザーが認証を頻繁に行うように求めるメッセージが表示される可能性があります。 通常の再認証プロンプトでは、ユーザーの生産性が低下し、攻撃に対して脆弱になる可能性があります。

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD セッションの有効期間の構成設定

ユーザーの認証プロンプトの頻度を最適化するには、Azure AD セッションの有効期間のオプションを構成できます。 ビジネスおよびユーザーのニーズを理解し、環境に最適なバランスを提供する設定を構成してください。

### <a name="evaluate-session-lifetime-policies"></a>セッションの有効期間ポリシーを評価する

セッションの有効期間が設定されていない場合、ブラウザー セッションに永続的な Cookie はありません。 ユーザーがブラウザーを閉じて開くたびに、再認証を求めるプロンプトが表示されます。 Office クライアントでは、既定の期間は 90 日のローリング ウィンドウです。 この既定の Office 構成では、ユーザーが自分のパスワードをリセットした場合、または非アクティブな状態が 90 日を超えた場合、ユーザーは必要なすべての要素 (第 1 要素と第 2 要素) を使用して再認証を行う必要があります。

Azure AD の ID がないデバイスでは、ユーザー向けに複数の MFA プロンプトが表示される場合があります。 各アプリケーションに他のクライアント アプリと共有されていない独自の OAuth 更新トークンがあると、複数のプロンプトが表示されます。 このシナリオでは、MFA を使用して OAuth 更新トークンを検証するよう各アプリケーションから要求されるため、MFA によって複数回プロンプトが表示されます。

Azure AD では、セッションの有効期間に最も制限の厳しいポリシーによって、ユーザーが再認証を必要とするタイミングが決まります。 以下のシナリオについて考えてみます。

* *[Remain signed-in] (サインインしたままの状態を続ける)* を有効にします。これにより、永続的なブラウザー Cookie が使用されます
* また、 *[Remember MFA for 14 days] (14 日間 MFA を記憶する)* も有効にします

このシナリオ例では、ユーザーは 14 日ごとに再認証する必要があります。 この動作は、 *[サインインしたままにする]* 自体でブラウザーでユーザーに再認証を要求としない場合でも、最も制限の厳しいポリシーに従います。

### <a name="managed-devices"></a>マネージド デバイス

Azure AD Join または Hybrid Azure AD Join を使用して Azure AD に参加しているデバイスは、[プライマリ更新トークン (PRT)](../devices/concept-primary-refresh-token.md) を受信して、アプリケーション間でシングル サインオン (SSO) を使用します。 この PRT を使用すると、ユーザーはデバイスに一度サインインすることができ、IT スタッフはセキュリティとコンプライアンスの基準を満たしているかどうかを確認できます。 アプリまたはシナリオによっては、参加しているデバイスでより頻繁にサインインするようにユーザーに求める必要がある場合は、[条件付きアクセスのサインインの頻度](../conditional-access/howto-conditional-access-session-lifetime.md)に関するページを使用することで実現できます。

### <a name="show-option-to-remain-signed-in"></a>サインインしたままにするオプションを表示する

ユーザーがサインイン中に *[サインインの状態を維持]* で **[はい]** を選択した場合は、永続的な Cookie がブラウザーに設定されます。 この永続的な Cookie は、第 1 要素と第 2 要素を記憶し、ブラウザーでの認証要求にのみ適用されます。

![サインインしたままの状態を続けるよう求めるメッセージの例のスクリーンショット](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Azure AD Premium 1 のライセンスをお持ちの場合は、*永続ブラウザー セッション*の条件付きアクセス ポリシーを使用することをお勧めします。 このポリシーは、 *[サインインの状態を維持]* の設定を上書きして、ユーザー エクスペリエンスを向上させます。 Azure AD Premium 1 のライセンスをお持ちでない場合は、ユーザーの [サインインの状態を維持] 設定を有効にすることをお勧めします。

ユーザーがサインインしたままの状態を続けるためのオプションの構成の詳細については、「[Azure AD サインイン ページをカスタマイズする](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)」を参照してください。

### <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication の記憶  

この設定では、1 ～ 60 日の間の値を設定でき、ブラウザーでユーザーがサインイン時に **[今後 X 日間はこのメッセージを表示しない]** オプションを選択したときに永続的な Cookie が設定されます。

![サインイン要求の承認を求めるメッセージの例のスクリーンショット](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

この設定によって Web アプリの認証回数が減りますが、Office クライアントなどの最新の認証クライアントで認証回数が増えます。 これらのクライアントでは、通常、パスワードのリセットまたは 90 日の非アクティブな状態の経過後にのみメッセージが表示されます。 ただし、 *[MFA を記憶する]* の最大値は 60 日です。 **[Remain signed-in] (サインインしたままの状態を続ける)** または条件付きアクセス ポリシーと組み合わせて使用すると、認証要求の数が増える場合があります。

*[MFA を記憶する]* を使用していて、Azure AD Premium 1 のライセンスをお持ちの場合は、これらの設定を条件付きアクセスのサインイン頻度に移行することを検討してください。 それ以外の場合は、代わりに *[サインインしたままにする]* の使用を検討してください。

詳細については、「[Multi-Factor Authentication の記憶](howto-mfa-mfasettings.md#remember-multi-factor-authentication)」を参照してください。

### <a name="authentication-session-management-with-conditional-access"></a>条件付きアクセスを使用した認証セッション管理

**[サインインの頻度]** では、管理者は、クライアントとブラウザーの両方の第 1 要素と第 2 要素の両方に適用されるサインインの頻度を選択できます。 重要なビジネス アプリケーションなどで認証セッションを制限する必要があるシナリオでは、マネージド デバイスの使用と共にこれらの設定を使用することをお勧めします。

**永続的なブラウザー セッション**では、ユーザーはブラウザー ウィンドウを閉じてから再度開いた後でもサインインした状態を維持できます。 *[Remain signed-in] (サインインしたままの状態を続ける)* 設定と同様に、これによりブラウザーに永続的な Cookie が設定されます。 ただし、管理者によって構成されているので、ユーザーが *[サインインしたままにする]* オプションで **[はい]** を選択する必要がないため、ユーザー エクスペリエンスが向上します。 *[Remain signed-in] (サインインしたままの状態を続ける)* オプションを使用する場合は、代わりに **[永続的ブラウザー セッション]** ポリシーを有効にすることをお勧めします。

詳しくは、 「[条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)」を参照してください。

### <a name="configurable-token-lifetimes"></a>構成可能なトークンの有効期間

この設定により、Azure Active Directory によって発行されたトークンの有効期間を構成できます。 このポリシーは、「*条件付きアクセスを使用した認証セッション管理*」に置き換えられます。 現在、*構成可能なトークンの有効期間*を使用している場合は、条件付きアクセス ポリシーへの移行を開始することをお勧めします。

## <a name="review-your-tenant-configuration"></a>テナント構成を確認する  

さまざまな設定のしくみと推奨される構成について理解しましたので、次はテナントの構成を確認し、それに応じて変更を加えます。

*[Remain signed-in] (サインインしたままの状態を続ける)* オプションを構成または確認するには、次の手順を実行します。

1. Azure AD portal で、 *[Azure Active Directory]* を検索して選択します。
1. **[会社のブランド]** を選択してから、ロケールごとに **[サインインしたままにするオプションを表示する]** を選択します。
1. *[はい]* を選択してから、 **[保存]** を選択します。

Multi-Factor Authentication の設定を記憶するには、次の手順を実行します。

1. Azure AD portal で、 *[Azure Active Directory]* を検索して選択します。
1. **セキュリティ** を選択してから、**MFA** を選択します。
1. **[構成]** で、 **[追加のクラウドベースの MFA 設定]** を選択します。
1. *Multi-Factor Authentication サービス設定*のページで、**Multi-Factor Authentication の設定の記憶**までスクロールします。 このチェック ボックスをオフにして、設定を無効にします。

サインインの頻度と永続的なブラウザー セッションの条件付きアクセス ポリシーを構成するには、次の手順を実行します。

1. Azure AD portal で、 *[Azure Active Directory]* を検索して選択します。
1. **[セキュリティ]** を選択してから、 **[条件付きアクセス]** を選択します。
1. この記事で詳しく説明している推奨のセッション管理オプションを使用して、ポリシーを構成します。

トークンの有効期間を確認するには、[Azure AD PowerShell を使用して Azure AD ポリシーに対してクエリを実行します](../develop/active-directory-configurable-token-lifetimes.md#prerequisites)。 使用しているすべてのポリシーを無効にします。

テナントで複数の設定が有効になっている場合は、使用可能なライセンスに基づいて設定を更新することをお勧めします。 たとえば、Azure AD Premium ライセンスをお持ちの場合は、 *[サインインの頻度]* と *[永続的ブラウザー セッション]* の条件付きアクセス ポリシーのみを使用する必要があります。 Office 365 アプリまたは Azure AD 無料ライセンスをお持ちの場合は、 *[Remain signed-in?] (サインインしたままの状態を続ける)* を使用する必要があります。

構成可能なトークンの有効期間が有効になっている場合、この機能はすぐに削除されます。 条件付きアクセス ポリシーへの移行を計画します。

ライセンスに基づく推奨事項を次の表に示します。

|              | Azure AD Free と Office 365 アプリ | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | アンマネージド デバイスの [Azure AD 参加](../devices/concept-azure-ad-join.md)または [Hybrid Azure AD 参加](../devices/concept-azure-ad-join-hybrid.md)、または[シームレス SSO](../hybrid/how-to-connect-sso.md)。 | Azure AD 参加<br />ハイブリッド Azure AD 参加 |
| **再認証の設定** | Remain signed-in (サインインしたままの状態を続ける)                  | サインインの頻度と永続的なブラウザー セッションに条件付きアクセス ポリシーを使用する |

## <a name="next-steps"></a>次のステップ

まずは、[Azure Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保](tutorial-enable-azure-mfa.md)したり、[ユーザー サインインのリスク検出を使用して、Azure Multi-Factor Authentication をトリガーしたりする](tutorial-risk-based-sspr-mfa.md)ためのチュートリアルを参照してください。
