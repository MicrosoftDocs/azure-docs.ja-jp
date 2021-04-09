---
title: Azure AD エンタイトルメント管理でアクセス パッケージの要求設定を変更する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理でアクセス パッケージの要求設定を変更する方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b6bc00af8d54c8748dd82b934974282e0e8da0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426962"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージの要求設定を変更する

アクセス パッケージの管理者は、ポリシーを編集するか新しいポリシーを追加することで、アクセス パッケージを要求できるユーザーをいつでも変更できます。 この記事では、既存のアクセス パッケージの要求設定を変更する方法について説明します。

## <a name="choose-between-one-or-multiple-policies"></a>1 つまたは複数のポリシーを選択する

アクセス パッケージを要求できるユーザーを指定するには、ポリシーを使用します。 アクセス パッケージで新しいポリシーを作成したり、既存のポリシーを編集したりする前に、アクセス パッケージに必要なポリシーの数を決定する必要があります。 

アクセス パッケージを作成するときに、ポリシーを作成する要求設定を指定します。 ほとんどのアクセス パッケージのポリシーは 1 つですが、1 つのアクセス パッケージに複数のポリシーを設定できます。 異なるユーザーのセットに異なる要求と承認が設定された割り当てを付与できるようにしたい場合は、アクセス パッケージ用の複数のポリシーを作成します。 

たとえば、1 つのポリシーを使用して、内部ユーザーと外部ユーザーを同じアクセス パッケージに割り当てることはできません。 ただし、同じアクセス パッケージ内に 2 つのポリシー (内部ユーザー用のポリシーと外部ユーザー用のポリシー) を作成することができます。 ユーザーに適用されるポリシーが複数ある場合は、要求時に、割り当て先にするポリシーを選択するよう求められます。 次の図は、2 つのポリシーがあるアクセス パッケージを示しています。

![アクセス パッケージ内の複数のポリシー](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>必要なポリシーの数

| シナリオ | ポリシーの数 |
| --- | --- |
| ディレクトリのユーザー全員に対して、アクセス パッケージの要求と承認の設定を同じにしたい | 1 つ |
| 特定の接続された組織のユーザー全員がアクセス パッケージを要求できるようにしたい | 1 つ |
| ディレクトリのユーザーだけでなくディレクトリ外のユーザーもアクセス パッケージを要求できるようにしたい | 複数 |
| 一部のユーザーに対して異なる承認設定を指定したい | 複数 |
| アクセス パッケージの割り当てに有効期限があるユーザーと、アクセス権を自分で延長できるユーザーを分けたい | 複数 |

複数のポリシーが適用されるときに使用される優先度ロジックについては、[複数のポリシー](entitlement-management-troubleshoot.md#multiple-policies
)を参照してください。

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>既存のアクセス パッケージを開き、要求設定の新しいポリシーを追加します

異なる要求と承認の設定を持つことが望ましいユーザーのセットがある場合、おそらく新しいポリシーを作成する必要があります。 既存のアクセス パッケージに新しいポリシーを追加するには、次の手順に従います。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[ポリシー]** をクリックし、**[ポリシーの追加]** をクリックします。

1. **[基本]** タブで開始します。ポリシーの名前と説明を入力します。

    ![名前と説明を含むポリシーを作成する](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **[次へ]** をクリックして、 **[要求]** タブを開きます。

1. **[アクセス権を要求できるユーザー]** 設定を変更します。 次のセクションの手順を使用して、設定を次のいずれかのオプションに変更します。 
    - [ディレクトリ内のユーザーの場合](#for-users-in-your-directory) 
    - [自分のディレクトリ内以外のユーザーの場合](#for-users-not-in-your-directory)
    - [なし (管理者直接割り当てのみ)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>ディレクトリ内のユーザーの場合

ディレクトリ内のユーザーがこのアクセス パッケージを要求できるようにする場合は、次の手順に従います。 要求ポリシーを定義するときは、個々のユーザーの指定を行うことも、より一般的なユーザー グループの指定を行うこともできます。 たとえば、組織には **すべての従業員** のようなグループが既に存在している場合があります。  アクセスを要求できるユーザーに対するポリシーにそのグループが追加されている場合、そのグループのすべてのメンバーがアクセスを要求できます。

1. **[Users who can request access]\(アクセスを要求できるユーザー\)** セクションで、 **[For users in your directory]\(ディレクトリ内のユーザー\)** をクリックします。

    このオプションを選択すると、ディレクトリ内でこのアクセス パッケージを要求できるユーザーをさらに絞り込むための新しいオプションが表示されます。

    ![アクセス パッケージ - 要求 - ディレクトリ内のユーザー](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. 以下のオプションの 1 つを選択します。

    |  |  |
    | --- | --- |
    | **特定のユーザーとグループ** | 指定したディレクトリ内のユーザーとグループのみがこのアクセス パッケージを要求できるようにする場合は、このオプションを選択します。 |
    | **All members (excluding guests) (すべてのメンバー (ゲストを除く))** | ディレクトリ内のすべてのメンバー ユーザーがこのアクセス パッケージを要求できるようにする場合は、このオプションを選択します。 このオプションには、ディレクトリに招待したゲスト ユーザーは含まれません。 |
    | **All users (including guests) (すべてのユーザー (ゲストを含む))** | ディレクトリ内のすべてのメンバー ユーザーとゲスト ユーザーがこのアクセス パッケージを要求できるようにする場合は、このオプションを選択します。 |

    ゲスト ユーザーは、[Azure AD B2B](../external-identities/what-is-b2b.md) でディレクトリに招待された外部ユーザーを指します。 メンバー ユーザーとゲスト ユーザーの違いについて詳しくは、「[Azure Active Directory の既定のユーザー アクセス許可とは](../fundamentals/users-default-permissions.md)」を参照してください。

1. **[特定のユーザーとグループ]** を選択した場合は、 **[ユーザーとグループの追加]** をクリックします。

1. [ユーザーとグループの選択] ウィンドウで、追加するユーザーとグループを選択します。

    ![アクセス パッケージ - 要求 - ユーザーとグループの選択](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. **[選択]** をクリックしてユーザーとグループを追加します。

1. 承認を要求する場合は、「[Azure AD エンタイトルメント管理でアクセス パッケージの承認設定を変更する](entitlement-management-access-package-approval-policy.md)」の手順を使用して、承認設定を構成します。

1. [[要求の有効化]](#enable-requests) セクションに移動します。
 
## <a name="for-users-not-in-your-directory"></a>ディレクトリ内にいないユーザーの場合

 **ディレクトリ内にいないユーザー** とは、別の Azure AD ディレクトリまたはドメイン内のユーザーのことを指します。 これらのユーザーは、ディレクトリにまだ招待されていない可能性があります。 Azure AD ディレクトリは、 **[コラボレーションの制限]** で招待を許可するように構成する必要があります。 詳細については、「[B2B 外部コラボレーションを有効にしてゲストを招待できるユーザーを管理する](../external-identities/delegate-invitations.md)」を参照してください。

> [!NOTE]
> 要求が承認されるか自動承認される、ディレクトリ内にまだ存在しないユーザーについて、ゲスト ユーザー アカウントが作成されます。 ゲストは招待されますが、招待メールは届きません。 その代わりに、自分のアクセス パッケージの割り当てが配信されるときに電子メールを受け取ります。 既定では、最後の割り当てが期限切れになったかキャンセルされたことが原因でそのゲスト ユーザーにアクセス パッケージの割り当てがなくなった場合、そのゲスト ユーザー アカウントはサインインがブロックされ、その後で削除されます。 アクセス パッケージの割り当てがない場合でも、ゲスト ユーザーがディレクトリ内に無期限に残るようにしたい場合は、エンタイトルメント管理構成の設定を変更できます。 ゲスト ユーザー オブジェクトの詳細については、「[Azure Active Directory B2B コラボレーション ユーザーのプロパティ](../external-identities/user-properties.md)」を参照してください。

ディレクトリ内にいないユーザーがこのアクセス パッケージを要求できるようにする場合は、次の手順に従います。

1. **[アクセス権を要求できるユーザー]** セクションで、 **[For users in your directory]\(ディレクトリ内のユーザー\)** をクリックします。

    このオプションを選択すると、新しいオプションが表示されます。

    ![アクセス パッケージ - 要求 - ディレクトリ内にいないユーザー](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. 以下のオプションの 1 つを選択します。

    |  |  |
    | --- | --- |
    | **Specific connected organizations (特定の接続済み組織)** | 管理者が以前に追加した組織の一覧から選択する場合は、このオプションを選択します。 選択された組織のすべてのユーザーは、このアクセス パッケージを要求できます。 |
    | **構成済みの接続されたすべての組織** | 構成済みのすべての接続済み組織のすべてのユーザーがこのアクセス パッケージを要求できるようにする場合は、このオプションを選択します。 構成済みのすべての組織のユーザーに公開されるアクセス パッケージを要求できるのは、構成済みの接続された組織のユーザーのみです。 |
    | **すべてのユーザー (すべての接続済み組織 + 新しい外部ユーザー)** | インターネット上のすべてのメンバー ユーザーがこのアクセス パッケージを要求できるようにする場合は、このオプションを選択します。  それらのユーザーがディレクトリ内の接続された組織に属していない場合は、パッケージを要求すると、接続された組織が自動的に作成されます。 自動的に作成された接続組織は、**提案された** 状態になります。 提案された状態の詳細については、「[接続された組織の状態プロパティ](entitlement-management-organization.md#state-properties-of-connected-organizations)」を参照してください。 |

    接続されている組織とは、ご自身と関係のある外部 Azure AD ディレクトリまたはドメインです。

1. **[Specific connected organizations]\(特定の接続済み組織\)** を選択した場合は、 **[Add directories]\(ディレクトリの追加\)** をクリックして、管理者が以前に追加した、接続済み組織の一覧から選択します。

1. 以前に接続されていた組織を検索するには、名前またはドメイン名を入力します。

    ![アクセス パッケージ - 要求 - ディレクトリの選択](./media/entitlement-management-access-package-request-policy/select-directories.png)

    共同作業したい組織が一覧にない場合、接続済み組織としてそれを追加することを管理者に依頼することができます。 詳細については、[接続されている組織の追加](entitlement-management-organization.md)に関する記事を参照してください。

1. すべての接続済み組織を選択したら、 **[選択]** をクリックします。

    > [!NOTE]
    > 選択した接続済み組織のすべてのユーザーは、このアクセス パッケージを要求できます。 これには、組織に関連付けられている Azure AD 内のすべてのサブドメインのユーザーが含まれます。ただし、それらのドメインが Azure B2B の許可リストまたは拒否リストによってブロックされている場合を除きます。 詳細については、「[B2B ユーザーに対する特定組織からの招待を許可またはブロックする](../external-identities/allow-deny-list.md)」を参照してください。

1. 承認を要求する場合は、「[Azure AD エンタイトルメント管理でアクセス パッケージの承認設定を変更する](entitlement-management-access-package-approval-policy.md)」の手順を使用して、承認設定を構成します。
 
1. [[要求の有効化]](#enable-requests) セクションに移動します。

## <a name="none-administrator-direct-assignments-only"></a>なし (管理者直接割り当てのみ)

アクセス要求をバイパスし、管理者が特定のユーザーをこのアクセス パッケージに直接割り当てることを許可する場合は、次の手順に従います。 ユーザーは、アクセス パッケージを要求する必要はありません。 ライフサイクルの設定を行うこともできますが、要求の設定はありません。

1. **[アクセス権を要求できるユーザー]** セクションで、 **[None (administrator direct assignments only)]\(なし (管理者直接割り当てのみ)\)** をクリックします。

    ![アクセス パッケージ - 要求 - なし (管理者直接割り当てのみ)](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    アクセス パッケージを作成した後は、そのアクセス パッケージに特定の内部および外部ユーザーを直接割り当てることができます。 外部ユーザーを指定する場合、ゲスト ユーザー アカウントがディレクトリ内に作成されます。 ユーザーの直接割り当てについては、[アクセス パッケージに対する割り当ての表示、追加、削除](entitlement-management-access-package-assignments.md)に関する記事を参照してください。

1. [[要求の有効化]](#enable-requests) セクションまでスキップします。


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>要求設定の既存ポリシーを開いて編集する

アクセス パッケージの要求と承認の設定を変更するには、対応するポリシーを開く必要があります。 次の手順を実行して、アクセス パッケージの要求設定を開いて編集します。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[ポリシー]** をクリックして、編集するポリシーをクリックします。

    ページの下部で [ポリシーの詳細] ウィンドウが開きます。

    ![アクセス パッケージ - [ポリシーの詳細] ウィンドウ](./media/entitlement-management-shared/policy-details.png)

1. **[編集]** をクリックしてポリシーを編集します。

    ![アクセス パッケージ - ポリシーの編集](./media/entitlement-management-shared/policy-edit.png)

1. **[要求]** タブをクリックして、要求設定を開きます。

1. 前のセクションの手順を使用して、要求設定を必要に応じて変更します。

1. [[要求の有効化]](#enable-requests) セクションに移動します。

## <a name="enable-requests"></a>要求の有効化

1. アクセス パッケージを要求ポリシー内のユーザーが要求のためにすぐに利用できるようにするには、有効トグルを **[はい]** に設定します。

    アクセス パッケージの作成が完了した後は、これをいつでも有効にすることができます。

    **[None (administrator direct assignments only)]\(なし (管理者直接割り当てのみ)\)** を選択し、有効化を **[いいえ]** に設定した場合、管理者はこのアクセス パッケージを直接割り当てることはできません。

    ![[アクセス パッケージ] - [ポリシー] - [ポリシーを有効にする] 設定](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. **[次へ]** をクリックします。

1. アクセス パッケージへのアクセスを要求するときに、追加情報を提供するように要求元に要求する場合、「[Azure AD エンタイトルメント管理でアクセス パッケージの承認および要求元情報 (プレビュー) 設定を変更する](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)」の手順を使用して、要求元情報を構成します (プレビュー)。

1. ライフサイクルの設定を構成します。

1. ポリシーを編集する場合は、**[更新]** をクリックします。 新しいポリシーを追加する場合は、**[作成]** をクリックします。

## <a name="next-steps"></a>次の手順

- [Change approval settings for an access package](entitlement-management-access-package-approval-policy.md) (アクセス パッケージの承認設定を変更する)
- [Change lifecycle settings for an access package](entitlement-management-access-package-lifecycle-policy.md) (アクセス パッケージのライフサイクル設定を変更する)
- [View requests for an access package](entitlement-management-access-package-requests.md) (アクセス パッケージの要求を表示する)
