---
title: "Azure Active Directory のレポート通知"
description: "疑わしいサインインに対して Azure Active Directory のレポート通知を使用する方法。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 90ba006f25c27e1dc24c85fdc4ef1d5e2a8d0e3d


---
# <a name="azure-active-directory-reporting-notifications"></a>Azure Active Directory のレポート通知
## <a name="what-reports-generate-email-notifications"></a>電子メール通知を生成するレポート
現時点では、電子メール通知は、不規則なサインイン アクティビティ レポートによってのみトリガーされます。

## <a name="what-is-an-irregular-sign-in"></a>"不規則なサインイン" とは何ですか。
不規則なサインインは、異常なサインイン場所とデバイスを組み合わせた「不可能な移動」条件に基づいてマイクロソフトの機械学習アルゴリズムによって識別されるサインインです。 これは、ハッカーがこのアカウントを使用してサインインを試行したことを示している可能性があります。

## <a name="who-receives-the-email-notifications"></a>誰が電子メールの通知を受け取りますか。
電子メールは、Active Directory Premium ライセンスが割り当てられているすべてのグローバル管理者に送信されます。 確実に配信されるようにするために、管理者の連絡用電子メール アドレスにも送信されます。 管理者は、 aad-alerts-noreply@mail.windowsazure.com 電子メールを確実に受信するために、差出人セーフ リストに登録する必要があります。

## <a name="how-often-are-these-emails-sent"></a>どの程度の頻度で電子メールが送信されますか。
電子メールは、過去 30 日以内、または最後の電子メールの送信後、10 回の新しい不規則なサインイン アクティビティが発生した場合に送信されます。

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>電子メールに記載されているレポートには、どのようにアクセスできますか。
リンクをクリックすると、Azure クラシック ポータル内のレポート ページにリダイレクトされます。 レポートにアクセスするには、次の両方の条件を満たす必要があります。

* Azure サブスクリプションの管理者または共同管理者
* ディレクトリのグローバル管理者であり、Active Directory Premium ライセンスが割り当てられている 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

## <a name="can-i-turn-off-these-emails"></a>電子メールの通知を無効にすることができますか。
はい。Azure クラシック ポータル内で異常なサインインに関連する通知をオフにするには、**[構成]** をクリックして **[通知]** セクションの下で **[無効]** をクリックします。

## <a name="whats-next"></a>参照トピック
* 使用可能なセキュリティ、監査、およびアクティビティ レポートに興味がある場合は、 [Azure AD のセキュリティ、監査、およびアクティビティ レポート](active-directory-view-access-usage-reports.md)に関するページをご覧ください
* [Azure Active Directory Premium の概要](active-directory-get-started-premium.md)
* [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)




<!--HONumber=Dec16_HO5-->


