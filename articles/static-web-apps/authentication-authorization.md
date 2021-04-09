---
title: Azure Static Web Apps の認証と承認
description: さまざまな承認プロバイダーを使用して、静的アプリをセキュリティで保護する方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: ab41a336c32a1827c23f4c4619f47dc294a4d2ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419288"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビューの認証と承認

Azure Static Web Apps では、次のプロバイダーでの認証を管理することで、認証エクスペリエンスを効率化しています。

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

プロバイダー固有の [招待](#invitations)によってユーザーはロールに関連付けられ、承認されたユーザーには、_routes.json_ ファイルに定義された規則によって [ルート](routes.md)へのアクセス権が付与されます。

すべての認証プロバイダーは、既定で有効になっています。 認証プロバイダーを制限するには、カスタム ルート規則によって[アクセスをブロック](#block-an-authorization-provider)します。

認証と承認のトピックは、ルーティングの概念とかなり重複します。 この記事と併せて、[ルーティング ガイド](routes.md)を是非お読みください。

## <a name="roles"></a>ロール

静的 Web アプリにアクセスするすべてのユーザーは、1 つまたは複数のロールに属しています。  ユーザーは、以下の 2 つの組み込みロールに属することができます。

- **匿名**:すべてのユーザーは自動的に "_匿名_" ロールに属します。
- **認証済み**:ログインしているすべてのユーザーは、"_認証済み_" ロールに属します。

組み込みロール以外に新しいロールを作成して、招待を使ってユーザーに割り当て、_routes.json_ ファイルで参照することができます。

## <a name="role-management"></a>ロール管理

### <a name="add-a-user-to-a-role"></a>ユーザーをロールに追加する

Web サイトにユーザーを追加するには、特定のロールにユーザーを関連付けできる招待を生成します。 ロールは、_route. json_ ファイル内で定義および管理されています。

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>招待を作成する

招待は個々の承認プロバイダーに固有であるため、サポートするプロバイダーを選択する際には、アプリのニーズを考慮してください。 プロバイダーによって、ユーザーの電子メール アドレスが公開される場合もあれば、サイトのユーザー名のみが提示される場合もあります。

<a name="provider-user-details" id="provider-user-details"></a>

| 承認プロバイダー | 公開されるユーザーの情報  |
| ---------------------- | ----------------- |
| Azure Active Directory | メール アドレス     |
| Facebook               | メール アドレス     |
| GitHub                 | username          |
| Google<sup>1</sup>     | メール アドレス     |
| Twitter                | username          |

1. [Azure portal](https://portal.azure.com) 上で Static Web Apps リソースに移動します。
1. _[設定]_ 下で、 **[ロール管理]** をクリックします。
1. **[招待]** ボタンをクリックします。
1. オプションの一覧から _[承認プロバイダー]_ を選択します。
1. _[Invitee details]\(招待者の詳細\)_ ボックスに、受信者のユーザー名または電子メール アドレスを追加します。
    - GitHub および Twitter の場合は、ユーザー名を入力します。 それ以外の場合は、受信者の電子メール アドレスを入力します。
1. _[ドメイン]_ ドロップダウンから静的サイトのドメインを選択します。
    - 選択されたドメインは、招待に表示されるドメインになります。 サイトに関連付けられているカスタム ドメインがある場合は、そのカスタム ドメインを選択することができます。
1. _[ロール]_ ボックスにロール名のコンマ区切りの一覧を追加します。
1. 招待が有効なまま維持される最大時間数を入力します。
    - 指定できる最大上限は、168 時間 (7 日間) です。
1. **[生成]** ボタンをクリックします。
1. _[Invite link]\(招待リンク\)_ ボックスからリンクをコピーします。
1. アプリへのアクセス権を付与するユーザーに招待リンクを電子メールで送信します。

ユーザーが招待のリンクをクリックすると、該当のアカウントでログインするように求められます。 正常にログインが行われると、選択されたロールにユーザーが関連付けられます。

> [!CAUTION]
> ルート規則が、選択された認証プロバイダーと競合しないようにしてください。 ルート規則によってプロバイダーがブロックされると、ユーザーは招待を承諾することができなくなります。

### <a name="update-role-assignments"></a>ロールの割り当てを更新する

1. [Azure portal](https://portal.azure.com) 上で Static Web Apps リソースに移動します。
1. _[設定]_ 下で、 **[ロール管理]** をクリックします。
1. 一覧上でユーザーをクリックします。
1. _[ロール]_ ボックスで、ロールの一覧を編集します。
1. **[更新]** ボタンをクリックします。

### <a name="remove-user"></a>ユーザーの削除

1. [Azure portal](https://portal.azure.com) 上で Static Web Apps リソースに移動します。
1. _[設定]_ 下で、 **[ロール管理]** をクリックします。
1. 一覧上でユーザーを探します。
1. そのユーザーの行のチェック ボックスをオンにします。
1. **[削除]** ボタンをクリックします。

ユーザーを削除する際には、以下の点に注意してください。

1. ユーザーを削除すると、そのユーザーのアクセス許可は無効になります。
1. 世界規模での反映には、数分かかる場合があります。
1. ユーザーがアプリに再び追加される場合、[`userId` は変更されます](user-information.md)。

## <a name="remove-personal-identifying-information"></a>個人が特定される情報を削除する

エンドユーザーとしてアプリケーションに同意すると、ID プロバイダーに応じて電子メール アドレスまたはユーザー名に、そのアプリケーションからアクセスできるようになります。 この情報が提供されたら、アプリケーションの所有者は、個人が特定される情報をどのように管理するかを決定します。

この情報をシステムから取り消すには、エンドユーザーは、個々の Web アプリの管理者に連絡する必要があります。

Azure Static Web Apps プラットフォームから個人が特定される情報を削除し、今後の要求でプラットフォームからこの情報が提供されないようにするには、以下の URL を使用して要求を送信します。

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

今後の要求でプラットフォームから個々のアプリに対してこの情報が提供されないようにするには、以下の URL へ要求を送信します。

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>システム フォルダー

Azure Static Web Apps では、`/.auth` システム フォルダーを使用して、承認関連の API へのアクセスが提供されています。 `/.auth` フォルダー下のいずれかのルートをエンドユーザーに直接公開するのではなく、[ルーティング規則](routes.md)を作成してわかりやすい URL を作ることを検討してください。

## <a name="login"></a>ログイン

次の表を使用して、プロバイダー固有のログイン ルートを確認します。

| 承認プロバイダー | ログイン ルート             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

たとえば、GitHub にログインするために、次のスニペットのようなログイン リンクを含めることができます。

```html
<a href="/.auth/login/github">Login</a>
```

複数のプロバイダーをサポートすることを選択した場合は、それぞれに対応するプロバイダー固有のリンクを Web サイト上に公開する必要があります。

[ルート規則](./configuration.md#routes)を使用して、既定のプロバイダーを _/login_ のようなわかりやすいルートにマップできます。

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>ログイン後のリダイレクト

ログイン後にユーザーが特定のページに戻るようにするには、`post_login_redirect_uri` クエリ文字列パラメーターに URL を指定します。

## <a name="logout"></a>Logout

`/.auth/logout` ルートでは、Web サイトからユーザーをログアウトします。 次の例に示すように、サイト ナビゲーションにユーザーがログアウトできるリンクを追加することが可能です。

```html
<a href="/.auth/logout">Log out</a>
```

[ルート規則](./configuration.md#routes)を使用して、 _/login_ のようなわかりやすいルートにマップできます。

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>ログアウト後のリダイレクト

ログアウト後にユーザーが特定のページに戻るようにするには、`post_logout_redirect_uri` クエリ文字列パラメーターに URL を指定します。

## <a name="block-an-authorization-provider"></a>承認プロバイダーをブロックする

承認プロバイダーを使用しないように、アプリを制限することができます。 たとえば、お使いのアプリ上で、[電子メール アドレスを公開するプロバイダー](#provider-user-details)のみに統一したい場合があります。

プロバイダーをブロックするには、[ルート規則](routes.md)を作成して、ブロックされたプロバイダー固有のルートへの要求に 404 を返すことができます。 たとえば、プロバイダーとして Twitter を制限するには、次のルート規則を追加します。

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>制限

一般的な制限事項と限度については、[クォータに関する記事](quotas.md)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ユーザーの認証と承認のデータにアクセスする](user-information.md)

<sup>1</sup> 外部証明書は保留されています。
