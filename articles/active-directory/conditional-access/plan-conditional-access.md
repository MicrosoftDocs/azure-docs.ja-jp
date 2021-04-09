---
title: Azure Active Directory の条件付きアクセスのデプロイを計画する
description: 条件付きアクセス ポリシーを設計し、組織で効果的にデプロイする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13460fad0ed106f5c0590df961ceca54cb04cd7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102427183"
---
# <a name="plan-a-conditional-access-deployment"></a>条件付きアクセスのデプロイを計画する

アプリとリソースに関する組織のアクセス戦略を実現するために、条件付きアクセスのデプロイを計画することが重要です。

モバイルを重視したクラウド中心の世界では、ユーザーはさまざまなデバイスやアプリを使用してどこからでも組織のリソースにアクセスします。 このため、だれがリソースにアクセスできるかに重点を置くだけでは十分ではなくなっています。 また、ユーザーがいる場所、使用されるデバイス、アクセスされるリソースなども考慮する必要があります。 

Azure Active Directory (Azure AD) の条件付きアクセスは、ユーザー、デバイス、場所などのシグナルを分析して、決定を自動化し、リソースに関する組織のアクセス ポリシーを適用します。 条件付きアクセス ポリシーを使用して、多要素認証 (MFA) などのアクセス制御を適用できます。 条件付きアクセス ポリシーを使用すると、セキュリティのために必要な場合はユーザーに MFA を求め、不要な場合はユーザーに対して何も行わないようにすることができます。

![条件付きアクセスの概要](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Microsoft は、基本レベルのセキュリティを保証する[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)と呼ばれる標準の条件付きポリシーを提供しています。 ただし、組織では、セキュリティの既定値群が提供する柔軟性よりも高い柔軟性が必要になる場合があります。 条件付きアクセスを使用すると、セキュリティの既定値群をより詳細にカスタマイズしたり、要件を満たす新しいポリシーを構成したりすることができます。

## <a name="learn"></a>学習する

始める前に、[条件付きアクセス](overview.md)のしくみと、どのような場合に使用すればよいかを理解しておく必要があります。

### <a name="benefits"></a>メリット

条件付きアクセスをデプロイする利点を次に示します。

* 生産性が向上します。 MFA のようなサインイン条件でユーザーが中断されるのは、それが 1 つ以上のシグナルによって保証されている場合のみです。 条件付きアクセス ポリシーを使用すると、ユーザーに MFA を求めるプロンプトをいつ出すか、アクセスをいつブロックするか、ユーザーが信頼できるデバイスをいつ使用する必要があるかを制御できます。

* リスクを管理します。 ポリシー条件を使用してリスク評価を自動化すると、危険なサインインがただちに特定され、修復またはブロックされます。 条件付きアクセスと [ID 保護](../identity-protection/overview-identity-protection.md)を組み合わせることで、異常および疑わしいイベントが検出され、リソースへのアクセスをいつブロックまたは締め出すかを制御できます。 

* コンプライアンスとガバナンスに対応します。 条件付きアクセスを使用すると、アプリケーションへのアクセスを監査し、同意のための使用条件を提示し、コンプライアンス ポリシーに基づいてアクセスを制限できます。

* コストを管理できます。 アクセス ポリシーを Azure AD に移行することで、条件付きアクセスのためのカスタムまたはオンプレミスのソリューションへの依存度と、それらのインフラストラクチャのコストを削減できます。

### <a name="license-requirements"></a>ライセンスの要件

[条件付きアクセスのライセンス要件](overview.md)に関する記事を参照してください。

追加機能が必要な場合、関連するライセンスが必要になることもあります。 詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

### <a name="prerequisites"></a>前提条件

* Azure AD Premium または試用版ライセンスが有効になっていて、正常に動作している Azure AD テナント。 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 条件付きアクセスの管理者特権があるアカウント。

* パスワードがわかっている管理者以外のユーザー (testuser など)。 ユーザーを作成する必要がある場合は、「[クイックスタート: Azure Active Directory に新しいユーザーを追加する](../fundamentals/add-users-azure-active-directory.md)」を参照してください。

* 管理者以外のユーザーが所属するグループ。 グループを作成する必要がある場合は、[Azure Active Directory でグループを作成し、メンバーを追加する方法](../fundamentals/active-directory-groups-create-azure-portal.md)に関する記事を参照してください。

### <a name="training-resources"></a>トレーニング リソース

条件付きアクセスについて学習する際に、次のリソースが役立ちます。


#### <a name="videos"></a>ビデオ

* [条件付きアクセスとは](https://youtu.be/ffMAw2IVO7A)
* [条件付きアクセスをデプロイする方法](https://youtu.be/c_izIRNJNuk)
* [条件付きアクセス ポリシーをエンド ユーザーにロールアウトする方法](https://youtu.be/0_Fze7Zpyvc)
* [条件付きアクセス ポリシーのユーザーを追加または除外する方法](https://youtu.be/5DsW1hB3Jqs)
* [デバイス コントロールを使用した条件付きアクセス](https://youtu.be/NcONUf-jeS4)
* [Azure AD MFA を使用した条件付きアクセス](https://youtu.be/Tbc-SU97G-w)
* [Conditional Access in Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc) (Enterprise Mobility + Security での条件付きアクセス)


#### <a name="online-courses-on-pluralsight"></a>PluralSight のオンライン コース

* [Design Identity Management in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design) (Microsoft Azure での ID 管理の設計)
* [Design Authentication for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design) (Microsoft Azure の認証の設計)
* [Design Authorization for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design) (Microsoft Azure の承認の設計)

## <a name="plan-the-deployment-project"></a>デプロイ プロジェクトを計画する

お客様の環境でこのデプロイの戦略を決定するときは、お客様の組織のニーズを考慮してください。

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md)し、またそのプロジェクトの役割が明確になっていることを確認します。

### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザー エクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡します。

### <a name="plan-a-pilot"></a>パイロットを計画する

現在の環境で新しいポリシーを使用する準備が整ったら、それらを運用環境で段階的にデプロイします。 最初に、テスト環境で少数のユーザーにポリシーを適用し、ポリシーが想定どおりに動作するかどうかを確認します。 「[パイロットのベスト プラクティス](../fundamentals/active-directory-deployment-plans.md)」を参照してください。

> [!NOTE]
> 管理者に固有ではない新しいポリシーをロールアウトする場合は、すべての管理者を除外します。 これにより、管理者はポリシーにアクセスして、重大な影響がある場合にはそれを変更または取り消すことができます。 すべてのユーザーに適用する前に、必ず小規模なユーザー グループでポリシーを検証してください。

## <a name="understand-conditional-access-policy-components"></a>条件付きアクセス ポリシーのコンポーネントについて
条件付きアクセス ポリシーは、if-then ステートメントです。割り当てが満たされた場合、それらのアクセス制御が適用されます。

条件付きアクセス ポリシーを構成する場合、条件は "*割り当て*" と呼ばれます。 条件付きアクセス ポリシーを使用すると、特定の割り当てに基づいて組織のアプリに対してアクセス制御を適用できます。


詳細については、「[条件付きアクセス ポリシーの構築](concept-conditional-access-policies.md)」を参照してください。

![ポリシー作成の画面](media/plan-conditional-access/create-policy.png)

[割り当て](concept-conditional-access-policies.md#assignments)によって以下を定義します。

* ポリシーの影響を受ける[ユーザーとグループ](concept-conditional-access-users-groups.md)

* ポリシーを適用する先の[クラウド アプリまたはアクション](concept-conditional-access-cloud-apps.md) 

* ポリシーが適用される[条件](concept-conditional-access-conditions.md)

[アクセス制御](concept-conditional-access-policies.md)の設定によって、ポリシーを適用する方法が決まります。

* クラウド アプリへのアクセスを[許可またはブロック](concept-conditional-access-grant.md)します。

* [セッション制御](concept-conditional-access-session.md)によって、特定のクラウド アプリ内でのエクスペリエンスを制限できます。

### <a name="ask-the-right-questions-to-build-your-policies"></a>ポリシーを構築するために適切な質問を行う

ポリシーは、誰がリソースにアクセスするか、どのようなリソースにアクセスするか、どのような条件でアクセスするかについての質問に答えるものです。 アクセスを許可するように、またはアクセスをブロックするようにポリシーを設計できます。 ポリシーで達成しようとしている内容について、適切な質問をしてください。 

ポリシーを構築する前に、ポリシーごとに質問に対する回答を文書化します。 

#### <a name="common-questions-about-assignments"></a>割り当てに関する一般的な質問

[ユーザーとグループ](concept-conditional-access-users-groups.md)

* どのユーザーとグループをポリシーに含めますか、またはポリシーから除外しますか?

* このポリシーには、すべてのユーザー、特定のユーザー グループ、ディレクトリ ロール、または外部のユーザーを含めますか?

[クラウド アプリまたはアクション](concept-conditional-access-cloud-apps.md)

* ポリシーをどのアプリケーションに適用しますか?

* どのようなユーザー アクションがこのポリシーの対象となりますか?

[条件](concept-conditional-access-conditions.md)

* ポリシーに含める、またはポリシーから除外するデバイス プラットフォームはどれですか?

* 組織の信頼できる場所は何ですか?

* ポリシーにどの場所を含め、どの場所を除外しますか?

* どのような種類のクライアント アプリ (ブラウザー、モバイル、デスクトップ クライアント、レガシ認証方法を使用したアプリ) をポリシーに含めますか、またはポリシーから除外しますか?

* Azure AD 参加済みデバイスまたは Hybrid Azure AD 参加済みデバイスをポリシーから除外するポリシーはありますか? 

* [ID 保護](../identity-protection/concept-identity-protection-risks.md)を使用する場合、サインイン リスク保護を組み込みますか?

#### <a name="common-questions-about-access-controls"></a>アクセス制御に関する一般的な質問

[許可またはブロック](concept-conditional-access-grant.md) 

次の 1 つ以上を必須としてリソースへのアクセスを許可しますか?

* Require MFA (MFA が必須)

* デバイスは準拠としてマーク済みである必要がある

* ハイブリッド Azure AD 参加済みのデバイスを必要とする

* 承認済みクライアント アプリを必須にする

* アプリの保護ポリシーを必須にする

[セッション制御](concept-conditional-access-session.md)

クラウド アプリに次のアクセス制御を適用しますか?

* アプリで適用されるアクセス許可を使用する

* アプリの条件付きアクセス制御を使用する

* サインインの頻度を適用する

* 永続的ブラウザー セッションを使用する

### <a name="access-token-issuance"></a>アクセス トークンの発行

アクセス トークンがどのように発行されるかを理解することが重要です。 

![アクセス トークン発行のダイアグラム](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> 割り当てが不要で、条件付きアクセス ポリシーが適用されていない場合、既定の動作ではアクセス トークンが発行されます。 

たとえば、次のようなポリシーを考えてみます。

ユーザーが Group 1 に所属している場合、App 1 へのアクセスに対して MFA を適用します。

Group 1 に所属していないユーザーがこのアプリにアクセスしようとすると、"if" 条件が満たされず、トークンが発行されます。 Group 1 の外部のユーザーを除外するには、他のすべてのユーザーをブロックする個別のポリシーが必要になります。

## <a name="follow-best-practices"></a>ベスト プラクティスに従う

条件付きアクセス フレームワークは、優れた構成柔軟性を提供します。 ただし、柔軟性が高いということは、リリースの前に各構成ポリシーを慎重に見直して、望ましくない結果を避ける必要があることも意味します。

### <a name="apply-conditional-access-policies-to-every-app"></a>条件付きアクセス ポリシーをすべてのアプリに適用する

条件付きアクセス ポリシー条件によってアクセス制御がトリガーされない場合、既定でアクセス トークンが発行されます。 すべてのアプリに少なくとも 1 つの条件付きアクセス ポリシーが適用されていることを確認してください。

> [!IMPORTANT]
> 1 つのポリシーでブロックとすべてのアプリを使用する場合は特に注意してください。 これにより、Azure 管理ポータルから管理者がロックアウトされる可能性があります。また、Microsoft Graph などの重要なエンドポイントに対しては除外を構成できません。

### <a name="minimize-the-number-of-conditional-access-policies"></a>条件付きアクセス ポリシーの数を最小限にする

アプリごとにポリシーを作成するのは効率的ではなく、管理が困難になります。 条件付きアクセスでは、ユーザーごとに最初の 195 個のポリシーのみが適用されます。 アプリを分析し、同じユーザーに対して同じリソース要件があるアプリケーションにそれらをグループ化することをお勧めします。 たとえば、すべての Microsoft 365 アプリまたはすべての HR アプリで同じユーザーに対して同じ要件がある場合、1 つのポリシーを作成し、それが適用されるすべてのアプリを含めます。 

### <a name="set-up-emergency-access-accounts"></a>緊急アクセス用アカウントを設定する

ポリシーを誤って構成した場合、それによって Azure portal から組織がロックアウトされる可能性があります。 組織で[緊急アクセス用アカウント](../roles/security-emergency-access.md)を 2 つ以上作成することで、誤って管理者がロックアウトされた場合の影響を軽減します。

* ポリシー管理専用のユーザー アカウントを作成し、すべてのポリシーから除外します。

### <a name="set-up-report-only-mode"></a>レポート専用モードを設定する

次のような一般的なデプロイ イニシアチブの影響を受けるユーザーの数と名前を予測することは困難です。

* レガシ認証のブロック
* MFA の要求
* サインイン リスク ポリシーの実装

[レポート専用モード](concept-conditional-access-report-only.md)を使用すると、管理者は、環境で条件付きアクセス ポリシーを有効にする前に、その影響を評価できます。

[条件付きアクセス ポリシーでレポート専用モードを構成する](howto-conditional-access-insights-reporting.md)方法について確認してください。

### <a name="plan-for-disruption"></a>中断に対する計画を立てる

MFA や 1 つのネットワークの場所などの単一のアクセス制御に依存して IT システムをセキュリティ保護している場合、その単一のアクセス制御が使用不能になった場合や誤って構成された場合には、アクセス障害が発生する可能性があります。 予期しない中断が発生した場合のロックアウトのリスクを軽減するために、組織に導入する[戦略を計画](../authentication/concept-resilient-controls.md)します。

### <a name="set-naming-standards-for-your-policies"></a>ポリシーの名前付け基準を設定する

名前付け基準があると、Azure 管理ポータルでポリシーを開かなくても、ポリシーを見つけてその用途を理解することができます。 以下を示すようにポリシーの名前を設定することをお勧めします。

* シーケンス番号

* 適用先のクラウド アプリ

* 応答

* 適用されるユーザー

* いつ適用するか (該当する場合)

![ポリシーの命名規則を示すスクリーンショット。](media/plan-conditional-access/11.png)

**例**: 外部ネットワークから Dynamics CRP アプリにアクセスするマーケティング ユーザーに対して MFA を要求するポリシーは次のようになります。

![名前付け基準](media/plan-conditional-access/naming-example.png)

わかりやすい名前は、条件付きアクセスの実装の概要を把握するのに役立ちます。 シーケンス番号は、会話でポリシーを参照する必要がある場合に便利です。 たとえば、管理者と電話で話す場合、問題を解決するためにポリシー CA01 を開くように依頼できます。

#### <a name="naming-standards-for-emergency-access-controls"></a>緊急アクセス制御の名前付け基準

アクティブ ポリシーに加えて、[機能停止や緊急状況のシナリオでセカンダリの回復性があるアクセス制御](../authentication/concept-resilient-controls.md)として機能する停止時のポリシーを実装します。 コンティンジェンシー ポリシーの命名規則には、以下を含める必要があります。
* 他のポリシーから名前が目立つようにするため、先頭に ENABLE IN EMERGENCY (緊急時に有効化)。

* 適用する必要のある中断の名前。

* 管理者がポリシーを有効にする順序を理解できるようにするための、順序シーケンス番号。

**例**

次の名前は、このポリシーが、MFA の中断時に有効にする 4 つのポリシーのうちの最初のポリシーであることを示しています。

EM01 - ENABLE IN EMERGENCY:MFA Disruption [1/4] - Exchange SharePoint:Require hybrid Azure AD join For VIP users.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>サインイン元の国として想定されない国を除外する

Azure Active Directory では、[ネームド ロケーション](location-condition.md)を作成できます。 サインイン元の国として想定されないすべての国を含むネームド ロケーションを作成します。 次に、そのネームド ロケーションからのサインインをブロックする、すべてのアプリ用のポリシーを作成します。 **管理者はこのポリシーから必ず除外してください**。

### <a name="plan-your-policy-deployment"></a>ポリシーのデプロイを計画する

環境用の新しいポリシーの準備ができたら、望ましくない結果を避けるために、各ポリシーをリリース前に必ず確認してください。

## <a name="common-policies"></a>一般的なポリシー

条件付きアクセス ポリシー ソリューションを計画するときは、次の結果を達成するためにポリシーを作成する必要があるかどうかを評価します。

* [MFA の要求](#require-mfa)
* [侵害された可能性があるアカウントに応答する](#respond-to-potentially-compromised-accounts)
* [マネージド デバイスを必須にする](#require-managed-devices)
* [承認済みクライアント アプリケーションを必須にする](#require-approved-client-apps)
* [アクセスをブロックする](#block-access)

### <a name="require-mfa"></a>Require MFA (MFA が必須)

MFA アクセスを必須とする一般的なユース ケース:

* [管理者による](howto-conditional-access-policy-admin-mfa.md)

* [特定のアプリに対する](../authentication/tutorial-enable-azure-mfa.md)

* [すべてのユーザーに対して](howto-conditional-access-policy-all-users-mfa.md)

* [信頼していないネットワークの場所から](untrusted-networks.md)

* [Azure の管理のため](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>侵害された可能性があるアカウントへの応答

条件付きアクセス ポリシーを使用すると、侵害された可能性がある ID からのサインインに対する自動応答を実装できます。 アカウントが侵害された可能性は、リスク レベルの形で表現されます。 ID 保護によって計算される 2 つのリスク レベルがあります。サインイン リスクとユーザー リスクです。 次の 3 つの既定のポリシーを有効にできます。

* [すべてのユーザーが MFA に登録することを必須にする](howto-conditional-access-policy-risk.md)

* [リスクの高いユーザーのパスワードの変更を必須にする](howto-conditional-access-policy-risk.md)

* [サインインのリスクが中以上のユーザーに対して MFA を必須にする](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>マネージド デバイスを必須にする

クラウド リソースへのアクセスでサポートされるデバイスの急増は、ユーザーの生産性の向上に役立ちます。 環境内の特定のリソースに、保護レベルが不明なデバイスがアクセスするのは望ましくない場合があります。 これらのリソースについては、[ユーザーがマネージド デバイスを使用した場合に限りアクセスできるようにする必要があります](require-managed-devices.md)。

### <a name="require-approved-client-apps"></a>承認済みクライアント アプリを必須にする

従業員は個人的な作業と業務上の作業のどちらにもモバイル デバイスを使用します。 BYOD のシナリオでは、デバイス全体を管理するか、デバイスのデータのみを管理するかを決定する必要があります。 データとアクセスのみを管理する場合は、企業のデータを保護できる[承認済みクラウド アプリを必須にする](app-based-conditional-access.md)ことができます。 たとえば、電子メールには、汎用メール プログラムではなく、Outlook モバイルからのみアクセスすることを必須にできます。

### <a name="block-access"></a>アクセスのブロック

[すべてのアクセスをブロック](howto-conditional-access-policy-block-access.md)するオプションは強力です。 これは、たとえば、アプリを Azure AD に移行しているが、まだだれもそれにサインインする準備ができていない場合に使用できます。 アクセスをブロックする: 

* ユーザーの他のすべての割り当てをオーバーライドする

* 組織全体がテナントにサインオンできなくなるようにする機能がある

> [!IMPORTANT]
> すべてのユーザーのアクセスをブロックするポリシーを作成する場合は、ポリシーから緊急アクセス用アカウントを必ず除外し、またすべての管理者を除外することを検討してください。

ユーザーのアクセスをブロックできる他の一般的なシナリオを次に示します。

* クラウド アプリへの[特定のネットワークの場所からのアクセスをブロックする](howto-conditional-access-policy-location.md)。 このポリシーを使用すると、トラフィックの発生元として不適切であると認識している特定の国をブロックできます。

* Azure AD は、レガシ認証をサポートしています。 ただし、レガシ認証では MFA はサポートされておらず、多くの環境では、ID セキュリティに対処するために MFA が必要です。 この場合、[レガシ認証を使用するアプリがテナント リソースにアクセスしないようにブロック](block-legacy-authentication.md)できます。

## <a name="build-and-test-policies"></a>ポリシーをビルドしてテストする

デプロイの各段階で、結果が期待どおりであるか評価します。 

新しいポリシーの準備が整ったら、それらを運用環境で段階的にデプロイします。

* エンド ユーザーに内部的な変更通知を提供します。

* 少数のユーザーから始めて、ポリシーが想定どおりに動作することを確認します。

* より多くのユーザーが含まれるようにポリシーを拡げます。このとき、すべての管理者は引き続き除外します。 管理者を除外することで、変更が必要になった場合に、だれかがポリシーにアクセスできるようにします。

* ポリシーをすべてのユーザーに適用するのは、ポリシーを全面的にテストした後に限ります。 ポリシーが適用されない管理者アカウントが少なくとも 1 つあることを確認します。

### <a name="create-test-users"></a>テスト ユーザーの作成

運用環境のユーザーを反映する一連のテスト ユーザーを作成します。 テスト ユーザーを作成すると、実際のユーザーに影響が及び、アプリやリソースへのアクセスが中断される前に、ポリシーが想定どおりに機能するか確認できます。

この目的のためにテスト テナントを持っている組織もあります。 ただし、ポリシーの結果を完全にテストすることができるすべての条件とアプリをテスト テナントで再現することは困難な場合があります。

### <a name="create-a-test-plan"></a>テスト計画の作成

テスト計画は、予想される結果と実際の結果を比較するために重要です。 何かをテストする前には、常に予想を用意しておきます。 次の表は、テスト ケースの例の概要です。 実際の条件付きアクセス ポリシーを構成する方法に基づいて、このシナリオと予想される結果を調整します。

| ポリシー| シナリオ| 予測される結果 |
| - | - | - |
| [社外ネットワークからの利用は MFA を要求する](untrusted-networks.md)| 承認済みユーザーが信頼できる場所または社内にいるときにアプリにサインインする| ユーザーに MFA は求められません |
| [社外ネットワークからの利用は MFA を要求する](untrusted-networks.md)| 承認済みユーザーが信頼できる場所または社内にいないときにアプリにサインインする| ユーザーには MFA が求められ、正常にサインインできます |
| [(管理者に) MFA を要求する](../fundamentals/concept-fundamentals-security-defaults.md)| 全体管理者がアプリにサインインする| 管理者に MFA が求められます |
| [リスクの高いサインイン](../identity-protection/howto-identity-protection-configure-risk-policies.md)| ユーザーが未承認のブラウザーを使用してアプリにサインインする| 管理者に MFA が求められます |
| [デバイス管理](require-managed-devices.md)| 承認済みユーザーが承認済みデバイスからサインインしようとする| アクセスが許可されます |
| [デバイス管理](require-managed-devices.md)| 承認済みユーザーが承認されていないデバイスからサインインしようとする| アクセスはブロックされます |
| [危険なユーザーのパスワード変更](../identity-protection/howto-identity-protection-configure-risk-policies.md)| 承認済みユーザーが侵害された資格情報を使用してサインインしようとする (高リスクのサインイン)| ユーザーはパスワードを変更するよう求められるか、ポリシーに基づいてアクセスがブロックされます |


### <a name="configure-the-test-policy"></a>テスト ポリシーを構成する

[Azure portal](https://portal.azure.com/) の [Azure Active Directory] > [セキュリティ] > [条件付きアクセス] で条件付きアクセス ポリシーを構成します。

条件付きアクセス ポリシーを作成する方法について詳しくは、この例を参照してください。[ユーザーが Azure portal にサインインしたときに MFA を求める条件付きアクセス ポリシー](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)。 このクイック スタートは、次のために役立ちます。

* ユーザー インターフェイスに慣れる。

* 条件付きアクセスのしくみについて第一印象を得る。

### <a name="enable-the-policy-in-report-only-mode"></a>レポート専用モードでポリシーを有効にする

ポリシーの影響を評価するために、最初に[レポート専用モード](concept-conditional-access-report-only.md)でポリシーを有効にします。 レポート専用のポリシーは、サインイン時に評価されますが、許可制御とセッション制御は適用されません。 レポート専用モードでポリシーを保存すると、サインイン ログでリアルタイム サインインへの影響を確認できます。 サインイン ログから、イベントを選択し、[レポート専用] タブに移動して、各レポート専用ポリシーの結果を確認します。


![レポート専用モード ](media/plan-conditional-access/report-only-mode.png)

ポリシーを選択すると、[ポリシーの詳細] 画面を使用して、ポリシーの割り当てとアクセス制御がどのように評価されたかを確認することもできます。 ポリシーをサインインに適用するには、構成済みの各割り当てが満たされている必要があります。 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>分析情報とレポートのブックを使用してポリシーの影響を理解する

分析情報とレポートのブックで、条件付きアクセス ポリシーの全体的な影響を確認できます。 ブックにアクセスするには、Azure Monitor のサブスクリプションが必要であり、[サインイン ログを Log Analytics ワークスペースにストリーミングする](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)必要があります。 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>what-if ツールを使用してサインインをシミュレートする

条件付きアクセス ポリシーを検証するもう 1 つの方法は、[what-if ツール](troubleshoot-conditional-access-what-if.md)を使用する方法です。これは、仮想的な状況でサインインするユーザーにどのポリシーが適用されるかをシミュレートします。 テストするサインイン属性 (ユーザー、アプリケーション、デバイス プラットフォーム、場所など) を選択し、どのポリシーが適用されるかを確認します。

> [!NOTE] 
> シミュレートされた実行により、条件付きアクセス ポリシーの影響について適切に考察できますが、実際のテスト実行に代わるものではありません。

### <a name="test-your-policy"></a>ポリシーのテスト

テスト ユーザーを使用して、テスト計画で各テストを実行します。

**ポリシーの除外条件を必ずテストしてください**。 たとえば、MFA を必要とするポリシーからユーザーまたはグループを除外することができます。 他のポリシーの組み合わせでは、除外されたユーザーに MFA を要求する可能性があるため、それらのユーザーに MFA を求めるプロンプトが表示されるかどうかをテストする必要があります。

### <a name="roll-back-policies"></a>ポリシーをロールバックする

新しく実装したポリシーをロールバックする必要がある場合は、次の 1 つ以上のオプションを使用します。

* **ポリシーを無効にする。** ポリシーを無効にすると、ユーザーがサインインしようとしたときにポリシーが適用されなくなります。 ポリシーを使用したい場合には、いつでも戻ってそのポリシーを有効にできます。

![ポリシー有効化の画像](media/plan-conditional-access/enable-policy.png)

* **ポリシーからユーザーまたはグループを除外する。** ユーザーがアプリにアクセスできない場合、そのユーザーをポリシーから除外することを選択できます。

![ユーザーとグループの除外](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  このオプションは、ユーザーを信頼できる状態でのみ、控えめに使用してください。 ユーザーはできるだけ早くポリシーまたはグループに追加し直す必要があります。

* **ポリシーを削除する。** ポリシーが不要になった場合は、それを[削除します](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)。

## <a name="manage-access-to-cloud-apps"></a>クラウド アプリへのアクセスを管理する

次の管理オプションを使用して、条件付きアクセス ポリシーを制御および管理します。

![CA ポリシーの管理オプションのスクリーンショット。ネームド ロケーション、カスタム コントロール、使用条件、VPN 接続、クラシック ポリシー (選択されています) などがあります。](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>ネームド ロケーション

条件付きアクセス ポリシーの場所の条件によって、アクセス制御設定をユーザーのネットワークの場所に関連付けることができます。 [ネームド ロケーション](location-condition.md)を使用すると、IP アドレス範囲または国や地域の論理グループを作成できます。

### <a name="custom-controls"></a>カスタム コントロール

[カスタム コントロール](controls.md)によって、Azure AD の外部での認証要件を満たすために、ユーザーは互換性のあるサービスにリダイレクトされます。 このコントロールを満たすために、ユーザーのブラウザーは外部サービスにリダイレクトされ、すべての必要な認証を実行してから、元の Azure AD にリダイレクトされます。 Azure AD は応答を検証し、ユーザーが正常に認証または検証された場合には、そのユーザーは条件付きアクセス フロー内にとどまります。

### <a name="terms-of-use"></a>使用条件

環境内の特定のクラウド アプリへのアクセス前に、使用条件 (ToU) を受け入れてもらうことでユーザーから同意を得ることができます。 この[使用条件を作成するためのクイック スタート](require-tou.md)に関する記事に従ってください。

## <a name="troubleshoot-conditional-access"></a>条件付きアクセスのトラブルシューティングを行う

ユーザーが条件付きアクセス ポリシーに関する問題を抱えている場合は、トラブルシューティングを容易にするために次の情報を収集します。

* ユーザー プリンシパル名

* ユーザー表示名

* オペレーティング システム名

* タイム スタンプ (おおよその時刻でもかまいません)

* ターゲット アプリケーション

* クライアント アプリケーションの種類 (ブラウザーとクライアント)

* 相関 ID (これはサインインに固有です)

ユーザーが [詳細] リンクを含むメッセージを受信した場合、それらのユーザーはこの情報の大部分を収集できます。

![アプリにアクセスできないエラー メッセージ](media/plan-conditional-access/cant-get-to-app.png)

情報を収集した後、次のリソースを参照してください。

* [条件付きアクセスでのサインインの問題](troubleshoot-conditional-access.md) – エラー メッセージと Azure AD のサインイン ログを使用して、条件付きアクセスに関連する予期しないサインイン結果について理解してください。

* [What-If ツールの使用](troubleshoot-conditional-access-what-if.md) - ポリシーが特定の状況でユーザーに適用された理由や適用されなかった理由、ポリシーが既知の状態で適用されるかどうかについて理解してください。

## <a name="next-steps"></a>次の手順

[多要素認証の詳細を確認する](../authentication/concept-mfa-howitworks.md)

[ID 保護の詳細を確認する](../identity-protection/overview-identity-protection.md)

[Microsoft Graph API を使用して条件付きアクセス ポリシーを管理する](/graph/api/resources/conditionalaccesspolicy)
