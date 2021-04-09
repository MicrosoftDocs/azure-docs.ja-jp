---
title: B2B の招待メールの要素 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの招待メール テンプレート
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb134a2fb784e02f5e00c9e88ab0df1794489e0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860594"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B コラボレーションの招待メールの要素 - Azure Active Directory

招待メールは、パートナーを B2B コラボレーション ユーザーとして Azure AD に参加させるための重要なコンポーネントです。 [B2B コラボレーションの使用について招待メールを送信することは必須ではありません](add-user-without-invite.md)が、送信すれと、招待を受け入れるかどうかを決定するために必要なすべての情報がユーザーに提供されます。 また、ユーザーが後でリソースに戻る必要がある場合に常に参照できるリンクも提供されます。

![B2B の招待メールを示すスクリーンショット](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>電子メールの説明

これらの機能の最適な使用方法がわかるように、電子メールの要素をいくつか見てみましょう。

### <a name="subject"></a>サブジェクト

メールの件名のパターンは次のようになります。

組織内のアプリケーションにアクセスするための &lt;ユーザー名&gt; さんからの招待。

### <a name="from-address"></a>差出人アドレス

差出人アドレスには、LinkedIn と同様のパターンが使用されています。 このパターンにより、メールは invites@microsoft.com からのものであっても、招待は別の組織からであることがはっきりわかります。 形式は次のようになります:Microsoft Invitations  <invites@microsoft.com> または Microsoft Invitations (&lt;テナント名&gt; の代理)  <invites@microsoft.com>. 

### <a name="reply-to"></a>返信

電子メールの返信先は招待元の電子メール アドレス (使用可能な場合) に設定されるので、電子メールに返信すると、招待元に送信されます。

### <a name="phishing-warning"></a>フィッシングの警告

メールの冒頭にはユーザーに対するフィッシングについての簡単な警告文があり、予期されている招待のみを受け入れる必要があることが警告されています。 事前に招待先に連絡しておくことで、招待先のパートナーが招待を受け取って驚かないようにすることをお勧めします。

![メールに記載されるフィッシング詐欺の警告の画像](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>招待元の情報と招待メッセージ

このメールには、招待を送信する組織に関連付けられている名前とプライマリ ドメインが含まれています。 この情報は、招待された人が、招待を受け入れるかどうかを情報に基づいて決定するのに役立ちます。 [ディレクトリ、グループ、アプリにゲスト ユーザーを招待する](add-users-administrator.md)とき、または[招待 API を使用する](customize-invitation-api.md)ときに、招待元が招待の一部としてメッセージを含めた場合、メールのメイン セクションでメッセージが強調表示されます。 また、招待元が設定した場合は、招待元の名前とプロファイル イメージも含まれます。 メッセージ自体はテキスト領域であるため、セキュリティ上の理由から、HTML タグは処理されません。

![メールでの招待メッセージの画像](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>同意ボタンとリダイレクト URL

メールの次のセクションには、招待に同意した後で招待された人が移動される先の場所に関する情報と、そのためのボタンが含まれています。  将来、招待された人はいつでもこのリンクを使用してリソースに直接戻ることができます。

![メールの同意ボタンとリダイレクト URL の画像](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>フッター セクション

フッターには、送信された招待に関する詳細情報が含まれています。 招待された人が将来の招待をブロックするためのオプションが常にあります。 組織に[プライバシーに関する声明が設けられている](../fundamentals/active-directory-properties-area.md)場合、声明へのリンクがここに表示されます。  それ以外の場合は、組織によってプライバシーに関する声明が設けられていないことが示されます。

![メールのフッター セクションの画像](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>組織をブロックする (購読解除)

組織からの招待には、フッターに **今後の招待をブロックする** オプションが含まれています。 ゲスト ユーザーはこのリンクを選択し、組織から今後届く招待をブロックできます。 この操作により、[https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) にあるユーザーの登録解除リストにも組織が追加されます。

### <a name="viewing-organizations-youve-blocked"></a>ブロックしている組織を表示する

ゲスト ユーザーはブロックされている組織を次の手順で表示またはエクスポートできます。

1. [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) に移動します。
2. メール アドレスを入力し、サインイン手順に従い、メールでワンタイム パスコード認証を行います。
3. ブロックしている組織を表示するか、コピーと貼り付けで名前をエクスポートします。
   > [!NOTE]
   > ブロックしている組織に対して再度招待することを許可する場合、組織を選択し、 **[次へ]** を選択します。

## <a name="how-the-language-is-determined"></a>言語が決定される方法

招待電子メール内のゲスト ユーザーに表示される言語は、次の設定によって決定されます。 これらの設定は、優先順位の順に一覧表示されています。 ある設定が構成されていない場合は、一覧内の次の設定によって言語が決定されます。

- 招待の作成 API が使用されている場合は、[invitedUserMessageInfo](/graph/api/resources/invitedusermessageinfo) オブジェクトの **messageLanguage** プロパティ
-   ゲストの [ユーザー オブジェクト](/graph/api/resources/user)で指定されている **preferredLanguage** プロパティ
-   ゲスト ユーザーのホーム テナントのプロパティで設定されている **通知言語** (Azure AD テナントの場合のみ)
-   リソース テナントのプロパティで設定されている **通知言語**

これらのいずれの設定も構成されていない場合、言語は既定で英語 (米国) になります。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](add-users-administrator.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](add-users-information-worker.md)
- [B2B コラボレーションの招待の利用](redemption-experience.md)
- [招待を使用せずに B2B コラボレーション ユーザーを追加する](add-user-without-invite.md)
