---
title: Azure サブスクリプション管理者を追加または変更する
description: ロールベースのアクセス制御 (RBAC) を使用して Azure サブスクリプション管理者を追加または変更する方法について説明します。
author: genlin
manager: dcscontentpm
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: banders
ms.openlocfilehash: 2054fbb7d0a9f450ad487fc0f03d0af920c6cc4b
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260923"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure サブスクリプション管理者を追加または変更する


Azure リソースへのアクセスを管理するには、適切な管理者ロールが必要です。 Azure には、組み込みロールを選択できる[ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC) と呼ばれる認可システムがあります。 管理グループ、サブスクリプション、リソース グループなどのさまざまなスコープでこれらのロールを割り当てることができます。 既定では、新しい Azure サブスクリプションを作成するユーザーは、サブスクリプションへの管理アクセス権を他のユーザーに割り当てることができます。

この記事では、サブスクリプション スコープで RBAC を使用してユーザーの管理者ロールを追加または変更する方法について説明します。

RBAC を使用してリソースへのアクセスを管理することをお勧めします。 ただし、まだクラシック デプロイ モデルを使用し、[Azure サービス管理 PowerShell モジュール](https://docs.microsoft.com/powershell/module/servicemanagement/azure)を使用してクラシック リソースを管理している場合は、従来の管理者を使用する必要があります。

> [!TIP]
> クラシック リソースの管理に Azure portal だけを使用している場合は、従来の管理者を使用する必要はありません。

詳細については、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](../azure-resource-manager/resource-manager-deployment-model.md)」および「[Azure classic subscription administrators (Azure の従来のサブスクリプション管理者)](../role-based-access-control/classic-administrators.md)」を参照してください。

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>サブスクリプション管理者を割り当てる

ユーザーを Azure サブスクリプションの管理者にするには、既存の管理者が、サブスクリプションのスコープで[所有者](../role-based-access-control/built-in-roles.md#owner)ロール (RBAC ロール) を割り当てます。 これにより、他のユーザーへアクセス権を委任する権限を含め、サブスクリプションにあるすべてのリソースへのフル アクセスがユーザーに付与されます。 次の手順は、他のロールの割り当てと同じです。

サブスクリプションのアカウント管理者が不明な場合は、次の手順で確認します。

1. [Azure portal で [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)を開きます。
1. 確認するサブスクリプションを選択し、 **[設定]** を調べます。
1. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。

### <a name="to-assign-a-user-as-an-administrator"></a>ユーザーを管理者に割り当てるには

1. サブスクリプション所有者として Azure portal にサインインし、[[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開きます。

1. アクセス権を付与するサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このサブスクリプションのすべてのロールの割り当てを表示します。

    ![ロールの割り当てを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックして、 **[ロールの割り当ての追加]** ウィンドウを開きます。

    ロールを割り当てるためのアクセス許可がない場合、このオプションは無効になります。

1. **[ロール]** ドロップダウン リストで、 **[所有者]** ロールを選択します。

1. **[選択]** 一覧で、ユーザーを選択します。 一覧にユーザーが表示されない場合には、 **[選択]** ボックスに表示名とメール アドレスを入力して、ディレクトリを検索します。

    ![選択された所有者ロールを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. **[保存]** をクリックしてロールを割り当てます。

    しばらくすると、サブスクリプション スコープで、ユーザーに所有者のロールが割り当てられます。

## <a name="next-steps"></a>次の手順

* [ロールベースのアクセス制御 (RBAC) とは何か](../role-based-access-control/overview.md)
* [Azure での各種ロールについて](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [方法: Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory での管理者ロールのアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
