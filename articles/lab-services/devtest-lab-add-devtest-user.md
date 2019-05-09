---
title: Azure DevTest Labs での所有者とユーザーの追加 | Microsoft Docs
description: Azure Portal または PowerShell を使用した Azure DevTest Labs での所有者とユーザーの追加
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727963"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Azure DevTest Labs での所有者とユーザーの追加
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Azure DevTest Labs へのアクセスは、 [Azure のロール ベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md)で制御します。 RBAC を使用して、チーム内で職務を *ロール* に分離し、職務に必要なアクセス許可のみをユーザーに付与します。 RBAC ロールは、"*所有者*"、"*DevTest Labs ユーザー*"、および "*共同作成者*" の 3 つです。 この記事では、これら 3 つの主な RBAC ロールそれぞれで実行できるアクションについて説明します。 ポータルを経由して、および PowerShell スクリプトを使用してラボにユーザーを追加する方法、およびサブスクリプション レベルでユーザーを追加する方法について説明します。

## <a name="actions-that-can-be-performed-in-each-role"></a>各ロールで実行できるアクション
次の 3 つの主なロールをユーザーを割り当てることができます。

* Owner
* DevTest Labs User
* の共同作成者

次の表は、これらの各ロールのユーザーが実行できるアクションを示しています。

| **このロールのユーザーが実行できるアクション** | **DevTest Labs ユーザー** | **所有者** | **共同作成者** |
| --- | --- | --- | --- |
| **ラボのタスク** | | | |
| ラボへのユーザーの追加 |いいえ  |はい |いいえ  |
| コストの設定の更新 |いいえ  |可能  |はい |
| **VM ベースのタスク** | | | |
| カスタム イメージの追加と削除 |いいえ  |可能  |はい |
| 数式の追加、更新、および削除 |はい |はい |はい |
| Azure Marketplace のイメージのホワイトリスト |いいえ  |可能  |はい |
| **VM タスク** | | | |
| VM の作成 |はい |はい |はい |
| VM の開始、停止、および削除 |ユーザーによって作成された VM のみ |はい |はい |
| VM のポリシーの更新 |いいえ  |可能  |はい |
| データ ディスクの VM への追加と VM からの削除 |ユーザーによって作成された VM のみ |はい |はい |
| **アーティファクトのタスク** | | | |
| アーティファクトのリポジトリの追加と削除 |いいえ  |可能  |はい |
| アーティファクトの適用 |はい |はい |はい |

> [!NOTE]
> ユーザーが VM を作成すると、そのユーザーは作成された VM の **所有者** ロールに自動的に割り当てられます。
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>ラボ レベルでの所有者またはユーザーの追加
所有者とユーザーは Azure Portal 経由で、ラボレベルで追加できます。 ユーザーには、有効な [Microsoft アカウント (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)を持つ外部ユーザーも可能です。
次のステップでは、Azure DevTest Labs で所有者またはユーザーをラボに追加するプロセスについて説明します。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。
4. ラボのブレードで、**[構成とポリシー]** を選択します。 
5. **[構成とポリシー]** ページで、**[アクセス制御 (IAM)]** を左側のメニューから選択します。 
6. ツールバーで **[ロールの割り当ての追加]** を選択し、ロールにユーザーを追加します。
1. **[アクセス許可の追加]** ウィンドウで、次のようにします。 
    1. ロールを選択します (例:DevTest ラボ ユーザー)。 セクション「 [各ロールで実行できるアクション](#actions-that-can-be-performed-in-each-role) 」では、所有者、DevTest ユーザーおよび共同作成者ロールのユーザーが実行できるさまざまなアクションが一覧表示されます。
    2. ロールに追加するユーザーを選択します。 
    3. **[保存]** を選択します。 
11. **[ユーザー]** ブレードに戻ると、このユーザーが追加されています。  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>PowerShell を使用して、ラボに外部ユーザーを追加します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Portal にユーザーを追加するだけでなく、PowerShell スクリプトを使用して、ラボに外部ユーザーを追加することもできます。 次の例で、**Values to change** コメントの下のパラメーター値を変更してください。
`subscriptionId`、`labResourceGroup`、および `labName` の値は、Azure Portal のラボのブレードから取得することができます。

> [!NOTE]
> サンプル スクリプトでは、指定したユーザーが Active Directory にゲストとして追加されていると想定しており、そうではない場合はスクリプトが失敗します。 Active Directory にないユーザーをラボに追加するには、セクション「 [ラボ レベルでの所有者またはユーザーの追加](#add-an-owner-or-user-at-the-lab-level)」で示されているように、Azure Portal を使用してユーザーをロールに割り当てます。   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>サブスクリプション レベルでの所有者またはユーザーの追加
Azure のアクセス許可は、Azure で親スコープから子スコープに反映されます。 したがって、ラボを含む Azure サブスクリプションの所有者は、自動的にこれらのラボの所有者となります。 また、これらの所有者は、VM やラボのユーザーによって作成されたその他のリソース、および Azure DevTest Labs サービスも所有します。 

[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)でラボのブレードを使用してラボに他の所有者を追加できます。 ただし、追加された所有者の管理のスコープは、サブスクリプションの所有者のスコープよりも狭くなります。 たとえば、追加された所有者には、DevTest Labs サービスによって、サブスクリプションで作成されたリソースの一部へのフル アクセスがありません。 

Azure サブスクリプションに所有者を追加するには、次の手順を実行します。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧から **[サブスクリプション]** を選択します。
3. 目的のサブスクリプションを選択します。
4. **[アクセス]** アイコンを選択します。 
   
    ![Access users](./media/devtest-lab-add-devtest-user/access-users.png)
5. **[ユーザー]** ブレードで、**[追加]** を選択します。
   
    ![ユーザーの追加](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. **[Select a role (役割の選択)]** ブレードで、**[所有者]** を選択します。
7. **[ユーザーの追加]** ブレードで、所有者として追加するユーザーの電子メール アドレスまたは名前を入力します。 ユーザーが見つからない場合は、問題を示すエラー メッセージが表示されます。 ユーザーが見つかった場合は、 **[ユーザー]** ボックスの下に表示されます。
8. 見つかったユーザー名を選択します。
9. **[選択]** を選択します。
10. **[OK]** を選択して、**[Add access (アクセスの追加)]** ブレードを閉じます。
11. **[ユーザー]** ブレードに戻ると、このユーザーが所有者として追加されています。 これで、このユーザーはこのサブスクリプションで作成されたすべてのラボの所有者となり、所有者タスクを実行できるようになります。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

