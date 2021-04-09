---
title: Azure Active Directory B2B コラボレーションによって管理コラボレーションに移行する
description: Azure Ad B2B コラボレーションによって、管理コラボレーションに移行します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648589"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B コラボレーションによって管理コラボレーションに移行する 

リソースへの外部アクセスをセキュリティで保護するには、コラボレーションを管理下に置くことが重要です。 この記事に進む前に、次のことが完了していることを確認してください。

* [セキュリティ体制を判別する](1-secure-access-posture.md)

* [現在の状態を理解する](2-secure-access-current-state.md)

* [セキュリティ プランを作成する](3-secure-access-plan.md)

* [グループとセキュリティがどのように連携するかを理解する](4-secure-access-groups.md)

これらのことが完了すると、管理コラボレーションに移行できるようになります。 この記事では、すべての外部コラボレーションを [Azure Active Directory B2B コラボレーション](../external-identities/what-is-b2b.md) (Azure AD B2B) に移行する手順について説明します。 Azure Ad B2B は [Azure AD External Identities](../external-identities/compare-with-b2c.md) の機能です。

## <a name="control-who-your-organization-collaborates-with"></a>組織のコラボレーション相手を管理する

ユーザーがコラボレーションを行うことができる組織と、コラボレーションを開始できる組織内のユーザーを制限するかどうかを決定する必要があります。 ほとんどの組織は、事業単位に対してコラボレーション相手の決定を許可し、必要に応じて承認と監視を委任するという方法を採用しています。 たとえば、一部の政府機関、教育機関、金融サービス組織は、オープン コラボレーションを許可していません。 このセクションの残りの部分で説明するように、Azure AD 機能を使用してコラボレーションのスコープを指定することもできます。

### <a name="determine-collaboration-partners"></a>コラボレーション パートナーを決定する

まず、現在コラボレーションを行っている組織と、それらの組織のユーザーのドメインを文書化しておいてください。 1 つのコラボレーション パートナーが複数のドメインを所有している場合があります。 たとえば、1 つのパートナーに、別々のドメインを持つ複数の事業単位が含まれる場合があります。

次に、以下のドメインとの将来のコラボレーションを有効にするかどうかを決定します。 

* 任意のドメイン (最も包括的)

* 明示的に拒否されたドメインを除くすべてのドメイン 

* 特定のドメインのみ (最も限定的)

> [!NOTE]
> コラボレーション設定が限定的であるほど、ユーザーが承認済みコラボレーション フレームワークの範囲外となる可能性が高くなります。 セキュリティに関するニーズによって許容される最大限に広範なコラボレーションを有効にし、過度に制限するのではなく、コラボレーションを綿密に確認することをお勧めします。 

また、1 つのドメインに制限すると、ユーザー用に他の無関係なドメインを持っている組織との承認済みのコラボレーションが、意図せず妨げられる可能性があります。 たとえば、Contoso 社と取引を行っている場合、Contoso との最初の通信ポイントは、".com" ドメインを持つ電子メールを所有する米国の従業員の 1 人である可能性があります。 しかし、".com" ドメインのみを許可した場合、".ca" ドメインを持つカナダの従業員を意図せず除外してしまう可能性があります。 

特定のコラボレーション パートナーのみを許可する必要が生じる場合があります。 たとえば、大学のシステムで、学内の教職員のみにリソース テナントへのアクセスを許可する場合があります。 また、複合企業で、特定の子会社のみが相互にコラボレーションを行って、必要なフレームワークへの準拠を実現できるようにする場合があります。

#### <a name="using-allow-and-deny-lists"></a>許可リストと拒否リストの使用

許可リストまたは拒否リストを使用して、[B2B ユーザーに対する特定組織からの招待を制限する](../external-identities/allow-deny-list.md)ことができます。 使用できるのは、許可リストのみまたは拒否リストのみです。両方を使用することはできません。

* [許可リスト](../external-identities/allow-deny-list.md)により、リストされているドメインにのみコラボレーションが制限されます。他のすべてのドメインは、事実上、拒否リストに表示されます。

* [拒否リスト](../external-identities/allow-deny-list.md)により、拒否リストに含まれていないドメインとのコラボレーションが許可されます。

> [!IMPORTANT]
> これらのリストは、ディレクトリ内に既に存在するユーザーには適用されません。 また、これらは OneDrive for Business にも適用されず、SharePoint では個別の拒否リストが許可されます。

一部の組織では、管理対象セキュリティ プロバイダーによって拒否リストに指定された既知の "不正ユーザー" ドメインのリストを使用しています。 たとえば、組織が Contoso と正当にビジネスを行い、.com ドメインを使用している場合、関連のない組織が Contoso の .org ドメインを使用し、Contoso の従業員を偽装するフィッシング攻撃を試みる可能性があります。 

## <a name="control-how-external-users-gain-access"></a>外部ユーザーがアクセス権を取得する方法を制御する

Azure AD B2B を使用して外部パートナーとコラボレーションを行うには、さまざまな方法があります。 コラボレーションを開始するには、パートナーがリソースにアクセスできるように招待するか、または許可します。 ユーザーは、次の操作に応答することでアクセス権を取得できます。

* [電子メール経由で送信された招待](../external-identities/redemption-experience.md)の引き換え、またはリソースを[共有するための直接リンク](../external-identities/redemption-experience.md)

* 作成した[アプリケーションを使用したアクセス](../external-identities/self-service-sign-up-overview.md)の要求

* [マイ アクセス](../governance/entitlement-management-request-access.md) ポータルを使用したアクセスの要求

B2B Azure AD を有効にすると、既定では、直接リンクや電子メールの招待を使用してゲスト ユーザーを招待する機能が有効になります。 電子メール OTP とセルフサービス ポータルを使用した招待は、現在プレビュー段階であり、Azure AD ポータルの [外部 ID] > [外部コラボレーション設定] で有効にする必要があります。

### <a name="control-who-can-invite-guest-users"></a>ゲスト ユーザーを招待できるユーザーを制御する

リソースにアクセスするようゲスト ユーザーを招待できるユーザーを決定します。

* 最も限定的な設定は、管理者と、[ゲスト招待元ロール](../external-identities/delegate-invitations.md)を付与されたユーザーのみがゲストを招待できるようにすることです。

* セキュリティ要件で許可されている場合は、userType が Member であるすべてのユーザーがゲストを招待できるようにすることをお勧めします。

* userType が Guest (Azure AD B2B ユーザーの既定のアカウントの種類) であるユーザーが他のゲストを招待できるようにするかどうかを決定します。 

![ゲスト招待の設定のスクリーンショット。](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>外部ユーザーに関する追加情報を収集する

Azure AD のエンタイトルメント管理を使用する場合は、外部ユーザーが回答する質問を構成できます。 これらの質問はその後、承認者に示され、承認者の意思決定に役立ちます。 承認者が承認しているアクセスに関連する情報を得られるように、[アクセス パッケージ ポリシー](../governance/entitlement-management-access-package-approval-policy.md)ごとに異なる一連の質問を構成することができます。 たとえば、あるアクセス パッケージがベンダーへのアクセスを目的としている場合、要求元にベンダーの契約番号を要求できます。 供給元を対象とした別のアクセス パッケージでは、原産国を要求できます。

セルフサービス ポータルを使用する場合は、[API コネクタ](../external-identities/api-connectors-overview.md)を使用して、サインアップ時にユーザーに関する追加の属性を収集できます。 その後、これらの属性を使用してアクセス権を割り当てることができます。 たとえば、サインアップ プロセス中に供給元 ID を収集する場合、その属性を使用して、その供給元のグループまたはアクセス パッケージに動的に割り当てることができます。 Azure portal でカスタム属性を作成し、セルフサービス サインアップのユーザー フローでそれらを使用できます。 また、[Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md) を使用してこれらの属性を読み書きすることもできます。 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Azure AD ユーザーへの招待の引き換えに関するトラブルシューティング

Azure AD を使用しているコラボレーション パートナーの招待されたゲスト ユーザーが、招待の引き換えの問題に直面するケースには、次の 3 つの状況があります。

* 許可リストを使用していて、ユーザーのドメインが許可リストに含まれていない場合。

* コラボレーション パートナーのホーム テナントに、外部ユーザーとのコラボレーションを妨げるテナントの制限がある場合。

* ユーザーがパートナーの Azure AD テナントに含まれていない場合。 たとえば、Active Directory (または別のオンプレミスの IdP) 内のみに存在する contoso.com のユーザーは、電子メール OTP プロセスを使用してのみ、招待を引き換えることができます。 詳細については、[招待の引き換えフロー](../external-identities/redemption-experience.md)を参照してください。

## <a name="control-what-external-users-can-access"></a>外部ユーザーがアクセスできる内容を制御する

ほとんどの組織はモノリシックではありません。 つまり、外部ユーザーと共有してよいリソースと、外部ユーザーにアクセスを許可しないリソースがあります。 そのため、外部ユーザーがアクセスできる内容を制御する必要があります。 [エンタイトルメント管理とアクセス パッケージを使用して特定のリソースへのアクセスを制御する](6-secure-access-entitlement-managment.md)ことを検討してください。

既定でゲスト ユーザーは、テナント メンバーおよびその他のパートナー (グループ メンバーシップを含む) に関する情報と属性を表示できます。 この情報への外部ユーザーのアクセスを制限することがセキュリティ要件で要求されるかどうかを考慮してください。

![外部コラボレーション設定の構成のスクリーンショット。](media/secure-external-access/5-external-collaboration-settings.png)

ゲスト ユーザーに次の制限を使用することをお勧めします。

* **ディレクトリ内の参照グループおよびその他のプロパティへのゲスト アクセスを制限する**。

   * 外部コラボレーション設定を使用して、ゲストが所属していないグループを読み取る機能を制限します。

* **従業員専用アプリへのアクセスをブロックする**。

   * 条件付きアクセス ポリシーを作成して、ゲスト以外のユーザーにのみ適した Azure AD 統合アプリケーションへのアクセスをブロックします。 

* **Azure portal へのアクセスをブロックする。まれに必要となる例外を作成できます**。 

   * すべてのゲスト ユーザーと外部ユーザーのどちらかを含む条件付きアクセス ポリシーを作成してから、[アクセスをブロックするポリシーを実装します](../../role-based-access-control/conditional-access-azure-management.md)。

 

## <a name="remove-users-who-no-longer-need-access"></a>アクセスが不要になったユーザーを削除する

[アクセスが不要になったユーザーを確認して削除する](../governance/access-reviews-external-users.md)ために、現在のアクセスを評価します。 テナントに外部ユーザーをゲストとして追加し、メンバー アカウントを持つユーザーを追加します。 

一部の組織では、ベンダー、パートナー、請負業者などの外部ユーザーをメンバーとして追加しています。 これらのメンバーには、特定の属性、または次のような文字で始まるユーザー名を指定できます。

* ベンダーの場合は v-

* パートナーの場合は p-

* 請負業者の場合は c-

メンバー アカウントを持つ外部ユーザーを評価して、引き続きアクセスする必要があるかどうかを判断します。 その場合は、次のセクションで説明するように、これらのユーザーを Azure AD B2B に移行します。

また、エンタイトルメント管理または Azure AD B2B を通じて招待されていないゲスト ユーザーがいる場合もあります。

これらのユーザーを見つけるには、次の手順に従います。

* [エンタイトルメント管理を通じて招待されていないゲスト ユーザーを見つける](../governance/access-reviews-external-users.md)。

   * [PowerShell スクリプトのサンプル](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)が提供されています。

次のセクションで説明するように、これらのユーザーを B2B ユーザー Azure AD に移行します。

## <a name="transition-your-current-external-users-to-b2b"></a>現在の外部ユーザーを B2B に移行する

Azure AD B2B を使用していない場合は、テナントに従業員以外のユーザーが存在する可能性があります。 これらのアカウントを Azure AD B2B 外部ユーザー アカウントに移行してから、userType を Guest に変更することをお勧めします。 これにより、Azure AD と Microsoft 365 でさまざまな方法を活用して、外部ユーザーを異なる方法で扱うことができます。 たとえば、次のような方法があります。

* 条件付きアクセス ポリシーにゲスト ユーザーを簡単に含めたり除外したりする。

* アクセス パッケージとアクセス レビューにゲスト ユーザーを簡単に含めたり除外したりする。

* Teams、SharePoint、およびその他のリソースへの外部アクセスを簡単に含めたり除外したりする。

現在のアクセス、UPN、およびグループ メンバーシップを維持しながら、これらの内部ユーザーを移行するには、[B2B コラボレーションへの外部ユーザーの招待](../external-identities/invite-internal-users.md)に関するページを参照してください。

## <a name="decommission-undesired-collaboration-methods"></a>不要になったコラボレーション方法の使用を停止する

管理コラボレーションへの移行を完了するには、意図しないコラボレーション方法の使用を停止する必要があります。 どの方法の使用を停止するかは、IT 部門がコラボレーションを制御する程度かと、セキュリティ体制によって決まります。 IT 部門とエンド ユーザーによる制御の詳細については、「[外部アクセスに対するセキュリティ対策を決定する](1-secure-access-posture.md)」を参照してください。

次に、評価する必要のあるコラボレーション方法を示します。

### <a name="direct-invitation-through-microsoft-teams"></a>Microsoft Teams を使用した直接招待

Teams では、既定で外部アクセスが許可されます。つまり、組織はすべての外部ドメインと通信できます。 特定のドメインを Teams に限って制限または許可する場合は、[Teams 管理ポータル](https://admin.teams.microsoft.com/company-wide-settings/external-communications)で行うことができます。 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>SharePoint と OneDrive を使用した直接共有

SharePoint と OneDrive を使用して直接共有すると、エンタイトルメント管理プロセス外でユーザーを追加できます。 これらの構成の詳細については、[Microsoft Teams、SharePoint、および OneDrive for business を使用したアクセスの管理](9-secure-access-teams-sharepoint.md)に関するページを参照してください。また、必要に応じて、[ユーザーの個人用 OneDrive の使用をブロックする](/office365/troubleshoot/group-policy/block-onedrive-use-from-office)こともできます。

### <a name="sending-documents-through-email"></a>電子メールを使用したドキュメントの送信

ユーザーは、外部ユーザーにドキュメントを電子メールで送信します。 秘密度ラベルを使用して、これらのドキュメントへのアクセスを制限および暗号化する方法を検討してください。 詳細については、秘密度ラベルを使用したアクセスの管理に関するページを参照してください。

### <a name="unsanctioned-collaboration-tools"></a>未承認のコラボレーション ツール

コラボレーション ツールの種類は膨大です。 ユーザーは公式の職務外で、Google Docs、DropBox、Slack、Zoom などのプラットフォームを含め、さまざまなツールを使用している可能性があります。 ファイアウォール レベルで、組織で管理されているデバイスに対するモバイル アプリケーション管理を使用して、企業ネットワークからこのようなツールの使用をブロックすることができます。 ただし、これらのプラットフォームの承認済みのインスタンスもブロックされ、また、管理されていないデバイスからのアクセスはブロックされません。 必要に応じて不要なプラットフォームをブロックし、使用する必要があるプラットフォームに対して未承認の使用がないようにビジネス ポリシーを作成します。 

未承認のアプリケーションの管理の詳細については、以下を参照してください。

* [接続されているアプリを管理する](/cloud-app-security/governance-actions)

* [アプリケーションを承認および却下する](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに対するセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md) (この記事)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)