---
title: Azure Security Center に対するテナント全体の可視性を確保する | Microsoft Docs
description: この記事では、Azure Active Directory テナントにリンクされているすべてのサブスクリプションにポリシーを適用することで、大規模なセキュリティ体制を管理する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2020
ms.author: memildin
ms.openlocfilehash: 5b257e45a86a7b22e9064fcfc6092b3c946ae99b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757647"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>管理グループ、サブスクリプション、およびテナント全体の可視性を整理する

この記事では、Azure Active Directory テナントにリンクされているすべての Azure サブスクリプションにセキュリティ ポリシーを適用することで、組織の大規模なセキュリティ体制を管理する方法について説明します。

Azure AD テナントに登録されているすべてのサブスクリプションのセキュリティ対策を把握するには、十分な読み取りアクセス許可を持つ Azure ロールをルート管理グループに割り当てる必要があります。


## <a name="organize-your-subscriptions-into-management-groups"></a>サブスクリプションを管理グループに整理する

### <a name="introduction-to-management-groups"></a>管理グループの概要

Azure の管理グループを使用すると、一連のサブスクリプションに対するアクセス、ポリシー、およびレポートを効率的に管理できると共に、ルート管理グループに対してアクションを実行することで、Azure 資産全体を効果的に管理することができます。 管理者は、管理グループ内にサブスクリプションを整理し、それらの管理グループに管理ポリシーを適用できます。 管理グループ内のすべてのサブスクリプションは、管理グループに適用されたポリシーを自動的に継承します。 

各 Azure AD テナントには、**ルート管理グループ** と呼ばれる 1 つの最上位管理グループがあります。 このルート管理グループは階層に組み込まれており、すべての管理グループとサブスクリプションはルート管理グループにまとめられます。 このグループにより、グローバル ポリシーと Azure ロールの割り当てをディレクトリ レベルで適用できます。 

ルート管理グループは、次のいずれかのアクションを実行したときに自動的に作成されます。 
- **管理グループ** を [Azure portal](https://portal.azure.com) で表示する。
- API 呼び出しを使用して管理グループを作成する。
- PowerShell を使用して管理グループを作成する。 PowerShell を使用する場合の手順については、「[リソースの整理と管理のための管理グループを作成する](../governance/management-groups/create-management-group-portal.md)」を参照してください。

管理グループは Security Center をオンボードするのに必須のものではありませんが、管理グループを少なくとも 1 つ作成して、ルート管理グループが作成されるようにすることをお勧めします。 グループが作成されると、自分の Azure AD テナントの配下にあるすべてのサブスクリプションがそのグループにリンクされます。 


管理グループの概要について詳しくは、「[Organize your resources with Azure management groups](../governance/management-groups/overview.md)」(Azure 管理グループでリソースを整理する) の記事をご覧ください。

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Azure portal で管理グループを表示および作成する

1. [Azure portal](https://portal.azure.com) で上部のバーの検索ボックスを使用し、 **[管理グループ]** を検索して開きます。

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="管理グループへのアクセス":::

    管理グループの一覧が表示されます。

1. 管理グループを作成するには、 **[管理グループの追加]** を選択し、関連する詳細情報を入力して、 **[保存]** を選択します。

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Azure への管理グループの追加":::

    - **[管理グループ ID]** は、この管理グループでコマンドを送信するために使用するディレクトリの一意識別子です。 この識別子は、このグループを識別するために Azure システム全体で使用されるため、作成後は編集できません。 
    - 表示名フィールドは、Azure Portal 内で表示される名前です。 管理グループの作成時には別の表示名は省略可能なフィールドで、いつでも変更できます。  


### <a name="add-subscriptions-to-a-management-group"></a>管理グループにサブスクリプションを追加する
管理者は、自分が作成した管理グループにサブスクリプションを追加できます。

1. **[管理グループ]** で、サブスクリプションの管理グループを選択します。

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="サブスクリプションの管理グループを選択する":::

1. グループのページが開いたら、 **[詳細]** を選択します。

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="管理グループの詳細ページを開く":::

1. グループの詳細ページで **[サブスクリプションの追加]** を選択し、サブスクリプションを選択して **[保存]** を選択します。 スコープ内のすべてのサブスクリプションを追加するまで、この手順を繰り返します。

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="管理グループにサブスクリプションを追加する":::
   > [!IMPORTANT]
   > 管理グループには、サブスクリプションと子管理グループの両方を含めることができます。 親管理グループに対する Azure ロールをユーザーに割り当てると、そのアクセス権は子管理グループのサブスクリプションによって継承されます。 親管理グループで設定されたポリシーは、子にも継承されます。 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>テナント全体のアクセス許可を自分に付与する

Azure Active Directory ロールが **グローバル管理者** であるユーザーは、テナント全体の責任を持つ場合がありますが、Azure Security Center でその組織全体の情報を表示するための Azure アクセス許可がありません。 

> [!TIP]
> 組織が [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) またはその他の PIM ツールを使用してリソースへのアクセスを管理している場合は、これらの変更を行うユーザーに対して、グローバル管理者ロールが有効になっている必要があります。

テナント レベルのアクセス許可を自分に割り当てるには、次の手順を実行します。

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

## <a name="assign-azure-roles-to-other-users"></a>Azure ロールを他のユーザーに割り当てる

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Azure portal を使用してユーザーに Azure ロールを割り当てる: 
1. [Azure portal](https://portal.azure.com) にサインインします。 
1. 管理グループを表示するには、Azure メイン メニューの **[すべてのサービス]** を選択してから、 **[管理グループ]** を選択します。
1.  管理グループを選択し、 **[詳細]** を選択します。

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="管理グループの詳細のスクリーンショット":::

1. **[アクセス制御 (IAM)]** 、 **[ロールの割り当て]** の順に選択します。
1. **[ロールの割り当ての追加]** を選択します。
1. 割り当てるロールとユーザーを選択し、 **[保存]** を選択します。  
   
   ![セキュリティ閲覧者ロールの追加のスクリーンショット](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>PowerShell を使用してユーザーに Azure ロールを割り当てる: 
1. [Azure PowerShell](/powershell/azure/install-az-ps)をインストールします。
2. 次のコマンドを実行します。 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. メッセージが表示されたら、グローバル管理者の資格情報でサインインします。 

    ![サインイン プロンプトのスクリーンショット](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. 次のコマンドを実行して、閲覧者ロールのアクセス許可を付与します。

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. ロールを削除するには、次のコマンドを使用します。 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>昇格されたアクセス権を削除する 

Azure ロールがユーザーに割り当てられたら、テナント管理者は自身をユーザー アクセス管理者ロールから削除する必要があります。

1. [Azure Portal](https://portal.azure.com) または [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。

2. ナビゲーション一覧で **Azure Active Directory** を選択し､ **[プロパティ]** を選択します｡

3. **Azure リソースのアクセス管理** の下で、スイッチを **[いいえ]** に設定します。

4. 設定を保存するには、 **[保存]** を選択します。



## <a name="next-steps"></a>次のステップ
この記事では、Azure Security Center に対するテナント全体の可視性を確保する方法について学習しました。 関連情報については、以下をご覧ください。

- [Azure Security Center におけるアクセス許可](security-center-permissions.md)