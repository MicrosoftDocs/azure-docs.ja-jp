---
title: Azure AD のエンタイトルメント管理で外部ユーザーのアクセスを管理する - Azure Active Directory
description: Azure Active Directory のエンタイトルメント管理で外部ユーザーのアクセスを管理するために指定できる設定について説明します。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c78dcbc34deca769739f82964df41ebfc596ea
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176792"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理で外部ユーザーのアクセスを管理する

Azure AD のエンタイトルメント管理では、[Azure AD 企業間 (B2B)](../external-identities/what-is-b2b.md) を利用して、別のディレクトリにいる組織外のユーザーとの共同作業が行われます。 Azure AD B2B では、外部ユーザーはユーザー自身のホーム ディレクトリで認証を行いますが、こちらのディレクトリにユーザーの表現が存在します。 こちらのディレクトリ内のその表現により、ユーザーにこちらのリソースへのアクセスを割り当てることができます。

この記事では、外部ユーザーのアクセスを管理するために指定できる設定について説明します。

## <a name="how-entitlement-management-can-help"></a>エンタイトルメント管理の効果

[Azure AD B2B](../external-identities/what-is-b2b.md) 招待状を使用する場合、こちらのリソース ディレクトリに招待して共同作業する外部ゲスト ユーザーのメール アドレスをあらかじめ知っている必要があります。 これは、小規模または短期間のプロジェクトで作業していて、すべての参加者を既に知っている場合は効果的ですが、一緒に作業したいユーザーが多い場合や、参加者の入れ替わりがある場合は管理が難しくなります。  たとえば、別の組織と仕事をしていて、その組織との連絡窓口が一本化されていたとしても、時間が経てば、その組織の新たなユーザーもアクセスを必要とするようになるでしょう。

エンタイトルメント管理を使用すれば、指定した組織のユーザーがアクセス パッケージを自分で要求できるようにするポリシーを定義できます。 承認が必要かどうか、またアクセスの有効期限を指定できます。 承認が必要な場合は、外部組織から 1 人以上のユーザーをディレクトリに招待し、承認者として指定することもできます。これらのユーザーは、自分の組織のどの外部ユーザーがアクセスを必要としているか、知っている可能性が高いからです。 アクセス パッケージを構成したら、アクセス パッケージのリンクを外部組織の連絡先担当者 (スポンサー) に送信できます。 その連絡先は外部組織の他のユーザーと共有することができ、それらのユーザーがこのリンクを使用してアクセス パッケージを要求できます。 その組織に属する、ディレクトリに招待済みのユーザーもそのリンクを使用できます。

要求が承認されると、エンタイトルメント管理は必要なアクセス権をユーザーにプロビジョニングします。これには、ユーザーがまだディレクトリに参加していない場合に、そのユーザーを招待する処理が含まれる場合があります。 Azure AD では、それらのユーザーの B2B ゲスト アカウントが自動的に作成されます。 管理者が以前に、他組織への招待を許可またはブロックする [B2B 許可/拒否リスト](../external-identities/allow-deny-list.md)を設定することによって、共同作業を許可する組織を制限していた可能性があることに注意してください。  ユーザーが許可/ブロック リストによって許可されていない場合、そのユーザーは招待されません。

外部ユーザーのアクセスを恒久的にしたくない場合、180 日などの有効期限をポリシーに指定します。 180 日経ってもアクセス権が延長されない場合、エンタイトルメント管理ではそのアクセス パッケージに関連付けられているすべてのアクセス権が削除されます。 既定では、エンタイトルメント管理によって招待されたユーザーに他のアクセス パッケージが割り当てられていない場合、最後の割り当てを失った時点で、そのユーザーのゲスト アカウントは 30 日間サインインからブロックされ、その後削除されます。 これにより、不要なアカウントの拡散を防ぎます。 以下のセクションで説明するように、これらの設定は構成可能です。

## <a name="how-access-works-for-external-users"></a>外部ユーザーのアクセスのしくみ

次の図と手順では、アクセス パッケージへのアクセスを外部ユーザーに許可する方法の概要を説明します。

![外部ユーザーのライフサイクルを示す図](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. コラボレーション対象の Azure AD ディレクトリまたはドメインに対して、[接続された組織を追加します](entitlement-management-organization.md)。

1. [[自分のディレクトリ内以外のユーザーの場合]](entitlement-management-access-package-create.md#for-users-not-in-your-directory) ポリシーが含まれるディレクトリに、アクセス パッケージを作成します。

1. 外部組織の連絡先に[マイ アクセス ポータルのリンク](entitlement-management-access-package-settings.md)を送信し、アクセス パッケージを要求するためにユーザーと共有できるようにします。

1. 外部ユーザー (この例では **要求者 A**) は、マイ アクセス ポータルのリンクを使用して、アクセス パッケージへの [アクセスを要求](entitlement-management-request-access.md)します。 ユーザーのサインイン方法は、接続された組織で定義されているディレクトリまたはドメインの認証の種類によって異なります。

1. 承認者が[要求を承認します](entitlement-management-request-approve.md) (または、要求が自動承認されます)。

1. 要求は[配信中状態](entitlement-management-process.md)になります。

1. B2B 招待プロセスを使用して、ゲスト ユーザー アカウントがディレクトリに作成されます (この例では **要求者 A (ゲスト)** )。 [許可リストまたは拒否リスト](../external-identities/allow-deny-list.md)が定義されている場合、リストの設定が適用されます。

1. ゲスト ユーザーに、アクセス パッケージ内のすべてのリソースへのアクセスが割り当てられます。 Azure AD および他の Microsoft Online Services や接続された SaaS アプリケーションに対して変更が行われるまで、しばらく時間がかかることがあります。 詳細については、「[変更が適用されるタイミング](entitlement-management-access-package-resources.md#when-changes-are-applied)」を参照してください。

1. 外部ユーザーは、アクセスが[配信された](entitlement-management-process.md)ことを示すメールを受信します。

1. 外部ユーザーは、メールのリンクをクリックするか、ディレクトリ リソースのいずれかに直接アクセスを試みて招待プロセスを完了することにより、リソースにアクセスできます。

1. ポリシーの設定によっては、時間が経過すると、外部ユーザーに対するアクセス パッケージの割り当てが期限切れになり、外部ユーザーのアクセスが削除されます。

1. 外部ユーザーの設定のライフサイクルによっては、外部ユーザーにアクセス パッケージの割り当てがなくなった場合、外部ユーザーはサインインをブロックされ、ゲスト ユーザー アカウントはディレクトリから削除されます。

## <a name="settings-for-external-users"></a>外部ユーザーの設定

組織外のユーザーがアクセス パッケージを要求し、それらのアクセス パッケージ内のリソースに確実にアクセスできるようにするには、適切に構成されていることを確認しなければならない設定がいくつかあります。

### <a name="enable-catalog-for-external-users"></a>外部ユーザーに対してカタログを有効にする

- 既定では、[新しいカタログ](entitlement-management-catalog-create.md)を作成すると、外部ユーザーがカタログ内のアクセス パッケージを要求できるようになります。 **[外部ユーザーに有効]** が **[はい]** に設定されていることを確認してください。

    ![カタログの設定を編集する](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Azure AD B2B の外部コラボレーション設定を構成する

- ゲストが他のゲストをディレクトリに招待できるようにすることは、エンタイトルメント管理の外部でゲストの招待が発生する可能性があることを意味します。 **[ゲストは招待ができる]** を **[いいえ]** に設定して、適切に管理された招待のみを許可することをお勧めします。
- B2B 許可リストを使用している場合は、エンタイトルメント管理を使用してパートナーにするドメインがリストに追加されていることを確認する必要があります。 または、B2B 拒否リストを使用している場合は、パートナーにするドメインがリストに追加されていないことを確認する必要があります。
- **すべてのユーザー** (すべての接続されている組織と新しい外部ユーザー) にエンタイトルメント管理ポリシーを作成し、ユーザーがディレクトリ内の接続されている組織に属していない場合は、パッケージを要求するときに、接続されている組織が自動的に作成されます。 お使いの B2B の許可または拒否リストの設定が優先されます。 したがって、このポリシーに含めるドメインを、許可リストを使用している場合は許可リストに含め、拒否リストを使用している場合は拒否リストから除外するようにしてください。
- **すべてのユーザー** (すべての接続されている組織とすべての新しい外部ユーザーを含む) のエンタイトルメント管理ポリシーを作成する場合は、まず、ディレクトリに対してメールのワンタイム パスコード認証を有効にする必要があります。 詳細については、「[電子メール ワンタイム パスコード認証](../external-identities/one-time-passcode.md)」を参照してください。
- Azure AD B2B の外部コラボレーション設定の詳細については、「[B2B 外部コラボレーションを有効にしてゲストを招待できるユーザーを管理する](../external-identities/delegate-invitations.md)」を参照してください。

    ![Azure AD の外部コラボレーション設定](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>条件付きアクセス ポリシーを確認する

- 新しいゲスト ユーザーが満たすことができない条件付きアクセス ポリシーから、ゲストを除外するようにしてください。これにより、これらのユーザーはディレクトリにサインインできなくなります。 たとえば、ゲストに登録済みのデバイスがなく、既知の場所にも登録されておらず、多要素認証 (MFA) に再登録したくない場合、条件付きアクセス ポリシーにこれらの要件を追加すると、ゲストのエンタイトルメント管理の使用がブロックされます。 詳細については、「[Azure Active Directory 条件付きアクセスの条件の概要](../conditional-access/concept-conditional-access-conditions.md)」をご覧ください。

    ![Azure AD 条件付きアクセス ポリシーの除外設定](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>SharePoint Online の外部共有設定を確認する

- 外部ユーザーのアクセス パッケージに SharePoint Online サイトを含めるには、組織レベルの外部共有設定が **[すべてのユーザー]** (ユーザーがサインインを必要としない)、または **[新規および既存のゲスト]** (ゲストがサインインするか、確認コードを入力する必要がある) に設定されていることを確認してください。 詳細については、「[外部共有を有効または無効にする](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)」を参照してください。

- エンタイトルメント管理の外部で外部共有を制限するには、外部共有設定を **[既存のゲスト]** に設定します。 その後は、エンタイトルメント管理を通じて招待された新しいユーザーのみが、これらのサイトにアクセスできるようになります。 詳細については、「[外部共有を有効または無効にする](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)」を参照してください。

- サイトレベルの設定で、ゲスト アクセスが有効になっていることを確認します (前述と同じオプションを選択します)。 詳細については、「[サイトの外部共有を有効または無効にする](/sharepoint/change-external-sharing-site)」を参照してください。

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Microsoft 365 グループの共有設定を確認する

- 外部ユーザーのアクセス パッケージに Microsoft 365 グループを含めるには、 **[ユーザーが組織に新しいゲストを追加できるようにします]** が **[オン]** に設定されていることを確認して、ゲスト アクセスを許可します。 詳細については、「[Microsoft 365 グループへのゲスト アクセスの管理](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access)」を参照してください。

- Microsoft 365 グループに関連付けられている SharePoint Online サイトとリソースに外部ユーザーがアクセスできるようにするには、SharePoint Online の外部共有がオンになっていることを確認してください。 詳細については、「[外部共有を有効または無効にする](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)」を参照してください。

- PowerShell のディレクトリ レベルで Microsoft 365 グループのゲスト ポリシーを設定する方法については、「[例:ディレクトリ レベルでグループのゲスト ポリシーを構成する](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)」を参照してください。

### <a name="review-your-teams-sharing-settings"></a>Teams の共有設定を確認する

- 外部ユーザーのアクセス パッケージに Teams を含めるには、 **[Teams へのゲスト アクセスを許可する]** が **[オン]** に設定されていることを確認してください。 詳細については、[Microsoft Teams 管理センターでのゲスト アクセスの構成](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)に関する記事を参照してください。

## <a name="manage-the-lifecycle-of-external-users"></a>外部ユーザーのライフサイクルを管理する

アクセス パッケージ要求が承認されることでディレクトリに招待された外部ユーザーが、アクセス パッケージの割り当てを失ったときに行われる処理を選択できます。 これは、ユーザーがアクセス パッケージの割り当てをすべて放棄した場合、または最後のアクセス パッケージの割り当てが期限切れになった場合に、行われる可能性があります。 既定では、アクセス パッケージの割り当てをすべて失った外部ユーザーは、ディレクトリへのサインインをブロックされます。 30 日後に、ゲスト ユーザー アカウントがディレクトリから削除されます。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューの **[エンタイトルメント管理]** セクションで、 **[設定]** をクリックします。

1. **[編集]** をクリックします。

    ![外部ユーザーのライフサイクルを管理するための設定](./media/entitlement-management-external-users/settings-external-users.png)

1. **[外部ユーザーのライフサイクルを管理する]** セクションで、外部ユーザーに対するさまざまな設定を選択します。

1. 外部ユーザーがアクセス パッケージへの最後の割り当てを失ったときに、そのユーザーによるこのディレクトリへのサインインをブロックする場合は、 **[外部ユーザーによるこのディレクトリへのサインインをブロックする]** を **[はい]** に設定します。

    > [!NOTE]
    > ユーザーがこのディレクトリへのサインインをブロックされている場合、このユーザーはアクセス パッケージを再要求したり、このディレクトリで追加のアクセスを要求したりすることができなくなります。 その後、これらのユーザーが他のアクセス パッケージへのアクセスを要求する必要がある場合は、サインインをブロックするように構成しないでください。

1. 外部ユーザーがアクセス パッケージへの最後の割り当てを失ったときに、このディレクトリ内のゲスト ユーザー アカウントを削除する場合は、 **[外部ユーザーを削除]** を **[はい]** に設定します。

    > [!NOTE]
    > エンタイトルメント管理では、エンタイトルメント管理によって招待されたアカウントのみが削除されます。 また、ユーザーがこのディレクトリ内のアクセス パッケージの割り当てではないリソースに追加されていた場合でも、そのユーザーはサインインをブロックされ、このディレクトリから削除されることに注意してください。 アクセス パッケージの割り当てを受け取る前に、ゲストがこのディレクトリ内に存在していた場合は、そのまま残ります。 ただし、ゲストがアクセス パッケージの割り当てによって招待され、招待された後で OneDrive for Business または SharePoint Online サイトにも割り当てられた場合でも、そのゲストは削除されます。

1. このディレクトリ内のゲスト ユーザー アカウントを削除する場合は、削除するまでの日数を設定できます。 アクセス パッケージへの最後の割り当てが失われた直後にゲスト ユーザー アカウントを削除する場合は、 **[Number of days before removing external user from this directory]\(このディレクトリから外部ユーザーを削除するまでの日数\)** を **0** に設定します。

1. **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [接続されている組織を追加する](entitlement-management-organization.md)
- [自分のディレクトリ内以外のユーザーの場合](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [[トラブルシューティング]](entitlement-management-troubleshoot.md)
