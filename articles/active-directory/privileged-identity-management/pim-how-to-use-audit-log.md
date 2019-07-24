---
title: PIM で Azure AD ロールの監査履歴を表示する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの監査履歴を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c080173af8ddd31b077bb820ea19d82eb2b29300
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576149"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールの監査履歴を表示する
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) の監査履歴を使用すると、すべての特権ロールでの特定の期間におけるすべてのユーザー割り当てとアクティブ化を確認できます。 管理者、エンド ユーザー、同期アクティビティを含むテナントのアクティビティの完全な監査履歴を確認するには、 [Azure Active Directory のアクセスおよび使用状況レポート](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>監査履歴に移動する
[Azure Portal](https://portal.azure.com) のダッシュボードで、 **Azure AD Privileged Identity Management** アプリを選択します。 次に、PIM ダッシュボードで **[特権ロールの管理]** > **[監査履歴]** の順にクリックして、監査履歴にアクセスします。

![監査履歴](media/azure-ad-pim-approval-workflow/image021.png)

> [!NOTE]
> [アクション] でデータを並べ替え、[アクティブ化 - 承認済み] を探します。


## <a name="audit-history-graph"></a>監査履歴グラフ
監査履歴を使用すると、合計アクティブ化数、1 日あたりの最大アクティブ化数、1 日あたりの平均アクティブ化数を折れ線グラフで表示できます。  監査履歴に複数のロールがある場合は、ロールによってデータをフィルターすることもできます。

**[時間]**、**[アクション]**、および **[ロール]** の各ボタンを使用して、履歴を並べ替えることができます。

## <a name="audit-history-list"></a>監査履歴一覧
監査履歴一覧の列は次のとおりです。

* **要求元** - ロールのアクティブ化または変更を要求したユーザー。  値が "Azure システム" の場合は、Azure 監査履歴で詳細を確認できます。
* **ユーザー** - ロールをアクティブ化しているユーザー、またはロールに割り当てられているユーザー。
* **ロール** - ユーザーによって割り当てられたかアクティブ化されたロール。
* **アクション** - 要求元によって実行されたアクション。 これには、割り当て、割り当て解除、アクティブ化、非アクティブ化などがあります。
* **時間** - アクションが発生した時刻。
* **理由** - アクティブ化の間に理由フィールドに入力されたテキスト。
* **有効期限** - ロールのアクティブ化のみに関連します。

## <a name="filter-audit-history"></a>監査履歴をフィルター処理する
**[フィルター]** をクリックすると、監査履歴に表示される情報を絞り込むことができます。  **[グラフ パラメーターの更新]** ブレードが表示されます。

フィルターを設定した後、**[更新]** をクリックすると、履歴のデータがフィルター処理されます。  データがすぐに表示されない場合は、ページを更新してください。

### <a name="change-the-date-range"></a>日付範囲を変更する
**[今日]**、**[過去 1 週間]**、**[過去 1 か月]**、**[カスタム]** のいずれかのボタンを使用して、監査履歴の時間範囲を変更することができます。

**[カスタム]** をクリックすると、**[開始]** および **[終了]** 日付フィールドが表示され、履歴の日付範囲を指定できます。  MM/DD/YYYY の形式で日付を入力するか、 **カレンダー** アイコンをクリックしてカレンダーから日付を選択できます。

### <a name="change-the-roles-included-in-the-history"></a>履歴に含まれるロールを変更する
ロールを履歴に含めたり履歴から除外したりするには、各ロールの横にある **[ロール]** チェックボックスをオンまたはオフにします。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールのアクティビティおよび監査履歴を表示する](azure-pim-resource-rbac.md)
