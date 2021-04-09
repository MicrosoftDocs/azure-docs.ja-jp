---
title: Azure AD エンタイトルメント管理でアクセス パッケージの割り当てを表示、追加、削除する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理でアクセス パッケージの割り当てを表示、追加、削除する方法を説明します。
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
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bc7ca9e108a35fd27f7b3155f186221a8caae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980061"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージの割り当てを表示、追加、および削除する

Azure AD エンタイトルメント管理では、アクセス パッケージを割り当てたユーザー、そのポリシー、および状態を確認できます。 アクセス パッケージに適切なポリシーが設定されている場合は、ユーザーをアクセス パッケージに直接割り当てることもできます。 この記事では、アクセス パッケージの割り当てを表示、追加、および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure AD エンタイトルメント管理を使用し、ユーザーをアクセス パッケージに割り当てるには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 ライセンス

## <a name="view-who-has-an-assignment"></a>割り当てられているユーザーを表示する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[割り当て]** をクリックして、アクティブな割り当ての一覧を表示します。

    ![アクセス パッケージへの割り当ての一覧](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 特定の割り当てをクリックすると、その他の詳細が表示されます。

1. 必ずしもすべてのリソース ロールが適切にプロビジョニングされなかった割り当ての一覧を表示するには、フィルターの状態をクリックし、 **[Delivering]\(配信中)** を選択します。

    **[要求]** ページでユーザーの対応する要求を見つけることで、配信エラーのその他の詳細を確認できます。

1. 有効期限切れの割り当てを確認するには、フィルターの状態をクリックし、 **[Expired]\(有効期限切れ)** を選択します。

1. フィルター処理された一覧の CSV ファイルをダウンロードするには、 **[ダウンロード]** をクリックします。

### <a name="viewing-assignments-programmatically"></a>プログラムによる割り当ての表示

Microsoft Graph を使用して、アクセス パッケージの割り当てを取得することもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageAssignments をリストする](/graph/api/accesspackageassignment-list?view=graph-rest-beta)ことができます。

## <a name="directly-assign-a-user"></a>ユーザーを直接割り当てる

場合によっては、ユーザーがアクセス パッケージを要求するプロセスをたどらなくて済むように、アクセス パッケージに特定のユーザーを直接割り当てることもできます。 ユーザーを直接割り当てるには、アクセス パッケージに、管理者に直接割り当てを許可するポリシーが必要です。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[割り当て]** をクリックします。

1. **[新しい割り当て]** をクリックして、[Add user to access package]\(アクセス パッケージにユーザーを追加する) を開きます。

    ![割り当て - アクセス パッケージにユーザーを追加する](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. **[ユーザーの追加]** をクリックして、アクセス パッケージを割り当てるユーザーを選択します。

1. ユーザーの今後の要求とライフサイクルを統制、追跡するためのポリシーを **[ポリシーの選択]** リストで選択します。 選択したユーザーに異なるポリシー設定を割り当てたい場合は、 **[新しいポリシーの作成]** をクリックして新しいポリシーを追加します。

1. 選択したユーザーの割り当てを開始および終了する日付と時刻を設定します。 終了日が指定されていない場合は、ポリシーのライフサイクルの設定が使用されます。

1. 必要に応じて、記録保存のために直接割り当ての妥当性を入力します。

1. **[追加]** をクリックして、選択したユーザーをアクセス パッケージに直接割り当てます。

    しばらくしてから **[更新]** をクリックすると、割り当ての一覧にユーザーが表示されます。

### <a name="directly-assigning-users-programmatically"></a>プログラムによるユーザーの直接割り当て

Microsoft Graph を使用して、アクセス パッケージにユーザーを直接割り当てることもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageAssignmentRequest を作成する](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)ことができます。

## <a name="remove-an-assignment"></a>割り当ての削除

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[割り当て]** をクリックします。
 
1. 割り当てをアクセス パッケージから削除するユーザーの横にあるチェック ボックスをオンにします。 

1. 左ウィンドウの上部付近にある **[削除]** ボタンをクリックします。 
 
    ![割り当て - アクセス パッケージからユーザーを削除する](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    割り当てが削除されたことを知らせる通知が表示されます。 

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージの要求と設定を変更する](entitlement-management-access-package-request-policy.md)
- [レポートとログを表示する](entitlement-management-reports.md)