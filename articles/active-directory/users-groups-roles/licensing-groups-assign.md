---
title: グループにライセンスを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure Active Directory のグループへのライセンスによってユーザーにライセンスを割り当てる方法
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900837"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーシップによるユーザーへのライセンスの割り当て

この記事では、製品のライセンスをユーザーのグループに割り当てて、その後、Azure Active Directory (Azure AD) 内で正しくライセンスされていることを確認する手順を説明します。

この例では、テナントには**人事部**というセキュリティ グループが含まれています。 このグループには、人事部のすべてのメンバーが含まれます (約 1,000 人)。 Office 365 Enterprise E3 ライセンスを部署全体に割り当てる必要があります。 製品に含まれている Yammer Enterprise サービスは、部署で使用開始の準備が整うまで、一時的に無効する必要があります。 同じユーザー グループに、Enterprise Mobility + Security ライセンスをデプロイする必要もあります。

> [!NOTE]
> Microsoft サービスの中には、すべての場所では利用できないものもあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの [利用場所] プロパティを指定しておく必要があります。
>
> グループ ライセンスの割り当ての際、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。 ユーザーが複数の場所にいる場合は、Azure AD でのユーザー作成フローの一環として利用場所を常に設定することをお勧めします (AAD Connect 構成など)。これにより、ライセンス割り当ての結果が常に正しいことが保証され、ユーザーは許可されていない場所でサービスを受けられなくなります。

## <a name="step-1-assign-the-required-licenses"></a>手順 1:必要なライセンスを割り当てる

1. ユーザー管理者アカウントを使用して [**Azure AD 管理センター**](https://aad.portal.azure.com)にサインインします。 ライセンスを管理するアカウントは、ライセンス管理者、ユーザー アカウント管理者、またはグローバル管理者である必要があります。

1. **[ライセンス]** を選択して、ライセンスを付与できるテナント内のすべての製品を確認および管理できるページを開きます。

1. **[すべての製品]** で、製品名を選択して Office 365 Enterprise E5 と Enterprise Mobility + Security E3 の両方を選択します。 割り当てを開始するには、ページの上部にある **[割り当て]** を選択します。

   ![ライセンスを割り当てる製品を選択します](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. **[ライセンスの割り当て]** ページで **[ユーザーとグループ]** を選択して、ユーザーとグループの一覧を開きます。

1. ユーザーまたはグループを選択し、ページ下部の **[選択]** を使用して選択を確定します。

1. **[ライセンスの割り当て]** ページで **[割り当てオプション]** をクリックすると、先ほど選択した 2 つの製品に含まれているすべてのサービス プランが表示されます。 **Yammer Enterprise** を探して **[オフ]** に設定し、製品ライセンスのこのサービスを無効にします。 **[ライセンス オプション]** の下部にある **[OK]** をクリックして確定します。

   ![ライセンスのサービス プランを選択します](./media/licensing-groups-assign/assignment-options.png)
  
1. 割り当てを完了するには、 **[ライセンスの割り当て]** ページで、ページの下部にある **[割り当て]** をクリックします。

1. 右上隅に通知が表示され、プロセスの状態と結果が示されます。 (たとえば、グループ内の既存のライセンスが原因で) グループへの割り当てを完了できなかった場合は、通知をクリックして失敗の詳細を確認します。

グループにライセンスを割り当てると、Azure AD によりそのグループのすべての既存メンバーが処理されます。 グループのサイズにより、この処理には時間がかかる場合があります。 次の手順では、プロセスの終了を確認する方法と、問題の解決にさらなる注意が必要かどうかを確認する方法について説明します。

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>手順 2:最初の割り当てが終了したことを確認する

1. **[Azure Active Directory]**  >  **[グループ]** の順に移動します。 ライセンスが割り当てられていたグループを選択します。

1. そのグループのページで、 **[ライセンス]** を選択します。 これで、ライセンスがユーザーに完全に割り当てられていることと、調査が必要なエラーの有無をすばやく確認できます。 利用可能な情報は以下のとおりです。

   - グループに現在割り当てられているサービス ライセンス。 エントリを選択すると、有効にした特定のサービスを表示して変更を行えます。

   - 最新のライセンス変更の状態の更新 (変更が処理中の場合、またはすべてのユーザー メンバーの処理が完了している場合に利用できます)。

   - エラー状態にあるユーザー ライセンスの割り当てに関する情報。

   ![ライセンス エラーとライセンスの状態](./media/licensing-groups-assign/assignment-errors.png)

1. ライセンスの処理に関する詳細情報は、 **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  "*グループ名*"  >  **[監査ログ]** の順に移動して入手できます。 次のアクティビティに注意してください。

   - アクティビティ: `Start applying group based license to users`。 このログは、システムによってグループでのライセンス割り当ての変更が検出され、すべてのユーザー メンバーに対するその変更の適用が開始されたときに、記録されます。 ここには、行われた変更の情報が含まれます。

   - アクティビティ: `Finish applying group based license to users`。 このログは、システムによるグループ内のユーザー全員の処理が完了したときに記録されます。 ここには、処理が正常に完了したユーザーの数とグループ ライセンスを割り当てることができなかったユーザーの数の概要が含まれます。

   [このセクション](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity)では、グループ ベースのライセンスによって行われた変更を、監査ログを使用して分析する方法を説明しています。

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>手順 3:ライセンスに関する問題をチェックして解決する

1. **[Azure Active Directory]**  >  **[グループ]** の順に移動して、ライセンスが割り当てられていたグループを見つけます。
1. そのグループのページで、 **[ライセンス]** を選択します。 ページの上部の通知で、ライセンスを割り当てられなかったユーザーが 10 人いることが示されます。 それを開くと、このグループでライセンスがエラー状態である全ユーザーの一覧が表示されます。
1. **[割り当ての失敗数]** 列から、どちらの製品ライセンスもユーザーに割り当てられなかったことがわかります。 **[エラーの最も大きな原因]** 列には、エラーの原因が記載されています。 この例では **[競合するサービス プラン]** です。

   ![割り当てることができなかったライセンス](./media/licensing-groups-assign/failed-assignments.png)

1. ユーザーの **[ライセンス]** ページを開くユーザーを選択します。 このページには、ユーザーに現在割り当てられているすべてのライセンスが表示されます。 この例では、このユーザーは**キオスク ユーザー** グループから継承された Office 365 Enterprise E1 のライセンスを持っています。 これは、システムが**人事部**グループから適用しようとした E3 ライセンスと競合しています。 結果的にこのユーザーには、そのグループからどのライセンスも割り当てられていません。

   ![ユーザーのすべてのライセンスの競合を表示します](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. この競合を解消するには、**キオスク ユーザー** グループからこのユーザーを削除します。 Azure AD によって変更が処理された後、**人事部**のライセンスは正常に割り当てられます。

## <a name="next-steps"></a>次の手順

グループを使用したライセンス割り当ての機能セットについては、以下の記事をご覧ください。

- [What is group-based licensing in Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context) (Azure Active Directory のグループベースのライセンスとは)
- [Azure Active Directory のグループのライセンスに関する問題の特定と解決](licensing-groups-resolve-problems.md)
- [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](licensing-groups-migrate-users.md)
- [Azure Active Directory のグループベースのライセンスを使用して製品ライセンス間でユーザーを移行する方法](licensing-groups-change-licenses.md)
- [Azure Active Directory グループベース ライセンスのその他のシナリオ](../active-directory-licensing-group-advanced.md)
- [Azure Active Directory のグループベースのライセンスの PowerShell の例](licensing-ps-examples.md)
