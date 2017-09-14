---
title: "Azure AD Connect Sync: AD DS アカウント パスワードの変更 | Microsoft ドキュメント"
description: "このトピックのドキュメントでは、AD DS アカウントのパスワードが変更された後に、Azure AD Connect を更新する方法について説明します。"
services: active-directory
keywords: "AD DS アカウント、Active Directory アカウント、パスワード"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 427070021ac547058c2f18be0e58ef6d81822b8a
ms.contentlocale: ja-jp
ms.lasthandoff: 04/12/2017

---
# <a name="changing-the-ad-ds-account-password"></a>AD DS アカウント パスワードの変更
AD DS アカウントとは、Azure AD Connect でオンプレミスの Active Directory との通信に使用したユーザー アカウントを指します。 AD DS アカウントのパスワードを変更する場合は、新しいパスワードを使用して Azure AD Connect 同期サービスを更新する必要があります。 それ以外の場合、同期サービスではオンプレミスの Active Directory と正しく同期できなくなり、次のエラーが発生します。

* Synchronization Service Manager で、オンプレミスの AD でのすべてのインポートまたはエクスポートの操作は、**no-start-credensials** エラーとなり、失敗します。

* Windows イベント ビューアーで、アプリケーション イベント ログに、**イベント ID 6000** のエラーと「**資格情報が有効でないため、管理エージェント "contoso.com" は実行できませんでした。**」というメッセージが表示されます。


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>AD DS アカウントの新しいパスワードで同期サービスを更新する方法
新しいパスワードを使用して同期サービスを更新するには、次のように実行します。

1. Synchronization Service Manager を起動します ([スタート]、[同期サービス] の順に移動します)。
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. **[コネクタ]** タブに移動します。

3. パスワードが変更された AD DS アカウントに対応する **AD コネクタ**を選択します。

4. **[アクション]** の **[プロパティ]** を選択します。

5. ポップアップ ダイアログで、**[Connect to Active Directory Forest (Active Directory フォレストに接続)]** を選択します。

6. AD DS アカウントの新しいパスワードを **[パスワード]** テキストボックスに入力します。

7. **[OK]** をクリックして新しいパスワードを保存し、ポップアップ ダイアログを閉じます。

8. Windows サービス コントロール マネージャーで Azure AD のConnect 同期サービスを再起動します。 これは、古いパスワードへの参照がメモリ キャッシュから削除されるようにするために行います。

## <a name="next-steps"></a>次のステップ
**概要トピック**

* [Azure AD Connect sync: 同期を理解してカスタマイズする](active-directory-aadconnectsync-whatis.md)

* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

