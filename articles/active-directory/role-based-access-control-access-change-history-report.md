---
title: "アクセス レポート - Azure RBAC | Microsoft Docs"
description: "ロールベースのアクセス制御を使用した Azure サブスクリプションへのアクセス権の変更について、過去 90 日間分をすべて一覧表示するレポートを生成します。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 43ddeebfea4c914b8377d3363ba3d0c12db0adca
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="create-an-access-report-for-role-based-access-control"></a>ロール ベースのアクセス制御のアクセス レポートを作成する
サブスクリプション内でだれかがアクセス権を付与したり取り消したりしたときは、その変更が常に Azure イベントに記録されます。 アクセス変更履歴レポートを作成すると、過去 90 日間のすべての変更を確認できます。

## <a name="create-a-report-with-azure-powershell"></a>Azure PowerShell を使用したレポートの作成
PowerShell でアクセス変更履歴レポートを作成するには、[Get-AzureRMAuthorizationChangeLog](/powershell/module/azurerm.resources/get-azurermauthorizationchangelog) コマンドを使用します。

このコマンドを呼び出すには、表示する割り当てのプロパティを指定します。プロパティの例を次に示します。

| プロパティ | Description |
| --- | --- |
| **アクション** |アクセス権が付与されているか取り消されているか |
| **Caller** |アクセス権の変更を担当する所有者 |
| **PrincipalId** | ロールが割り当てられているユーザー、グループ、またはアプリケーションの一意の識別子 |
| **PrincipalName** |ユーザー、グループ、またはアプリケーションの名前 |
| **PrincipalType** |割り当てはユーザー、グループ、アプリケーションのどれか |
| **RoleDefinitionId** |付与または取り消されたロールの GUID |
| **RoleName** |付与または取り消されたロール |
| **スコープ** | 割り当ての適用先となるサブスクリプション、リソース グループ、またはリソースの一意の識別子 | 
| **ScopeName** |サブスクリプション、リソース グループ、またはリソースの名前 |
| **ScopeType** |割り当てはサブスクリプション、リソース グループ、またはリソース スコープのどのレベルで行われたか |
| **Timestamp** |アクセス権が変更された日時 |

このコマンド例を実行すると、サブスクリプションにおける過去 7 日間のすべてのアクセス変更が一覧表示されます。

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - スクリーンショット](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Azure CLI を使用したレポートの作成
Azure コマンド ライン インターフェイス (CLI) でアクセス変更履歴レポートを作成するには、 `azure role assignment changelog list` コマンドを使用します。

## <a name="export-to-a-spreadsheet"></a>スプレッドシートへのエクスポート
レポートを保存したりデータを操作したりするには、アクセス変更を .csv ファイルにエクスポートします。 このレポートをスプレッドシートで表示して確認することができます。

![スプレッドシートとして表示した Changelog - スクリーンショット](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="next-steps"></a>次のステップ
* [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md)
* [PowerShell を使用して RBAC を管理する](role-based-access-control-manage-access-powershell.md)方法を確認します。


