---
title: Immersive Reader SDK リファレンス
titleSuffix: Azure Cognitive Services
description: イマーシブ リーダー SDK には、お客様のアプリケーションにイマーシブ リーダーを統合させる JavaScript ライブラリが含まれています。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 6dfcd8d56232f893f881f310b33f3f849e2364a7
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2020
ms.locfileid: "85475953"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Immersive Reader JavaScript SDK リファレンス (v1.1)

イマーシブ リーダー SDK には、お客様のアプリケーションにイマーシブ リーダーを統合させる JavaScript ライブラリが含まれています。

## <a name="functions"></a>関数

SDK では、次の関数が公開されています。

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Web アプリケーションの `iframe` 内でイマーシブ リーダーを起動します。 コンテンツのサイズは最大 50 MB に制限されていることに注意してください。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>パラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| `token` | string | Azure AD 認証トークン。 |
| `subdomain` | string | Azure 内のイマーシブ リーダー リソースのカスタム サブドメイン。 |
| `content` | [コンテンツ](#content) | イマーシブ リーダーで表示するコンテンツを含むオブジェクト。 |
| `options` | [[オプション]](#options) | イマーシブ リーダーの特定の動作を構成するオプション。 省略可能。 |

### <a name="returns"></a>戻り値

イマーシブ リーダーが読み込まれたときに解決される `Promise<LaunchResponse>` を返します。 `Promise` は [`LaunchResponse`](#launchresponse) オブジェクトに解決されます。

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

### <a name="parameters"></a>パラメーター

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
| application/mathml+xml | Mathematical Markup Language (MathML)。 [詳細については、こちらを参照してください](./how-to/display-math.md)。
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word の .docx 形式のドキュメント。

### <a name="options"></a>Options

イマーシブ リーダーの特定の動作を構成するプロパティを含みます。

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000).
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits.
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
    disableFirstRun?: boolean; // Disable the first run experience.
    readAloudOptions?: ReadAloudOptions; // Options to configure Read Aloud.
    translationOptions?: TranslationOptions; // Options to configure translation.
    displayOptions?: DisplayOptions; // Options to configure text size, font, etc.
    preferences?: string; // String returned from onPreferencesChanged representing the user's preferences in the Immersive Reader.
    onPreferencesChanged?: (value: string) => any; // Executes when the user's preferences have changed.
}
```

```typescript
enum CookiePolicy { Disable, Enable }
```

```typescript
type ReadAloudOptions = {
    voice?: string;      // Voice, either 'male' or 'female'. Note that not all languages support both genders.
    speed?: number;      // Playback speed, must be between 0.5 and 2.5, inclusive.
    autoplay?: boolean;  // Automatically start Read Aloud when the Immersive Reader loads.
};
```

> [!NOTE]
> ブラウザーの制限により、Safari では自動再生はサポートされていません。

```typescript
type TranslationOptions = {
    language: string;                         // Set the translation language, e.g. fr-FR, es-MX, zh-Hans-CN. Required to automatically enable word or document translation.
    autoEnableDocumentTranslation?: boolean;  // Automatically translate the entire document.
    autoEnableWordTranslation?: boolean;      // Automatically enable word translation.
};
```

```typescript
type DisplayOptions = {
    textSize?: number;          // Valid values are 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96.
    increaseSpacing?: boolean;  // Set whether increased spacing is enabled.
    fontFamily?: string;        // Valid values are 'Calibri', 'ComicSans', and 'Sitka'.
};
```

### <a name="launchresponse"></a>LaunchResponse

`ImmersiveReader.launchAsync` の呼び出しからの応答を含みます。 イマーシブ リーダーを含む `iframe` のリファレンスには、`container.firstChild` を使用してアクセスできます。

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```
 
### <a name="error"></a>エラー

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

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

イマーシブ リーダー ボタンをレンダリングするためのオプション。

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

## <a name="launching-the-immersive-reader"></a>イマーシブ リーダーの起動

SDK は、イマーシブ リーダーの起動用ボタンに既定のスタイルを提供します。 このスタイルを有効にするには、`immersive-reader-button` クラス属性を使用します。 詳細については、 [この記事](./how-to-customize-launch-button.md) を参照してください。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>省略可能な属性

ボタンのルック アンド フィールを構成するには、次の属性を使用します。

| 属性 | 説明 |
| --------- | ----------- |
| `data-button-style` | ボタンのスタイルを設定します。 `icon`、`text`、または `iconAndText` を指定できます。 既定値は `icon` です。 |
| `data-locale` | ロケールを設定します。 たとえば、`en-US` または `fr-FR` です。 既定値は英語 `en` です。 |
| `data-icon-px-size` | アイコンのサイズをピクセル単位で設定します。 既定値は 20px です。 |

## <a name="html-support"></a>HTML サポート

| HTML | サポートされているコンテンツ |
| --------- | ----------- |
| フォント スタイル | 太字、斜体、下線、コード、取り消し線、上付き文字、下付き文字 |
| 順不同のリスト | ディスク、円、正方形 |
| 順序指定済みリスト | 小数点、大文字アルファベット、小文字アルファベット、大文字ローマ字、小文字ローマ字 |

サポートされていないタグが同等にレンダリングされます。 イメージとテーブルは現在サポートされていません。

## <a name="browser-support"></a>ブラウザーのサポート

イマーシブ リーダーのエクスペリエンスを最適化するには、次のブラウザーの最新バージョンを使用してください。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>次のステップ

* [GitHub 上の Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) を探索する
* [クイック スタート: イマーシブ リーダーを起動する Web アプリを作成する (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
