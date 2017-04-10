---
title: "Azure Active Directory のパスワード ポリシーと制限 | Microsoft Docs"
description: "Azure Active Directory のパスワードに適用されるポリシー (使用できる文字、長さ、有効期限など) について説明します"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e7cae5cf-e0ee-467b-9a90-db6fdf56cd52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 9f6c36011941510d2da7f6a043128e493c13d47b
ms.lasthandoff: 03/28/2017


---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory のパスワード ポリシーと制限
この記事では、Azure AD ディレクトリに格納されているユーザー アカウントと関連付けられたパスワード ポリシーと複雑さの要件について説明します。

> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、 [自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)。
>
>

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>すべてのユーザー アカウントに適用される UserPrincipalName ポリシー
Azure AD 認証システムにサインインする必要があるすべてのユーザー アカウントは、一意のユーザー プリンシパル名 (UPN) 属性値がそのアカウントに関連付けられている必要があります。 次の表は、オンプレミス Active Directory ソースのユーザー アカウント (クラウドと同期) とクラウドのみのユーザー アカウントの両方に適用されるポリシーの概要です。

| プロパティ | UserPrincipalName の要件 |
| --- | --- |
| 使用できる文字 |<ul> <li>A - Z</li> <li>a - z</li><li>0 – 9</li> <li> に関するページを参照してください。 - \_ ! \# ^ \~</li></ul> |
| 使用できない文字 |<ul> <li>ユーザー名とドメインの間以外にある '@' 文字</li> <li>'@' 記号の直前のピリオド文字 '.'</li></ul> |
| 長さの制限 |<ul> <li>全体の長さは 113 文字以内にする必要があります</li><li>‘@’ 記号の前に 64 文字</li><li>‘@’ 記号の後に 48 文字</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>クラウド ユーザー アカウントにのみ適用されるパスワード ポリシー
次の表は、Azure AD で作成し、管理しているユーザー アカウントに適用できる利用可能なパスワード ポリシー設定の説明です。

| プロパティ | 必要条件 |
| --- | --- |
| 使用できる文字 |<ul><li>A - Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 使用できない文字 |<ul><li>Unicode 文字</li><li>スペース</li><li> **強力なパスワードのみ**: '@' 記号の直前にピリオド文字 ('.') を含めることはできません</li></ul> |
| パスワードの制限 |<ul><li>8 文字以上 16 文字以下</li><li>**強力なパスワードのみ**: 次の 4 つのうち、3 つが必要です。<ul><li>小文字</li><li>大文字</li><li>数値 (0 - 9)</li><li>記号 (上述のパスワード制限を参照してください)</li></ul></li></ul> |
| パスワードの有効期間 |<ul><li>規定値: **90** 日 </li><li>値を構成するには、Windows PowerShell の Azure Active Directory モジュールから Set-MsolPasswordPolicy コマンドレットを使用します。</li></ul> |
| パスワードの期限切れの通知 |<ul><li>既定値: **14** 日 (パスワードの有効期限が切れる前)</li><li>値は Set-MsolPasswordPolicy コマンドレットを使用して構成できます。</li></ul> |
| パスワードの期限切れ |<ul><li>既定値: **false** 日 (パスワードの有効期限が有効になっていることを示します) </li><li>各ユーザー アカウントの値を構成するには、Set-MsolUser コマンドレットを使用します。 </li></ul> |
| パスワード**変更**履歴 |パスワードを**変更する**ときに、前回のパスワードをもう一度使用することは**できません**。 |
| パスワード **リセット**履歴 | 忘れたパスワードを**リセット**するときに、前回のパスワードをもう一度使用することが**できます**。 |
| アカウントのロックアウト |正しくないパスワードを入力してサインイン試行に 10 回失敗したユーザーを、1 分間ロックアウトします。 不適切なサインイン試行をさらに行った場合は、ロックアウトの期間が延長されます。 |

## <a name="next-steps"></a>次のステップ
* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
* [任意の場所からのパスワードの管理](active-directory-passwords.md)
* [パスワード管理のしくみ](active-directory-passwords-how-it-works.md)
* [パスワード管理の概要](active-directory-passwords-getting-started.md)
* [パスワード管理のカスタマイズ](active-directory-passwords-customize.md)
* [パスワード管理のベスト プラクティス](active-directory-passwords-best-practices.md)
* [パスワード管理レポートで運用情報を把握する方法](active-directory-passwords-get-insights.md)
* [パスワード管理に関する FAQ](active-directory-passwords-faq.md)
* [パスワード管理のトラブルシューティング](active-directory-passwords-troubleshoot.md)
* [詳細情報](active-directory-passwords-learn-more.md)

