---
title: B2B の招待メールの要素 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの招待メール テンプレート
services: active-directory
ms.service: active-directory
ms.subservice: b2b
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65768366"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B コラボレーションの招待メールの要素 - Azure Active Directory

招待メールは、パートナーを B2B コラボレーション ユーザーとして Azure AD に参加させるための重要なコンポーネントです。 それらを使用して、受信者の信頼を高めることができます。 電子メールに正当性と社会的証明を加えることによって、受信者が安心して **[開始]** ボタンを選択して招待に応じることができるようにします。 この信頼は、共有時の摩擦を軽減するための重要な手段です。 さらに、電子メールの見栄えを良くすることができます。

![B2B の招待メールを示すスクリーンショット](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>電子メールの説明
これらの機能の最適な使用方法がわかるように、電子メールの要素をいくつか見てみましょう。

### <a name="subject"></a>サブジェクト
電子メールの件名は次のパターンに従います:You're invited to the &lt;テナント名&gt; organization (テナント名の組織に招待されています)

### <a name="from-address"></a>差出人アドレス
差出人アドレスには、LinkedIn と同様のパターンが使用されています。  招待元がどの会社のだれであるかを明らかにし、電子メールが Microsoft の電子メール アドレスから送信されていることを明確に示す必要があります。 形式は次のようになります:Microsoft Invitations <invites@microsoft.com>、または &lt;テナント名&gt; からの &lt;招待元の表示名&gt; (Microsoft 経由) <invites@microsoft.com>。

### <a name="reply-to"></a>返信
電子メールの返信先は招待元の電子メール アドレス (使用可能な場合) に設定されるので、電子メールに返信すると、招待元に送信されます。

### <a name="branding"></a>ブランド
テナントからの招待メールでは、テナントで設定されている会社のブランドを使用します。 この機能を利用する場合は、[こちら](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)で構成方法の詳細を参照してください。 バナー ロゴが電子メールに表示されます。 最良の結果を得るために、画像のサイズと品質については[こちら](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)の指示に従ってください。 また、行動への呼び掛けには会社名も表示されます。

### <a name="call-to-action"></a>行動への呼び掛け
行動への呼び掛けは、受信者が電子メールを受け取った理由と、受信者に求める行動の 2 つの部分で構成されます。
- "why"(理由) セクションは、次のパターンを使用して指定できます:You've been invited to access applications in the &lt;テナント名&gt; organization (テナント名の組織のアプリケーションにアクセスするよう招待を受けています)

- また、"受信者に求める行動" セクションは、 **[開始]** ボタンの存在によって示されます。 招待を必要とせずに受信者が追加された場合、このボタンは表示されません。

### <a name="inviters-information"></a>招待元の情報
電子メールには、招待元の表示名が含まれます。 また、Azure AD アカウントのプロフィール画像を設定している場合は、招待メールにその画像も含まれます。 どちらも、電子メールに対する受信者の信頼を高めることを目的としています。

招待元がプロファイル画像をまだ設定していない場合は、次のように招待元のイニシャルを示すアイコンが画像の場所に作成されます。

  ![招待元のイニシャルが表示されている招待を示すスクリーンショット](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>本文
本文には、[ゲスト ユーザーをディレクトリ、グループ、またはアプリに招待](add-users-administrator.md)するときに、または [Invitation API を使用して](customize-invitation-api.md)招待元が作成するメッセージが含まれています。 これはテキスト領域であるため、セキュリティ上の理由で HTML タグは処理されません。

  ![招待メールの本文を示すスクリーンショット](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>フッター セクション
フッターには Microsoft 社のブランドが含まれており、電子メールが監視されていないエイリアスから送信されたものかどうかを受信者に伝えます。 

特殊なケースは次のとおりです。

- 招待元が招待元のテナントで電子メール アドレスを持っていない場合

  ![招待元が招待元のテナントでメール アドレスを持っていない場合のスクリーンショット](media/invitation-email-elements/inviter-no-email.png)


- 受信者が招待を利用する必要がない場合

  ![受信者が招待を利用する必要がない場合のスクリーンショット](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>言語が決定される方法
招待電子メール内のゲスト ユーザーに表示される言語は、次の設定によって決定されます。 これらの設定は、優先順位の順に一覧表示されています。 ある設定が構成されていない場合は、一覧内の次の設定によって言語が決定されます。 
- 招待の作成 API が使用されている場合は、[invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) オブジェクトの **messageLanguage** プロパティ
-   ゲストの[ユーザー オブジェクト](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)で指定されている **preferredLanguage** プロパティ
-   ゲスト ユーザーのホーム テナントのプロパティで設定されている**通知言語** (Azure AD テナントの場合のみ)
-   リソース テナントのプロパティで設定されている**通知言語**

これらのいずれの設定も構成されていない場合、言語は既定で英語 (米国) になります。

## <a name="next-steps"></a>次の手順

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](add-users-administrator.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](add-users-information-worker.md)
- [B2B コラボレーションの招待の利用](redemption-experience.md)
- [招待を使用せずに B2B コラボレーション ユーザーを追加する](add-user-without-invite.md)
