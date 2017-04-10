---
title: "許可されていない使用に関するレポート | Microsoft Docs"
description: "許可されていない使用に関するレポートを使用すると、有料の Azure AD 機能を使用しているライセンスのないユーザーを特定できます。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3400d00112b4b66cceef602dba5cb8666e49e0e4
ms.lasthandoff: 12/29/2016


---
# <a name="unlicensed-usage-report"></a>許可されていない使用に関するレポート
許可されていない使用に関するレポートを使用すると、有料の Azure AD 機能を使用しているライセンスのないユーザーを特定できます。 これにより、購入したライセンスの使用をより適切に管理できるほか、ライセンスを追加する必要があるタイミングがわかります。 

レポートには、過去 30 日間にわたって有料機能のアクティブな使用状況が示されます。 

## <a name="report-structure"></a>レポートの構造
| 列名 | 説明 |
|:--- |:--- |
| ライセンスのないユーザー |ユーザーの名前 |
| 機能 |機能名。 例: 条件付きアクセス |
| アクセスしたアプリケーション |機能によってアクセスされているアプリケーションの名前。 例: Office 365 SharePoint Online |

> [!NOTE]
> ユーザー アカウントが削除されている場合、[ライセンスのないユーザー] 列には 1003000090D8B285 のような ID が示されます。
> 
> 

## <a name="conditional-access-feature"></a>条件付きアクセス機能
条件付きアクセス ポリシーが適用されているサービスに Azure AD Premium ライセンスを持たないユーザーがアクセスすると、ライセンスを持たないユーザーであることを示すフラグが設定されます。 

これは、Intune を使用するデバイス ポリシーに加えて、MFA ポリシーや場所ポリシーにも適用されます。

## <a name="see-also"></a>関連項目
* [Office 365 およびその他の Azure Active Directory 接続アプリでの条件付きアクセスの使用](active-directory-conditional-access.md)
* [Azure AD への条件付きアクセスの基本](active-directory-conditional-access-azuread-connected-apps.md) 


