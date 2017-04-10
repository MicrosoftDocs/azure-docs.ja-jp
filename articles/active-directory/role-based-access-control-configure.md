---
title: "Azure Portal におけるロールベースのアクセス制御 | Microsoft Docs"
description: "Azure ポータルでのロールベースのアクセス制御を使用したアクセス管理の基本について説明します。 ロールの割り当てを使用して、リソースに対するアクセス許可を割り当てます。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c3e3e407eb6a05b208863443bb8ccf3cba701b23
ms.lasthandoff: 03/22/2017


---
# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する
> [!div class="op_single_selector"]
> * [ユーザーまたはグループによるアクセスの管理](role-based-access-control-manage-assignments.md)
> * [リソースによるアクセスの管理](role-based-access-control-configure.md)

Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用すると、職務に必要な範囲のアクセス権だけをユーザーに付与することができます。 この記事では、Azure ポータルにおける RBAC の基本的な使い方について説明しています。 RBAC を使用した高度なアクセス管理については、「 [What is Role-Based Access Control (ロールベースのアクセス制御とは)](role-based-access-control-what-is.md)」を参照してください。

## <a name="view-access"></a>アクセス許可の表示
[Azure ポータル](https://portal.azure.com)のメイン ブレードで、リソース、リソース グループ、またはサブスクリプションへのアクセス権が付与されているユーザーを確認できます。 たとえば、リソース グループのいずれかへのアクセス権があるユーザーを確認する場合は、次の手順を実行します。

1. 左側にあるナビゲーション バーで、 **[リソース グループ]** アイコンをクリックします。  
    ![リソース グループ - アイコン](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. **[リソース グループ]** ブレードからリソース グループの名前を選択します。
3. 左側のメニューから **[アクセス制御 (IAM)]** を選択します。  
4. アクセス制御ブレードに、リソース グループへのアクセス権が付与されたすべてのユーザー、グループ、およびアプリケーションが一覧表示されます。  
   
    ![Users blade - inherited vs assigned access screenshot](./media/role-based-access-control-configure/view-access.png)

アクセス権が**割り当て済み**になっているユーザーと**継承済み**になっているユーザーがいることに注目してください。 アクセス権は、リソース グループに明示的に割り当てられる場合と、親サブスクリプションへの割り当てから継承される場合があります。

> [!NOTE]
> 従来のサブスクリプションの管理者と共同管理者は、新しい RBAC モデルではサブスクリプションの所有者と見なされます。

## <a name="add-access"></a>アクセス権の追加
アクセス権は、リソース内、リソース グループ内、またはサブスクリプション内から付与します。これは、ロール割り当てのスコープになります。

1. アクセス制御ブレードで **[追加]** を選択します。  
2. **[役割を選択]** ブレードから割り当てる役割を選択します。
3. ディレクトリで、アクセス権を付与するユーザー、グループ、またはアプリケーションを選択します。 ディレクトリは、表示名、電子メール アドレス、およびオブジェクト識別子を使用して検索できます。  
   
    ![Add users blade - search screenshot](./media/role-based-access-control-configure/grant-access2.png)
4. **[OK]** を選択して割り当てを作成します。 **[ユーザーを追加中]** ポップアップに進行状況が表示されます。  
    ![[ユーザーを追加中] 進捗状況バー - スクリーンショット](./media/role-based-access-control-configure/addinguser_popup.png)

ロールの割り当てが正常に追加されると、 **[ユーザー]** ブレードに表示されます。

## <a name="remove-access"></a>アクセス権の削除
1. アクセス制御ブレードのチェック ボックスを使用して、ロールの割り当てを&1; つ以上選択します。
2. **[削除]** を選択します。  
3. アクションの確定を求めるボックスがポップアップ表示されます。 **[はい]** を選択してロールの割り当てを削除します。

継承された割り当ては削除できません。 継承された割り当てを削除する必要がある場合は、ロールの割り当てが作成されたスコープで削除する必要があります。 **[スコープ]** 列の **[継承済み]** の横に、このロールが割り当てられているリソースへのリンクが表示されています。 そこにリストされているリソースにアクセスして、ロールの割り当てを削除してください。

![Users blade - inherited access disables remove button screenshot](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>その他のアクセス管理ツール
Azure ポータル以外のツールでも Azure RBAC コマンドを使用したアクセス管理とロールの割り当てを実行できます。  次のリンクをクリックして、前提条件の詳細と、Azure RBAC コマンドの使用方法について確認できます。

* [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
* [Azure コマンド ライン インターフェイス](role-based-access-control-manage-access-azure-cli.md)
* [REST API](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>次のステップ
* [アクセス変更履歴レポートの作成](role-based-access-control-access-change-history-report.md)
* [RBAC の組み込みロール](role-based-access-built-in-roles.md)
* 独自の [Azure RBAC でカスタム ロール](role-based-access-control-custom-roles.md)


