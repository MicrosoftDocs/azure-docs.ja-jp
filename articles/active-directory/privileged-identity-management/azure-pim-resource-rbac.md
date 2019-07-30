---
title: PIM で Azure リソース ロールのアクティビティおよび監査履歴を表示する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールのアクティビティおよび監査履歴を表示します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bd491d992ed15df288d9226b58bfe832e0692a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476506"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールのアクティビティおよび監査履歴を表示する

組織内の Azure リソース ロールのアクティビティ、アクティブ化、および監査履歴を Azure Active Directory (Azure AD) Privileged Identity Management (PIM) で表示できます。 対象には、サブスクリプション、リソース グループ、さらに仮想マシンも含まれます。 Azure のロールベースのアクセス制御 (RBAC) 機能を利用する Azure portal 内のすべてのリソースで、PIM のセキュリティおよびライフサイクル管理機能を利用できます。

## <a name="view-activity-and-activations"></a>アクティビティとアクティブ化を表示する

各リソースに対して特定のユーザーが行った操作を確認する場合、指定したアクティブ化期間に関連付けられている Azure リソース アクティビティを表示することができます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

1. アクティビティとアクティブ化を表示するリソースをクリックします。

1. **[ロール]** または **[メンバー]** をクリックします。

1. ユーザーをクリックします。

    日付ごとの Azure リソースに対するユーザー操作のグラフィカル ビューが表示されます。 その同じ期間での最近のロールのアクティブ化も表示されます。

    ![リソース アクティビティの概要とロールのアクティブ化を含むユーザーの詳細](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 特定のロールのアクティブ化をクリックして、詳細およびそのユーザーがアクティブな間に発生した、対応する Azure リソース アクティビティを表示します。

    ![選択されたロールのアクティブ化と日付別に表示されるアクティビティの詳細](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>子を含むロールの割り当てをエクスポートする

コンプライアンス要件により、ロールの割り当ての完全な一覧を監査担当者に提供しなければならない場合があります。 PIM を使用すると、特定のリソースでロールの割り当て (すべての子リソースのロールの割り当てを含む) のクエリを実行することができます。 以前は、管理者がサブスクリプションに対するロールの割り当ての完全な一覧を取得するのは困難で、特定のリソースごとにロールの割り当てをエクスポートする必要がありました。 PIM を使用すると、サブスクリプション内のアクティブかつ対象となるすべてのロールの割り当て (すべてのリソース グループとリソースに対するロールの割り当てを含む) についてクエリを実行できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

1. サブスクリプションなど、ロールの割り当てをエクスポートするリソースをクリックします。

1. **[メンバー]** をクリックします。

1. **[エクスポート]** をクリックして [メンバーシップのエクスポート] ウィンドウを開きます。

    ![すべてのメンバーをエクスポートする [メンバーシップのエクスポート] ウィンドウ](media/azure-pim-resource-rbac/export-membership.png)

1. **[すべてのメンバーのエクスポート]** をクリックして、すべてのロールの割り当てを CSV ファイルでエクスポートします。

    ![Excel に表示された CSV ファイルでエクスポートされたロールの割り当て](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>リソースの監査履歴を表示する

リソースの監査では、リソースのすべてのロール アクティビティのビューが提供されます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

1. 監査履歴を表示するリソースをクリックします。

1. **[リソースの監査]** をクリックします。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![フィルターを使用したリソースの監査の一覧](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. **[監査の種類]** で **[アクティブ化 (割り当て済み + アクティブ化済み)]** を選択します。

    ![アクティブ化の監査の種類でフィルター処理されたリソースの監査の一覧](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. **[操作]** の下で、ユーザーの **[(アクティビティ)]** をクリックして、そのユーザーの Azure リソースに対するアクティビティの詳細を表示します。

    ![特定のアクションに対するユーザーのアクティビティの詳細](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>自分の監査を表示する

自分の監査を使用すると、個人のロール アクティビティを表示できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

1. 監査履歴を表示するリソースをクリックします。

1. **[自分の監査]** をクリックします。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![現在のユーザーに対する監査の一覧](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
- [PIM で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
- [PIM で Azure AD ロールの監査履歴を表示する](pim-how-to-use-audit-log.md)
