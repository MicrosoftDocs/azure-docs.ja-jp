---
title: "Azure Active Directory B2B コラボレーションの招待メールの要素 | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションの招待メール テンプレート"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 46677085695b31d0e2b1102fa5a5105b6437adc6


---


# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>B2B コラボレーションの招待メールの要素

招待メールは、パートナーを B2B コラボレーション ユーザーとして Azure AD に参加させるための重要なコンポーネントです。 その主な目的は、受信者に対する信頼を高め、電子メールに正当性と社会的証明を加えることによって、受信者が安心して **[開始]** ボタンを選択し、招待を承諾できるようにすることです。 これは共有を促進する重要なコンポーネントです。 もちろん、電子メールの見栄えをよくすることも必要です。

![Azure AD B2B 招待電子メール](media/active-directory-b2b-invitation-email/invitation-email.png)

## <a name="explaining-the-email"></a>電子メールの説明
これらの機能の最適な使用方法がわかるように、電子メールの要素をいくつか見てみましょう。

### <a name="subject"></a>[件名]
電子メールの件名は、"&lt;テナント名&gt; 組織に招待されました" というパターンに従います。

### <a name="from-address"></a>差出人アドレス
差出人アドレスには、LinkedIn と同様のパターンが使用されています。 ここでの目的は、招待元がどの会社のだれであるかを明らかにし、電子メールが Microsoft の電子メール アドレスから送信されていることを明確に示すことです。 形式は、&lt;テナント名&gt; &lt;招待元の表示名&gt; (Microsoft 経由) <invites@microsoft.com&gt; になります。

### <a name="reply-to"></a>返信
電子メールの返信先は招待元の電子メール アドレス (使用可能な場合) に設定されるので、電子メールに返信すると、招待元に送信されます。

### <a name="branding"></a>ブランド
テナントからの招待メールでは、テナントで設定されている会社のブランドを使用します。 ブランドを利用する場合、構成方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)をご覧ください。 電子メールにはバナー ロゴが表示されます。 最良の結果を得るために、画像のサイズと品質については[こちら](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)の指示に従ってください。 また、行動への呼び掛けには会社名も表示されます。

### <a name="call-to-action"></a>行動への呼び掛け
行動への呼び掛けは、受信者が電子メールを受け取った理由と、受信者に求める行動の&2; つの部分で構成されます。
- "理由" セクションには、"&lt;テナント名&gt; 組織のアプリケーションにアクセスするよう招待されました" というパターンで対処できます。

- また、"受信者に求める行動" セクションは、**[開始]** ボタンの存在によって示されます。 招待を必要とせずに受信者が追加された場合、このボタンは表示されません。

### <a name="inviters-information"></a>招待元の情報
電子メールには招待元の表示名が含まれます。 また、Azure AD アカウントのプロフィール画像を設定している場合は、招待メールにその画像も含まれます。 これらはいずれも、電子メールに対する受信者の信頼を高めることを目的としています。

招待元がプロフィール画像をまだ設定していない場合は、Azure AD によって、次のように招待元のイニシャルを示すアイコンが画像の場所に作成されます。

  ![招待元のイニシャルの表示](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>本文
本文には、招待元が作成したメッセージまたは招待 API から渡されたメッセージが含まれます。 これはテキスト領域であり、セキュリティ上の理由から HTML タグは処理されません。

### <a name="footer-section"></a>フッター セクション
フッターには Microsoft 社のブランドが含まれており、電子メールが監視されていないエイリアスから送信されたものかどうかを受信者に伝えます。 特殊なケースは次のとおりです。

- 招待元が招待元のテナントで電子メール アドレスを持っていない場合

  ![招待元が招待元のテナントで電子メール アドレスを持っていない場合の画像](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- 受信者が招待を利用する必要がない場合

  ![受信者が招待を利用する必要がない場合](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


