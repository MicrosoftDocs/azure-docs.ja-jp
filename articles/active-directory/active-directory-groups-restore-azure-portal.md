---
title: "Azure Active Directory プレビューで削除された Office 365 グループを復元する | Microsoft Docs"
description: "Azure Active Directory で、削除されたグループを復元する方法、復元可能なグループを表示する方法、およびグループを完全に削除する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: cc177695f73805940f6a0229c94e391bf3899f09
ms.lasthandoff: 03/29/2017


---

# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Azure Active Directory で削除された Office 365 グループを復元する

Azure Active Directory (Azure AD) プレビューで Office 365 グループを削除すると、削除されたグループは表示されなくなりますが、削除日から 30 日間は保持されます。 これは、必要に応じて、グループとその内容を復元できるようにするためです。 この機能は、Azure AD の Office 365 グループに限定されます。 セキュリティ グループや配布グループでは使用できません。

グループを復元するために必要なアクセス許可は、次のいずれかになります。

役割  | アクセス許可 
--------- | ---------
会社の管理者、パートナー レベル 2 のサポート、InTune サービス管理者 | 削除されたすべての Office 365 グループを復元できます。 
ユーザー アカウント管理者、パートナー レベル 1 のサポート | 会社の管理者ロールに割り当てられているグループを除き、削除されたすべての Office 365 グループを復元できます。 
User | ユーザーが所有する、削除されたすべての Office 365 グループを復元できます。 


## <a name="how-to-view-deleted-office-365-groups-that-are-available-to-restore"></a>復元可能な削除済み Office 365 グループを表示する方法
次のコマンドレットを使用すると、削除されたグループを表示して、目的のグループがまだ完全には削除されていないことを確認できます。 これらのコマンドレットは、[Azure Active Directory PowerShell V2 プレビュー モジュール](https://www.powershellgallery.com/packages/AzureADPreview) (ダウンロード リンク) に含まれています。 このモジュールの詳細については、「[Azure Active Directory PowerShell Version 2 (Azure Active Directory PowerShell バージョン 2)](https://docs.microsoft.com/powershell/azuread/)」をご覧ください。

1.    次のコマンドレットを実行して、テナント内のまだ復元可能なすべての削除済み Office 365 グループを表示します。
  ```
  Get-AzureADMSDeletedGroup
  ```

2.    特定のグループの objectID がわかっている場合 (手順 1 のコマンドレットで objectID を取得できる場合) は、次のコマンドレットを実行して、削除された特定のグループがまだ完全には削除されていないことを確認できます。
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-an-office-365-group"></a>Office 365 グループを復元する方法
グループがまだ復元可能であることを確認したら、次のいずれかの手順を実行して削除されたグループを復元します。 グループにドキュメント、SP サイト、または他の永続的なオブジェクトが含まれている場合、グループとその内容を完全に復元するまでに最大 24 時間かかることがあります。

1.    次のコマンドレットを実行して、グループとその内容を復元します。
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

また、次のコマンドレットを実行して、削除されたグループを完全に削除することもできます。
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>グループが復元されたことを確認する方法
Office 365 グループが正常に復元されたことを確認するには、`Get-AzureADGroup –ObjectId <objectId>` コマンドレットを実行してそのグループの情報を表示します。 復元要求が完了すると、次のようになります。
- Exchange の左側のナビゲーション バーにグループが表示されます。
- Planner にグループのプランが表示されます。
- Sharepoint サイトとそのすべてのコンテンツを利用できるようになります。
- Exchange エンドポイントと、Office 365 グループをサポートする他の Office 365 ワークロードからグループにアクセスできます。


## <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory グループに関する追加情報を提供します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)
