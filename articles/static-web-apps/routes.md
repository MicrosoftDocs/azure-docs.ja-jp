---
title: Azure Static Web Apps でのルート
description: バックエンド ルーティング規則と、ロールを使用してルートをセキュリティ保護する方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5cbcbcf8914a663a6d039abecd6a4488eaf677b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739646"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビューでのルート

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

Azure Static Web Apps でのルーティングでは、静的コンテンツと API の両方に対する、バックエンド ルーティング規則と承認動作が定義されています<sup>1</sup>。 規則は、規則の配列として _routes.json_ ファイルで定義されます。

- _routes.json_ ファイルは、アプリのビルド成果物フォルダーのルートに存在する必要があります。
- 規則は、`routes` 配列に出現する順序で実行されます。
- 最初に一致した時点で、規則の評価は停止します。 ルーティング規則は連結されません。
- ロールは _routes.json_ ファイルで定義されており、ユーザーは [招待](authentication-authorization.md)によってロールに関連付けられます。
- ロールの名前はお客様が完全に制御できます。

ルーティングのトピックは、認証と承認の概念とかなり重複します。 この記事と共に、[認証と承認](authentication-authorization.md)に関するガイドをお読みください。

詳細については、[ルート ファイルの例](#example-route-file)を参照してください。

## <a name="location"></a>場所

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

_routes.json_ ファイルは、アプリのビルド成果物フォルダーのルートに存在する必要があります。 Web アプリに、ビルドされたファイルを特定のフォルダーからビルド成果物フォルダーにコピーするビルド ステップが含まれている場合は、_routes.json_ ファイルがその特定のフォルダーに存在している必要があります。

次の表では、いくつかのフロントエンド フレームワークとライブラリについて、_routes.json_ ファイルを配置する適切な場所を示します。

|フレームワーク/ライブラリ | 場所  |
|---------|----------|
| Angular | _assets_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |
| Blazor  | _wwwroot_ |

上の表は、Azure Static Web Apps と互換性のあるいくつかのフレームワークとライブラリを表示したものに過ぎません。 詳細については、[フロントエンド フレームワークとライブラリの構成](./front-end-frameworks.md)に関するページを参照してください。

## <a name="defining-routes"></a>ルートの定義

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

ルートは、`routes` プロパティのルート規則の配列として、_routes.json_ ファイルで定義されています。 各規則は、ルート パターンと、1 つまたは複数のオプションの規則プロパティで構成されます。 使用例については、[ルート ファイルの例](#example-route-file)を参照してください。

| 規則のプロパティ  | 必須 | 既定値 | 解説                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | はい      | 該当なし          | 呼び出し元によって要求されるルート パターン。<ul><li>ルート パスの末尾には、[ワイルドカード](#wildcards)を使用できます。 たとえば、ルート _admin/\*_ は、_admin_ パスの下にあるすべてのルートと一致します。<li>ルートの既定のファイルは、_index.html_ です。</ul>|
| `serve`        | いいえ       | 該当なし          | 要求から返されるファイルまたはパスを定義します。 ファイルのパスと名前は、要求されたパスと異なっていてもかまいません。 `serve` の値が定義されていない場合は、要求されたパスが使用されます。 Querystring パラメーターはサポートされていません。`serve` の値では、実際のファイルが指定されている必要があります。  |
| `allowedRoles` | いいえ       | anonymous     | ロール名の配列。 <ul><li>有効な文字は、`a-z`、`A-Z`、`0-9`、`_` です。<li>組み込みのロール `anonymous` は、認証されていないすべてのユーザーに適用されます。<li>組み込みのロール `authenticated` は、ログインしているすべてのユーザーに適用されます。<li>ユーザーは、少なくとも 1 つのロールに属している必要があります。<li>ロールは、_OR_ に基づいて照合されます。 ユーザーが一覧にあるいずれかのロールに属している場合は、アクセス権が付与されます。<li>個々のユーザーは、[招待](authentication-authorization.md)によってロールに関連付けられます。</ul> |
| `statusCode`   | いいえ       | 200           | 要求に対する [HTTP 状態コード](https://wikipedia.org/wiki/List_of_HTTP_status_codes)の応答。 |

## <a name="securing-routes-with-roles"></a>ロールによるルートのセキュリティ保護

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

ルートをセキュリティで保護するには、1 つまたは複数のロール名を規則の `allowedRoles` 配列に追加します。 使用例については、[ルート ファイルの例](#example-route-file)を参照してください。

既定では、すべてのユーザーが組み込みの `anonymous` ロールに属し、ログインしているすべてのユーザーが `authenticated` ロールのメンバーになります。 たとえば、認証されたユーザーのみにルートを制限するには、組み込みの `authenticated` ロールを `allowedRoles` 配列に追加します。

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

必要に応じて、`allowedRoles` 配列に新しいロールを作成できます。 ルートを管理者のみに制限するには、`administrator` ロールを `allowedRoles` 配列で定義します。

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- ロールの名前は完全に制御できます。ロールが従う必要のあるリストはありません。
- 個々のユーザーは、[招待](authentication-authorization.md)によってロールに関連付けられます。

## <a name="wildcards"></a>ワイルドカード

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

ワイルドカード規則は、指定されたルート パターンでのすべての要求と一致します。 規則で `serve` の値を定義すると、指定したファイルまたはパスが応答として提供されます。

たとえば、カレンダー アプリケーションに対するルートを実装するには、1 つのファイルを提供するように、_calendar_ ルートの下にあるすべての URL をマップできます。

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

その場合、_calendar.html_ ファイルでは、クライアント側ルーティングを使用して、`/calendar/january/1`、`/calendar/2020`、`/calendar/overview` などの URL のバリエーションに対して異なるビューを提供できます。

また、ワイルドカードを使用してルートをセキュリティで保護することもできます。 次の例では、_admin_ パスで要求されるすべてのファイルには、_administrator_ ロールのメンバーである認証済みのユーザーが必要です。

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> 提供されるファイルによって参照されているファイルへの要求は、認証チェックについてのみ評価されます。 たとえば、ワイルドカード パスの下にある CSS ファイルへの要求に対して提供されるのは CSS ファイルであり、`serve` ファイルとして定義されているものではありません。 ユーザーが必要な認証要件を満たしている限り、CSS ファイルが提供されます。

## <a name="fallback-routes"></a>フォールバック ルート

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

シングルページ アプリケーションは、フロントエンドの JavaScript フレームワークまたはライブラリを使用しているか、Blazor のような WebAssembly プラットフォームを使用しているかにかかわらず、多くの場合、クライアント側ルーティングを使用して Web アプリのナビゲーションを行っています。 これらのクライアント側ルーティング規則では、要求をサーバーに返さずに、ブラウザーのウィンドウの場所が更新されます。 ページを更新する場合、またはクライアント側ルーティング規則によって生成された場所に直接移動する場合は、適切な HTML ページを提供するために、サーバー側フォールバック ルートが必要です。

一般的なフォールバック ルートの例を次に示します。

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

フォールバック ルートは、それより前に定義されている規則によってキャッチされないすべての要求をキャッチするため、ルーティング規則の一覧の最後に追加する必要があります。

## <a name="redirects"></a>リダイレクト

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

[301](https://en.wikipedia.org/wiki/HTTP_301) および [302](https://en.wikipedia.org/wiki/HTTP_302) の HTTP 状態コードを使用して、あるルートから別のルートに要求をリダイレクトすることができます。

たとえば、次の規則では、_old-page.html_ から _new-page.html_ への 301 リダイレクトが作成されます。

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

リダイレクトは、個別のファイルが定義されていないパスでも機能します。

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>カスタム エラー ページ

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

ユーザーは、エラーが発生する可能性のあるさまざまな状況に遭遇する場合があります。 `platformErrorOverrides` 配列を使用して、これらのエラーに対するカスタム エクスペリエンスを提供できます。 _routes.json_ ファイルへの配列の配置については、[ルート ファイルの例](#example-route-file)を参照してください。

> [!NOTE]
> 要求によってプラットフォーム オーバーライド レベルになると、ルート規則は再度実行されません。

次の表では、使用できるプラットフォーム エラー オーバーライドの一覧を示します。

| エラーの種類  | HTTP 状態コード | 説明 |
|---------|---------|---------|
| `NotFound` | 404  | ページがサーバーに見つかりません。 |
| `Unauthenticated` | 401 | ユーザーは、[認証プロバイダー](authentication-authorization.md)を使用してログインしていません。 |
| `Unauthorized_InsufficientUserInformation` | 401 | 認証プロバイダーでのユーザーのアカウントは、必要なデータを公開するように構成されていません。 このエラーは、アプリが認証プロバイダーにユーザーのメール アドレスを要求したが、ユーザーがメール アドレスへのアクセスを制限している場合などに、発生することがあります。 |
| `Unauthorized_InvalidInvitationLink` | 401 | 招待の有効期限が切れているか、ユーザーは別の受信者に対して生成された招待リンクを使用しました。  |
| `Unauthorized_MissingRoles` | 401 | ユーザーは、必要なロールのメンバーではありません。 |
| `Unauthorized_TooManyUsers` | 401 | サイトがユーザーの最大数に達したため、サーバーで追加が制限されています。 このエラーがクライアントに対して公開されるのは、生成できる[招待](authentication-authorization.md)の数には制限がなく、一部のユーザーは招待に同意しない可能性があるためです。|
| `Unauthorized_Unknown` | 401 | ユーザーを認証しようとして、不明な問題が発生しました。 このエラーの原因の 1 つとして可能性があるのは、ユーザーがアプリケーションに同意していないため、ユーザーが認識されない場合です。|

## <a name="custom-mime-types"></a>カスタム MIME の種類

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

`routes` 配列と同じレベルにリストされている `mimeTypes` オブジェクトを使用すると、[MIME の種類](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)をファイル拡張子と関連付けることができます。

```json
{
    "routes": [],
    "mimeTypes": {
        "custom": "text/html"
    }
}
```

上記の例では、拡張子が `.custom` のすべてのファイルは、`text/html` の MIME の種類で提供されます。

MIME の種類を使用するときは、次の考慮事項が重要です。

- キーを null、空、または 50 文字より多くすることはできません
- 値を null、空、または 1000 文字より多くすることはできません

> [!NOTE]
> Static Web Apps は、Blazor アプリケーションと、WASM ファイルおよび DLL ファイルに必要な MIME の種類を認識します。これらのアプリケーションのマッピングを追加する必要はありません。

## <a name="default-headers"></a>既定のヘッダー

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

`routes` 配列と同じレベルにリストされている `defaultHeaders` オブジェクトを使用すると、[応答ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers)を追加、変更、または削除できます。

ヘッダーに対して値を指定すると、ヘッダーが追加または変更されます。 空の値を指定すると、クライアントに提供されるヘッダーが削除されます。

```json
{
    "routes": [],
    "defaultHeaders": {
      "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
      "cache-control": "must-revalidate, max-age=6000",
      "x-dns-prefetch-control": ""
    }
}
```

上の例では、新しい `content-security-policy` ヘッダーが追加され、`cache-control` によってサーバーの既定値が変更され、`x-dns-prefetch-control` ヘッダーが削除されます。

ヘッダーを使用するときは、次の考慮事項が重要です。

- キーを null または空にすることはできません。
- 値を null または空にすると、ヘッダーが処理から削除されます。
- キーまたは値が 8,000 文字を超えることはできません。
- 定義されたヘッダーは、すべての要求で提供されます。
- _routes.json_ で定義されているヘッダーは、静的コンテンツにのみ適用されます。 関数のコードで API エンドポイントの応答ヘッダーをカスタマイズできます。

## <a name="example-route-file"></a>ルート ファイルの例

> [!IMPORTANT]
> 現在、*routes.json* ファイルで定義される機能は非推奨とされており、Azure Static Web Apps の[構成ファイル](./configuration.md#routes)で実装することが推奨されています。

次の例では、_routes.json_ ファイルで静的コンテンツと API に対するルート規則を作成する方法を示します。 一部のルートでは、認証関連のエンドポイントにアクセスする [ _/.auth_ システム フォルダー](authentication-authorization.md)を使用します。

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
      "custom": "text/html"
  }
}
```

次の例では、要求が規則と一致した場合の動作について説明します。

| 要求先 | 結果 |
|--|--|--|
| _/profile_ | 認証されたユーザーには、 _/profile/index.html_ ファイルが提供されます。 認証されていないユーザーは、 _/login_ にリダイレクトされます。 |
| _/admin/reports_ | _administrators_ ロールの認証されたユーザーには、 _/admin/reports/index.html_ ファイルが提供されます。 _administrators_ ロールではない認証されたユーザーには、401 エラー <sup>2</sup>が提供されます。 認証されていないユーザーは、 _/login_ にリダイレクトされます。 |
| _/api/admin_ | _administrators_ ロールの認証されたユーザーからの要求は、API に送信されます。 _administrators_ ロールではない認証されたユーザーおよび認証されていないユーザーには、401 エラーが提供されます。 |
| _/customers/contoso_ | _administrators_ ロールまたは _customers\_contoso_ ロールに属している認証されたユーザーには、 _/customers/contoso/index.html_ ファイル <sup>2</sup>が提供されます。 _administrators_ ロールまたは _customers\_contoso_ ロールでない認証されたユーザーには、401 エラーが提供されます。 認証されていないユーザーは、 _/login_ にリダイレクトされます。 |
| _/login_ | 認証されていないユーザーは、GitHub で認証するように求められます。 |
| _/.auth/login/twitter_ | Twitter での承認は無効になっています。 サーバーは 404 エラーで応答します。 |
| _/logout_ | ユーザーは、すべての認証プロバイダーからログアウトされます。 |
| _/calendar/2020/01_ | ブラウザーには、 _/calendar.html_ ファイルが提供されます。 |
| _/specials_ | ブラウザーは _/deals_ にリダイレクトされます。 |
| _/unknown-folder_ | _/custom-404.html_ ファイルが提供されます。 |
| `.custom` 拡張子のファイル | MIME の種類 `text/html` で提供されます |

すべての応答には、値が `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` である `content-security-policy` ヘッダーが含まれます。

<sup>1</sup> API 関数のルート規則では、[リダイレクト](#redirects)と[ロールによるルートのセキュリティ保護](#securing-routes-with-roles)のみがサポートされます。

<sup>2</sup> `platformErrorOverrides` 配列で `Unauthorized_MissingRoles` 規則を定義することにより、カスタム エラー ページを提供できます。

## <a name="restrictions"></a>制限

- _routes.json_ ファイルは、100 KB 以下にする必要があります
- _routes.json_ ファイルでは、最大で 50 の異なるロールがサポートされています

一般的な制限事項と限度については、[クォータに関する記事](quotas.md)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証と承認を設定する](authentication-authorization.md)
