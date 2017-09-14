---
title: "グループの動的メンバーシップのトラブルシューティング | Microsoft Docs"
description: "Azure AD でのグループの動的メンバーシップ管理に関する問題について、トラブルシューティングのヒントを紹介します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 89bb04b6-a379-49c2-8465-fe386641816a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 8b4251c39a8883dfcb97ded8e4b533f073dce685
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>グループの動的メンバーシップのトラブルシューティング
**グループに対するルールを構成しましたが、グループのメンバーシップが更新されません**<br/>**[構成]** タブで **[委任されたグループ管理の有効化]** 設定が **[はい]** に設定されていることを確認してください。Azure Active Directory Premium ライセンスが割り当てられたユーザーとしてサインインした場合にのみ、この設定が表示されます。 ルールに使用しているユーザー属性の値を確認し、そのルールを満たすユーザーが存在することを確認してください。

**ルールの設定を変更したのですが、そのルールの既存のメンバーが削除されてしまいました**<br/>これは正しい動作です。 ルールを有効にしたり変更を加えたりするとグループの既存のメンバーは削除されます。 ルールの評価から返されたユーザーは、グループのメンバーとして追加されます。     

**ルールを追加または変更してもすぐにはメンバーシップの変更を確認できません。なぜでしょうか。**<br/>メンバーシップの評価に特化した機能が、非同期のバックグラウンド プロセスで定期的に実行されます。 プロセスにかかる時間は、ディレクトリ内のユーザー数と、ルールの結果として作成されるグループのサイズによって変わります。 ユーザー数の少ないディレクトリであれば通常、グループ メンバーシップの変更が数分以内に反映されます。 ディレクトリのユーザー数が多いと、変更が反映されるまでに 30 分以上かかる場合があります。

### <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory とは](active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

