---
title: Immersive Reader SDK リファレンス
titleSuffix: Azure Cognitive Services
description: Immersive Reader SDK のリファレンス
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 1d9fc20055fe3adb571b5a77330cc6537998cb5f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534473"
---
# <a name="immersive-reader-sdk-reference"></a>Immersive Reader SDK リファレンス

Immersive Reader SDK は、イマーシブ リーダーを Web アプリケーションに統合するための JavaScript ライブラリです。

## <a name="functions"></a>Functions

SDK は 1 つの関数 `ImmersiveReader.launchAsync(token, subdomain, content, options)` を公開します。

### <a name="launchasync"></a>launchAsync

Web アプリケーションの `iframe` 内でイマーシブ リーダーを起動します。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>parameters

| Name | Type | 説明 |
| ---- | ---- |------------ |
| `token` | string | Azure AD 認証トークン。 [Azure AD の認証方法](./azure-active-directory-authentication.md)に関するページを参照してください。 |
| `subdomain` | string | Azure 内のイマーシブ リーダー リソースのカスタム サブドメイン。 [Azure AD の認証方法](./azure-active-directory-authentication.md)に関するページを参照してください。 |
| `content` | [コンテンツ](#content) | イマーシブ リーダーで表示するコンテンツを含むオブジェクト。 |
| `options` | [オプション](#options) | イマーシブ リーダーの特定の動作を構成するオプション。 省略可能。 |

#### <a name="returns"></a>戻り値

イマーシブ リーダーが読み込まれたときに解決される `Promise<HTMLDivElement>` を返します。 `Promise` は `div` 要素に解決され、この要素の唯一の子はイマーシブ リーダーページ を含む `iframe` 要素です。

#### <a name="exceptions"></a>例外

イマーシブ リーダーの読み込みに失敗した場合、返された `Promise` は [`Error`](#error) オブジェクトで拒否されます。 詳細については、「[エラー コード](#error-codes)」を参照してください。

## <a name="types"></a>型

### <a name="content"></a>コンテンツ

イマーシブ リーダーで表示するコンテンツを含みます。

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>サポートされている MIME タイプ

| MIME タイプ | 説明 |
| --------- | ----------- |
| text/plain | プレーンテキスト。 |
| application/mathml+xml | Mathematical Markup Language (MathML)。 [詳細情報](https://developer.mozilla.org/en-US/docs/Web/MathML)。
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word の .docx 形式のドキュメント。

### <a name="options"></a>オプション

イマーシブ リーダーの特定の動作を構成するプロパティを含みます。

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Error

エラーに関する情報が含まれます。

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>エラー コード

| コード | 説明 |
| ---- | ----------- |
| BadArgument | 与えられた引数が無効です。詳細は `message` を参照してください。 |
| タイムアウト | 指定されたタイムアウト時間内にイマーシブ リーダーを読み込めませんでした。 |
| TokenExpired | 与えられたトークンの期限が切れています。 |
| Throttled | 呼び出しレートの制限を超えました。 |

## <a name="launching-the-immersive-reader"></a>イマーシブ リーダーの起動

SDK は、イマーシブ リーダーの起動用ボタンに既定のスタイルを提供します。 このスタイルを有効にするには、`immersive-reader-button` クラス属性を使用します。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>省略可能な属性

ボタンのルック アンド フィールを構成するには、次の属性を使用します。

| Attribute | 説明 |
| --------- | ----------- |
| `data-button-style` | ボタンのスタイルを設定します。 `icon`、`text`、または `iconAndText` を指定できます。 既定値は `icon` です。 |
| `data-locale` | ロケール (例: `en-US`、`fr-FR`) を設定します。 既定値は English です。 |
| `data-icon-px-size` | アイコンのサイズをピクセル単位で設定します。 既定値は 20px です。 |

## <a name="browser-support"></a>ブラウザーのサポート

イマーシブ リーダーのエクスペリエンスを最適化するには、次のブラウザーの最新バージョンを使用してください。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>次の手順

* [GitHub 上の Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) を探索する
* [クイック スタート:イマーシブ リーダーを起動する Web アプリを作成する (C#)](./quickstart.md)