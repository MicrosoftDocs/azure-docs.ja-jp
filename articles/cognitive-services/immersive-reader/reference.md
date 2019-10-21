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
ms.openlocfilehash: b25a002cb1e2563ab97a2081c6b6a05362b66779
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338511"
---
# <a name="immersive-reader-sdk-reference"></a>Immersive Reader SDK リファレンス

Immersive Reader SDK は、イマーシブ リーダーを Web アプリケーションに統合するための JavaScript ライブラリです。

# <a name="functions"></a>Functions

SDK では、次の関数が公開されています。

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Web アプリケーションの `iframe` 内でイマーシブ リーダーを起動します。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>parameters

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| `token` | string | Azure AD 認証トークン。 [Azure AD の認証方法](./azure-active-directory-authentication.md)に関するページを参照してください。 |
| `subdomain` | string | Azure 内のイマーシブ リーダー リソースのカスタム サブドメイン。 [Azure AD の認証方法](./azure-active-directory-authentication.md)に関するページを参照してください。 |
| `content` | [コンテンツ](#content) | イマーシブ リーダーで表示するコンテンツを含むオブジェクト。 |
| `options` | [オプション](#options) | イマーシブ リーダーの特定の動作を構成するオプション。 省略可能。 |

### <a name="returns"></a>戻り値

イマーシブ リーダーが読み込まれたときに解決される `Promise<HTMLDivElement>` を返します。 `Promise` は `div` 要素に解決され、この要素の唯一の子はイマーシブ リーダーページ を含む `iframe` 要素です。

### <a name="exceptions"></a>例外

イマーシブ リーダーの読み込みに失敗した場合、返された `Promise` は [`Error`](#error) オブジェクトで拒否されます。 詳細については、「[エラー コード](#error-codes)」を参照してください。

## <a name="close"></a>close

イマーシブ リーダーを閉じます。

この関数のユース ケースの例には、```hideExitButton: true```オプション[で ](#options) を設定して終了ボタンを非表示にする場合があります。 その後、別のボタン (たとえば、モバイル ヘッダーの戻る矢印) がクリックされたときに、この ```close``` 関数を呼び出すことができます。

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

この関数は、ドキュメントのイマーシブ リーダー ボタン要素のスタイル設定と更新を行います。 ```options.elements``` が指定されている場合、この関数により ```options.elements``` 内にボタンがレンダリングされます。 それ以外の場合は、クラス ```immersive-reader-button``` を持つドキュメントの要素内にボタンがレンダリングされます。

この関数は、ウィンドウの読み込み時に SDK によって自動的に呼び出されます。

レンダリング オプションの詳細については、「[省略可能な属性](#optional-attributes)」を参照してください。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>parameters

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | renderButtons 関数の特定の動作を構成するためのオプション。 省略可能。 |

## <a name="types"></a>型

### <a name="content"></a>コンテンツ

イマーシブ リーダーで表示するコンテンツを含みます。

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>チャンク

1 つのデータ チャンク。イマーシブ リーダーのコンテンツに渡されます。

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>サポートされている MIME タイプ

| MIME タイプ | 説明 |
| --------- | ----------- |
| text/plain | プレーンテキスト。 |
| text/html | HTML コンテンツ。 [詳細情報](#html-support)|
| application/mathml+xml | Mathematical Markup Language (MathML)。 [詳細情報](https://developer.mozilla.org/en-US/docs/Web/MathML)。
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word の .docx 形式のドキュメント。

### <a name="html-support"></a>HTML サポート
| HTML | サポートされているコンテンツ |
| --------- | ----------- |
| フォント スタイル | 太字、斜体、下線、コード、取り消し線、上付き文字、下付き文字 |
| 順不同のリスト | ディスク、円、正方形 |
| 順序指定済みリスト | 小数点、大文字アルファベット、小文字アルファベット、大文字ローマ字、小文字ローマ字 |
| ハイパーリンク | 近日対応予定 |

サポートされていないタグが同等にレンダリングされます。 イメージとテーブルは現在サポートされていません。

### <a name="options"></a>オプション

イマーシブ リーダーの特定の動作を構成するプロパティを含みます。

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

イマーシブ リーダー ボタンをレンダリングするためのオプション。

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
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
| `data-locale` | ロケールを設定します。 たとえば、`en-US` または `fr-FR` です。 既定値は英語 `en` です。 |
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