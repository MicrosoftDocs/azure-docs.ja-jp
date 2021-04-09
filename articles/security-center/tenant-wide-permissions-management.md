---
title: Azure Security Center でテナント全体のアクセス許可を付与および要求する
description: Azure Security Center でテナント全体のアクセス許可を管理する方法について説明します
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: b3ddbdf04dc736b6f78a04dc6bb2bc484e67f70f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107218"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>テナント全体の可視性を付与および要求する

Azure Active Directory (AD) ロールが **グローバル管理者** であるユーザーは、テナント全体の責任を持つ場合がありますが、Azure Security Center でその組織全体の情報を表示するための Azure アクセス許可がありません。 Azure AD ロールの割り当てでは Azure リソースへのアクセス権が付与されないため、アクセス許可の昇格が必要です。 

> [!TIP]
> グローバル管理者ロールのアクセス許可の昇格の詳細については、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../role-based-access-control/elevate-access-global-admin.md)」を参照してください。

## <a name="grant-tenant-wide-permissions-to-yourself"></a>テナント全体のアクセス許可を自分に付与する

テナント レベルのアクセス許可を自分に割り当てるには、次の手順を実行します。

1. 組織が [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) またはその他の PIM ツールを使用してリソースへのアクセスを管理している場合は、下の手順に従い、ユーザーに対してグローバル管理者ロールが有効になっている必要があります。

1. テナントのルート管理グループに割り当てられていないグローバル管理者ユーザーとして、Security Center の **[概要]** ページを開き、バナーの **[テナント全体の可視性]** リンクを選択します。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Azure Security Center でテナントレベルのアクセス許可を有効にする":::

1. 割り当てる新しい Azure ロールを選択します。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="ユーザーに割り当てるテナントレベルのアクセス許可を定義するフォーム":::

    > [!TIP]
    > 一般に、セキュリティ管理者ロールはルート レベルでポリシーを適用するために必要であり、テナント レベルの可視性を提供するのであればセキュリティ閲覧者で十分です。 これらのロールによって付与されるアクセス許可について詳しくは、[セキュリティ管理者組み込みロールの説明](../role-based-access-control/built-in-roles.md#security-admin)または[セキュリティ閲覧者組み込みロールの説明](../role-based-access-control/built-in-roles.md#security-reader)に関するページをご覧ください。
    >
    > Security Center 固有のロールの違いについては、「[ロールと許可されているアクション](security-center-permissions.md#roles-and-allowed-actions)」の表を参照してください。

    組織全体のビューは、テナントのルート管理グループ レベルにロールを付与することで実現されます。  

1. Azure portal からログアウトした後に、もう一度ログインします。

1. 昇格されたアクセス権を取得できたら、Azure Security Center を開くか更新して、Azure AD テナントの配下にあるすべてのサブスクリプションに対する可視性が確保されていることを確認します。 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>テナント全体のアクセス許可が不十分な場合に要求する

Security Center にログインした場合に、ビューが制限されていることを示すバナーが表示されるときは、クリックスルーすることで、組織のグローバル管理者に要求を送信できます。 この要求には、自分に割り当ててもらいたいロールを含めることができます。グローバル管理者は付与するロールを決定します。 

これらの要求を受け入れるか拒否するかは、グローバル管理者が決定します。 

> [!IMPORTANT]
> 要求は 7 日ごとに 1 回だけ送信できます。

昇格されたアクセス許可をグローバル管理者に要求するには、次のようにします。

1. Azure portal から Azure Security Center を開きます。

1. "情報が制限されています。" という内容のバナーが表示される場合は、 それを選択します。

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="テナント全体のアクセス許可を要求できることをユーザーに通知するバナー。":::

1. 詳細な要求フォームで、目的のロールを選択し、これらのアクセス許可が必要である正当な理由を選択します。

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Azure グローバル管理者にテナント全体のアクセス許可を要求するための詳細ページ":::

1. **[アクセス権の要求]** を選択します。

    グローバル管理者に電子メールが送信されます。 電子メールには Security Center へのリンクが含まれ、それを使用して要求を承認または拒否することができます。

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="新しいアクセス許可についてグローバル管理者に電子メールで送信する":::

    グローバル管理者が **[要求の確認]** を選択してプロセスを完了したら、決定内容が要求元のユーザーに電子メールで送信されます。 

## <a name="next-steps"></a>次のステップ

Security Center のアクセス許可の詳細については、次の関連ページを参照してください。

- [Azure Security Center におけるアクセス許可](security-center-permissions.md)