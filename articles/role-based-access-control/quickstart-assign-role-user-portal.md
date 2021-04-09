---
title: チュートリアル:Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する - Azure RBAC
description: このチュートリアルでは、Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセス権をユーザーに付与する方法について説明します。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: fcba9cad208c2ac170f91cc06a6db22e271f2a70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559311"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する

[Azure ロールベースのアクセス制御 (Azure RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 このチュートリアルでは、ユーザーに対してリソース グループ内で仮想マシンを作成および管理するアクセス許可を付与します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リソース グループをスコープとするユーザーのアクセス権の付与
> * アクセス権の削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-resource-group"></a>リソース グループを作成する

1. ナビゲーション リストで、 **[リソース グループ]** をクリックします。

1. **[追加]** を選択して **[リソース グループ]** ブレードを開きます。

   ![新しいリソース グループの追加](./media/quickstart-assign-role-user-portal/resource-group.png)

1. **[リソース グループ名]** に **rbac-resource-group** と入力します。

1. サブスクリプションと場所を選択します。

1. **[作成]** をクリックしてリソース グループを作成します。

1. **[更新]** をクリックしてリソース グループの一覧を更新します。

   新しいリソース グループがリソース グループの一覧に表示されます。

   ![リソース グループの一覧](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>アクセス権の付与

Azure RBAC でアクセス権を付与するには、Azure ロールを割り当てます。

1. **[リソース グループ]** の一覧で、新しい **rbac-resource-group** リソース グループをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、ロールの割り当ての現在の一覧を表示します。

   ![リソース グループの [アクセス制御 (IAM)] ブレード](./media/quickstart-assign-role-user-portal/access-control.png)

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックして、[ロールの割り当ての追加] ウィンドウを開きます。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[ロールの割り当ての追加] メニュー](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ウィンドウが開きます。

   ![[ロールの割り当ての追加] ウィンドウ](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. **[ロール]** ドロップダウン リストで、 **[仮想マシン共同作成者]** を選択します。

1. **[選択]** 一覧で、自分または別のユーザーを選択します。

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、rbac-resource-group リソース グループ スコープにおける仮想マシン共同作成者ロールがユーザーに割り当てられます。

   ![仮想マシン共同作成者ロールの割り当て](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>アクセス権の削除

Azure RBAC でアクセス権を削除するには、ロールの割り当てを削除します。

1. ロールの割り当ての一覧で、仮想マシン共同作成者ロールを持つユーザーの横にチェック マークを追加します。

1. **[削除]** をクリックします。

   ![ロールの割り当ての削除メッセージ](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. ロールの割り当ての削除メッセージが表示されたら、 **[はい]** をクリックします。

## <a name="clean-up"></a>クリーンアップ

1. ナビゲーション リストで、 **[リソース グループ]** をクリックします。

1. **rbac-resource-group** をクリックしてリソース グループを開きます。

1. **[リソース グループの削除]** をクリックしてリソース グループを削除します。

   ![リソース グループの削除](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. **[削除しますか]** ブレードにリソース グループ名 **rbac-resource-group** を入力します。

1. **[削除する]** をクリックしてリソース グループを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をユーザーに付与する](tutorial-role-assignments-user-powershell.md)
