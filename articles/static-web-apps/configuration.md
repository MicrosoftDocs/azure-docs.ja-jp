---
title: Azure Static Web Apps を構成する
description: ルートの構成、セキュリティ規則の適用、および Azure Static Web Apps のグローバル設定について学習します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: 324a8e75488d74fc6aa52e499b8dde616cd9beb5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034049"
---
# <a name="configure-azure-static-web-apps"></a>Azure Static Web Apps を構成する

Azure Static Web Apps の構成は、次の設定を制御する _staticwebapp.config.json_ ファイルで定義されます。

- ルーティング
- 認証
- 承認
- フォールバック規則
- HTTP 応答のオーバーライド
- グローバル HTTP ヘッダーの定義
- カスタムの MIME の種類

## <a name="file-location"></a>ファイルの場所

_staticwebapp.config.json_ の推奨される場所は、[ワークフロー ファイル](./github-actions-workflow.md)で `app_location` として設定されたフォルダー内です。 ただし、このファイルは、アプリケーションのソース コード フォルダー内の任意の場所に配置できます。

詳細については、「[構成ファイルの例](#example-configuration-file)」を参照してください。

> [!IMPORTANT]
> _staticwebapp.config.json_ がある場合、[_routers.json_ ファイル](./routes.md)は無視されます。

## <a name="routes"></a>ルート

ルート規則を使用すると、アプリケーションから Web へのアクセスを許可する URL のパターンを定義できます。 ルートは、ルーティング規則の配列として定義します。 使用例については、「[構成ファイルの例](#example-configuration-file)」を参照してください。

- 規則は、`routes` 配列に出現する順序で実行されます。
- 規則の評価は最初の一致で停止します。複数のルーティング規則が連結されることはありません。
- カスタム ロール名は完全に制御できます。
  - [`anonymous`](./authentication-authorization.md) と [`authenticated`](./authentication-authorization.md) を含む組み込みロール名がいくつかあります。

ルーティングの懸念事項は、認証 (ユーザーの識別) および承認 (ユーザーへの機能の割り当て) の概念とかなり重複しています。 この記事と共に、[認証と承認](authentication-authorization.md)に関するガイドをお読みください。

静的コンテンツの既定のファイルは、*index.html* ファイルです。

## <a name="defining-routes"></a>ルートの定義

各規則は、ルート パターンと、1 つまたは複数のオプションの規則プロパティで構成されます。 使用例については、「[構成ファイルの例](#example-configuration-file)」を参照してください。

| 規則のプロパティ  | 必須 | 既定値 | 解説                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | はい      | 該当なし          | 呼び出し元によって要求されるルート パターン。<ul><li>ルート パスの末尾には、[ワイルドカード](#wildcards)を使用できます。<ul><li>たとえば、ルート _admin/\*_ は、_admin_ パスの下にあるすべてのルートと一致します。</ul></ul>|
| `rewrite`        | いいえ       | 該当なし          | 要求から返されるファイルまたはパスを定義します。<ul><li>`redirect` 規則に対して相互に排他的です<li>書き換え規則では、ブラウザーの場所は変更されません。<li>値は、アプリのルートを基準にする必要があります</ul>  |
| `redirect`        | いいえ       | 該当なし          | 要求のファイルまたはパスのリダイレクト先を定義します。<ul><li>`rewrite` 規則に対して相互に排他的です。<li>リダイレクト規則では、ブラウザーの場所が変更されます。<li>既定の応答コードは [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (一時的なリダイレクト) ですが、[`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (永続的なリダイレクト) でオーバーライドできます。</ul> |
| `allowedRoles` | いいえ       | anonymous     | ルートにアクセスするために必要なロール名のリストを定義します。 <ul><li>有効な文字は、`a-z`、`A-Z`、`0-9`、`_` です。<li>組み込みロール [`anonymous`](./authentication-authorization.md) は、認証されていないすべてのユーザーに適用されます。<li>組み込みロール [`authenticated`](./authentication-authorization.md) は、ログインしている任意のユーザーに適用されます。<li>ユーザーは、少なくとも 1 つのロールに属している必要があります。<li>ロールは、_OR_ に基づいて照合されます。<ul><li>ユーザーが一覧にあるいずれかのロールに属している場合は、アクセス権が付与されます。</ul><li>個々のユーザーは、[招待](authentication-authorization.md)によってロールに関連付けられます。</ul> |
| `headers`<a id="route-headers"></a> | いいえ | 該当なし | 応答に追加される [HTTP ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers)のセット。 <ul><li>ルート固有のヘッダーが応答内のグローバル ヘッダーと同じ場合、[`globalHeaders`](#global-headers) はルート固有のヘッダーによってオーバーライドされます。<li>ヘッダーを削除するには、値を空の文字列に設定します。</ul> |
| `statusCode`   | いいえ       | `200`、`301`、または `302` (リダイレクト用) | 応答の [HTTP 状態コード](https://developer.mozilla.org/docs/Web/HTTP/Status)。 |
| `methods` | いいえ | すべてのメソッド | ルートに一致する要求メソッドのリスト。 使用可能なメソッドには、`GET`、`HEAD`、`POST`、`PUT`、`DELETE`、`CONNECT`、`OPTIONS`、`TRACE`、`PATCH` があります。 |

各プロパティは、要求または応答パイプラインで特定の目的があります。

| 目的 | Properties |
|---|---|
| ルートと一致させる | `route`, `methods` |
| ルートが一致した後に承認する | `allowedRoles` |
| 規則が一致して承認された後に処理する | `rewrite` (要求を変更する) <br><br>`redirect`、`headers`、`statusCode` (応答を変更する) |

## <a name="securing-routes-with-roles"></a>ロールによるルートのセキュリティ保護

ルートをセキュリティで保護するには、規則の `allowedRoles` 配列に 1 つ以上のロール名を追加します。ユーザーは、[招待](./authentication-authorization.md)を介してカスタム ロールに関連付けられます。 使用例については、「[構成ファイルの例](#example-configuration-file)」を参照してください。

既定では、すべてのユーザーが組み込みの `anonymous` ロールに属し、ログインしているすべてのユーザーが `authenticated` ロールのメンバーになります。

たとえば、認証されたユーザーのみにルートを制限するには、組み込みの `authenticated` ロールを `allowedRoles` 配列に追加します。

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

必要に応じて、`allowedRoles` 配列に新しいロールを作成できます。 ルートを管理者のみに制限するには、`allowedRoles` 配列に `administrator` という名前の独自のロールを定義できます。

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- ロールの名前は完全に制御できます。ロールが従う必要のあるリストはありません。
- 個々のユーザーは、[招待](authentication-authorization.md)によってロールに関連付けられます。

## <a name="wildcards"></a>ワイルドカード

ワイルドカード規則は、ルート パターン内のすべての要求と一致し、パスの末尾でのみサポートされます。また、ファイル拡張子でフィルター処理できます。 使用例については、「[構成ファイルの例](#example-configuration-file)」を参照してください。

たとえば、カレンダー アプリケーションに対するルートを実装するには、1 つのファイルを提供するように、_calendar_ ルートに該当するすべての URL を書き換えることができます。

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

その場合、_calendar.html_ ファイルでは、クライアント側ルーティングを使用して、`/calendar/january/1`、`/calendar/2020`、`/calendar/overview` などの URL のバリエーションに対して異なるビューを提供できます。

ワイルドカードの一致をファイル拡張子でフィルター処理できます。 たとえば、指定したパスの HTML ファイルのみに一致する規則を追加する場合は、次の規則を作成できます。

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

複数のファイル拡張子に基づいてフィルター処理するには、この例に示すように、オプションを中かっこで囲みます。

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

ワイルドカード ルートの一般的なユース ケースには、以下が含まれます。

- パス パターン全体に対して特定のファイルを提供する
- さまざまな HTTP メソッドをパス パターン全体にマップする
- 認証と承認の規則を適用する
- 特殊なキャッシュ規則を実装する

## <a name="fallback-routes"></a>フォールバック ルート

シングル ページ アプリケーションは、多くの場合、クライアント側のルーティングに依存します。 これらのクライアント側ルーティング規則では、要求をサーバーに返さずに、ブラウザーのウィンドウの場所が更新されます。 ページを更新する場合、またはクライアント側ルーティング規則によって生成された URL に直接移動する場合は、適切な HTML ページ (通常はクライアント側アプリの _index.html_) を提供するために、サーバー側フォールバック ルートが必要です。

ファイル フィルターでパス ワイルドカードが使用されている次の例に示すように、フォールバック ルートを実装する規則を使用するようにアプリを構成できます。

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

次のファイル構造の例では、この規則を使用して次の結果を得ることができます。

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| 要求先 | 返されるもの | 状態 |
| --- | --- | --- |
| */about/* | *index.html* ファイル | `200` |
| */images/logo.png* | 画像ファイル  | `200` |
| */images/icon.svg* | */index.html* ファイル。*svg* ファイル拡張子が `/images/*.{png,jpg,gif}` フィルターに含まれていないため   | `200` |
| */images/unknown.png* | "ファイルが見つかりませんでした" エラー  | `404` |
| */css/unknown.css* | "ファイルが見つかりませんでした" エラー  | `404` |
| */css/global.css* | スタイルシート ファイル | `200` |
| */images* または */css* フォルダー外にあるその他のファイル | *index.html* ファイル | `200` |

## <a name="global-headers"></a>グローバル ヘッダー

`globalHeaders` セクションでは、[ルート ヘッダー](#route-headers)規則によってオーバーライドされない限り、各応答に適用される一連の [HTTP ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers)が提供されます。それ以外の場合は、ルートからのヘッダーとグローバル ヘッダーの両方の和集合が返されます。

使用例については、「[構成ファイルの例](#example-configuration-file)」を参照してください。

ヘッダーを削除するには、値を空の文字列 (`""`) に設定します。

グローバル ヘッダーの一般的なユース ケースには、以下が含まれます。

- カスタム キャッシュ ルール
- セキュリティ ポリシーの適用
- エンコードの設定

## <a name="response-overrides"></a>応答のオーバーライド

`responseOverrides` セクションでは、サーバーからエラー コードが返される場合のカスタム応答を定義できます。 使用例については、「[構成ファイルの例](#example-configuration-file)」を参照してください。

次の HTTP コードをオーバーライドできます。

| 状態コード | 説明 | 考えられる原因 |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | 正しくない要求 | 無効な招待リンク |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | 権限がありません | 認証されていない状態での制限されたページへの要求 |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Forbidden |<ul><li>ユーザーはログインしているが、ページを表示するために必要なロールがない。<li>ユーザーはログインしているが、ランタイムが ID 要求からユーザーの詳細を取得できない。<li>カスタム ロールを使用してサイトにログインしているユーザーが多すぎるため、ランタイムでユーザーをログインさせることができない。</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | 見つかりません | ファイルが見つからない |

次の構成例は、エラー コードをオーバーライドする方法を示しています。

```json
{
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html",
            "statusCode": 200
        },
        "401": {
            "statusCode": 302,
            "redirect": "/login"
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html",
            "statusCode": 200
        },
        "404": {
            "rewrite": "/custom-404.html",
            "statusCode": 200
        }
    }
}
```

## <a name="example-configuration-file"></a>構成ファイルの例

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
            "route": "/images/*",
            "headers": {
                "cache-control": "must-revalidate, max-age=15770000"
            }
        },
        {
            "route": "/api/*",
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/api/*",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/customers/contoso",
            "allowedRoles": ["administrator", "customers_contoso"]
        },
        {
            "route": "/login",
            "rewrite": "/.auth/login/github"
        },
        {
            "route": "/.auth/login/twitter",
            "statusCode": 404,
        },
        {
            "route": "/logout",
            "redirect": "/.auth/logout"
        },
        {
            "route": "/calendar/*",
            "rewrite": "/calendar.html"
        },
        {
            "route": "/specials",
            "redirect": "/deals",
            "statusCode": 301
        }
    ],
    "navigationFallback": {
      "rewrite": "index.html",
      "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
    },
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html"
        },
        "401": {
            "redirect": "/login",
            "statusCode": 302
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html"
        },
        "404": {
            "rewrite": "/404.html"
        }
    },
    "globalHeaders": {
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
    },
    "mimeTypes": {
        ".json": "text/json"
    },
}
```

上記の構成に基づいて、次のシナリオを確認します。

| 要求先 | 結果 |
| --- | --- |
| _/profile_ | 認証されたユーザーには、 _/profile/index.html_ ファイルが提供されます。 認証されていないユーザーは _/login_ にリダイレクトされます。 |
| _/admin/_ | _administrator_ ロールの認証されたユーザーには、 _/admin/index.html_ ファイルが提供されます。 _administrator_ ロールに属していない認証されたユーザーには、`403` エラーが返されます<sup>1</sup>。 認証されていないユーザーは _/login_ にリダイレクトされます。 |
| _/logo.png_ | 最長有効期間が 182 日 (15,770,000 秒) を少し超えるカスタム キャッシュ規則を使用して画像を提供します。 |
| _/api/admin_ | _registeredusers_ ロールの認証されたユーザーからの `GET` 要求は、API に送信されます。 _registeredusers_ ロールに属していない認証されたユーザー、および認証されていないユーザーには、`401` エラーが返されます。<br/><br/>_administrator_ ロールの認証されたユーザーからの `POST`、`PUT`、`PATCH`、および `DELETE` 要求は、API に送信されます。 _administrator_ ロールに属していない認証されたユーザー、および認証されていないユーザーには、`401` エラーが返されます。 |
| _/customers/contoso_ | _administrator_ または _customers\_contoso_ ロールに属している認証されたユーザーには、 _/customers/contoso/index.html_ ファイルが提供されます。 _administrator_ または _customers\_contoso_ ロールに属していない認証されたユーザーには、`403` エラーが返されます<sup>1</sup>。 認証されていないユーザーは _/login_ にリダイレクトされます。 |
| _/login_ | 認証されていないユーザーは、GitHub で認証するように求められます。 |
| _/.auth/login/twitter_ | Twitter での承認がルート規則によって無効になっているため、`404` エラーが返されます。これにより、フォールバックされ、`200` 状態コードと共に _/index.html_ が提供されます。 |
| _/logout_ | ユーザーは、すべての認証プロバイダーからログアウトされます。 |
| _/calendar/2021/01_ | ブラウザーには、 _/calendar.html_ ファイルが提供されます。 |
| _/specials_ | ブラウザーは、永続的に _/deals_ にリダイレクトされます。 |
| _/data.json_ | MIME の種類 `text/json` で提供されるファイル。 |
| _/about_、またはクライアント側のルーティング パターンに一致する任意のフォルダー | _/index.html_ ファイルは、`200` 状態コードと共に提供されます。 |
| _/Images/_ フォルダー内に存在しないファイル | `404` エラー。 |

<sup>1</sup> [応答のオーバーライド規則](#response-overrides)を使用することにより、カスタム エラー ページを提供できます。

## <a name="restrictions"></a>制限

_staticwebapps.config.json_ ファイルには次の制限があります。

- 最大ファイル サイズは 100 KB
- 最大 50 種類のロール

一般的な制限事項と限度については、[クォータに関する記事](quotas.md)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証と承認を設定する](authentication-authorization.md)
