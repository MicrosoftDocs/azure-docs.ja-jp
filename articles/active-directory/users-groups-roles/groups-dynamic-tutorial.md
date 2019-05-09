---
title: 動的グループ メンバーシップによるユーザーの追加と削除 - チュートリアル - Azure Active Directory
description: このチュートリアルでは、ユーザー メンバーシップの規則を含んだグループを使って、ユーザーを自動的に追加したり削除したりします。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93b59a108d5d87479c12174e97713d4c12d84f2e
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200057"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>チュートリアル:グループ メンバーを自動的に追加/削除する

Azure Active Directory (Azure AD) では、セキュリティ グループまたは Office 365 グループのユーザーを自動的に追加したり削除したりできるので、この作業を必ずしも手動で行う必要はありません。 ユーザーまたはデバイスのいずれかのプロパティが変更されると、Azure AD はテナント内のすべての動的グループ規則を評価し、その変更によってメンバーの追加または削除が必要であるかどうかを確認します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * パートナー企業のゲスト ユーザーが自動的に追加されるグループを作成します。
> * パートナー固有の機能にゲスト ユーザーがアクセスするためのライセンスをグループに割り当てます。
> * 補足: ゲスト ユーザーを削除して **[すべてのユーザー]** グループのセキュリティを確保する (社内専用サイトへのアクセス権をメンバー ユーザーに与える場合など)。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この機能を使うには、テナントのグローバル管理者に Azure AD Premium ライセンスが 1 つ必要となります。 ライセンスがない場合は、Azure AD の **[ライセンス]** > **[製品]** > **[試用/購入]** を選択してください。

ユーザーを動的グループのメンバーにするために、そのユーザーにライセンスを割り当てる必要はありません。 その対象となるすべてのユーザーをカバーできるだけの Azure AD Premium P1 ライセンス数がテナントにあれば済みます。 

## <a name="create-a-group-of-guest-users"></a>ゲスト ユーザーのグループを作成する

まず、全員が単一のパートナー企業に所属するゲスト ユーザーのグループを作成します。 これらのユーザーには特別なライセンスが必要であるため、通常は、そのためのグループを作成した方が効率的です。

1. テナントのグローバル管理者アカウントで Azure portal (https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** > **[グループ]** > **[新しいグループ]** の順に選択します。
   ![新しいグループを開始するためのコマンドを選択](./media/groups-dynamic-tutorial/new-group.png)
3. **[グループ]** ブレードで次の操作を行います。
  
   * グループの種類として **[セキュリティ]** を選択します。
   * グループの名前と説明として「`Guest users Contoso`」を入力します。
   * **[メンバーシップの種類]** を **[動的ユーザー]** に変更します。
   * **[動的クエリの追加]** を選択します。
  
4. **[高度なルール]** を選択し、**[高度なルール]** ボックスに次のように入力します。`(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`
5. **[クエリの追加]** を選択してブレードを閉じます。
6. **[グループ]** ブレードで **[作成]** を選択して、グループを作成します。

## <a name="assign-licenses"></a>ライセンスを割り当てる

新しいグループが作成されたら、パートナー ユーザーに必要なライセンスを適用することができます。

1. Azure AD で **[ライセンス]** を選択し、少なくとも 1 つライセンスを選択して、**[割り当て]** を選択します。
2. **[ユーザーとグループ]** を選択し、**[Guest users Contoso]** グループを選択して、変更内容を保存します。
3. 選択したライセンスに含まれているサービス プランの有効と無効を **[割り当てオプション]** で切り替えることができます。 変更を加えたら、必ず **[OK]** をクリックして、変更内容を保存してください。
4. 割り当てを完了するには、**[ライセンスの割り当て]** ウィンドウで、ウィンドウの下部にある **[割り当て]** をクリックします。

## <a name="remove-guests-from-all-users-group"></a>"すべてのユーザー" グループからゲストを削除する

最終的な管理の計画は、すべてのゲスト ユーザーを各自の会社ごとのグループに割り当てることでしょう。 そこで、自分のテナント内のメンバー ユーザーのみが対象となるように、**すべてのユーザー** グループを変更することもできます。 そのうえで、ホーム組織に固有のアプリとライセンスを割り当てることができます。

   !["すべてのユーザー" グループをメンバー専用に変更](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

**ゲスト ユーザー グループを削除するには**

1. テナントの全体管理者アカウントで [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** > **[グループ]** を選択します。 **[Guest users Contoso]** グループを選択し、省略記号 (...) を選択して、**[削除]** を選択します。 グループを削除すると、割り当てられているライセンスはすべて削除されます。

**"すべてのユーザー" グループを復元するには**
1. **[Azure Active Directory]** > **[グループ]** を選択します。 **すべてのユーザー** グループの名前を選択して、そのグループを開きます。
1. **[動的メンバーシップ ルール]** を選択し、ルール内のテキストをすべて消去して、**[保存]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
> * ゲスト ユーザーのグループを作成する
> * 新しいグループにライセンスを割り当てる
> * "すべてのユーザー" グループをメンバー専用に変更

次の記事に進み、グループベースのライセンスの基礎について詳しく学習してください。
> [!div class="nextstepaction"]
> [グループ ライセンスの基礎](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



