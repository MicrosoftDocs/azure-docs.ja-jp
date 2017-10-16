---
title: "グループを使用したアクセス管理のための次のステップ | Microsoft Docs"
description: "セキュリティ グループの管理方法とそれらのグループを使用してリソースへのアクセスを管理する方法を上級者向けに説明しています。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: e1b88f7d43ba29589e5f1f4a8036c716ae984ae4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="managing-owners-for-a-group"></a>グループの所有者の管理
リソースの所有者がリソースへのアクセスを Azure AD グループに割り当てたら、そのグループの所有者がグループのメンバーシップを管理します。 実質的には、リソースの所有者が、ユーザーをリソースに割り当てる権限をグループの所有者に委任することになります。

## <a name="add-an-owner-to-a-group"></a>グループに所有者を追加する

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザーとグループ]** を選択します。
2. **[すべてのグループ]** を選択し、所有者を追加するグループを開きます。
3. **[所有者の追加]**を選択します。
4. **[所有者の追加]** ページで、このグループの所有者として追加するユーザーを選択し、その名前が **[選択済み]** ウィンドウに追加されていることを確認します。

## <a name="remove-an-owner-from-a-group"></a>グループから所有者を削除する

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザーとグループ]** を選択します。
2. **[すべてのグループ]** を選択し、所有者を削除するグループを開きます。
3. **[所有者]** タブをクリックします。
4. このグループから削除する所有者を選択し、 **[削除]**をクリックします。

## <a name="additional-information"></a>追加情報
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory とは](active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
