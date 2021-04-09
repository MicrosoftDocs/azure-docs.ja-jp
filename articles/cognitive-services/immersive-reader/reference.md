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
ms.author: metang
ms.openlocfilehash: f2f5c8193454a3b7fa6be1cea7a1236b613d6c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92636529"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Immersive Reader JavaScript SDK リファレンス (v1.1)

イマーシブ リーダー SDK には、お客様のアプリケーションにイマーシブ リーダーを統合させる JavaScript ライブラリが含まれています。

## <a name="functions"></a>関数

SDK では、次の関数が公開されています。

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Web アプリケーションの `iframe` 内でイマーシブ リーダーを起動します。 コンテンツのサイズは最大 50 MB に制限されていることに注意してください。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| `token` | string | Azure AD 認証トークン。 詳細については、[イマーシブ リーダー リソースの作成方法](./how-to-create-immersive-reader.md)に関するページを参照してください。 |
| `subdomain` | string | Azure 内のイマーシブ リーダー リソースのカスタム サブドメイン。 詳細については、[イマーシブ リーダー リソースの作成方法](./how-to-create-immersive-reader.md)に関するページを参照してください。 |
| `content` | [コンテンツ](#content) | イマーシブ リーダーで表示するコンテンツを含むオブジェクト。 |
| `options` | [[オプション]](#options) | イマーシブ リーダーの特定の動作を構成するオプション。 省略可能。 |

#### <a name="returns"></a>戻り値

イマーシブ リーダーが読み込まれたときに解決される `Promise<LaunchResponse>` を返します。 `Promise` は [`LaunchResponse`](#launchresponse) オブジェクトに解決されます。

#### <a name="exceptions"></a>例外

イマーシブ リーダーの読み込みに失敗した場合、返された `Promise` は [`Error`](#error) オブジェクトで拒否されます。 詳細については、「[エラー コード](#error-codes)」を参照してください。

<br>

## <a name="close"></a>close

イマーシブ リーダーを閉じます。

この関数のユース ケースの例には、```hideExitButton: true```オプション[で ](#options) を設定して終了ボタンを非表示にする場合があります。 その後、別のボタン (たとえば、モバイル ヘッダーの戻る矢印) がクリックされたときに、この ```close``` 関数を呼び出すことができます。

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>イマーシブ リーダーの起動ボタン

SDK は、イマーシブ リーダーの起動用ボタンに既定のスタイルを提供します。 このスタイルを有効にするには、`immersive-reader-button` クラス属性を使用します。 詳細については、「[イマーシブ リーダーのボタンをカスタマイズする方法](./how-to-customize-launch-button.md)」を参照してください。

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>省略可能な属性

ボタンのルック アンド フィールを構成するには、次の属性を使用します。

| 属性 | 説明 |
| --------- | ----------- |
| `data-button-style` | ボタンのスタイルを設定します。 `icon`、`text`、または `iconAndText` を指定できます。 既定値は `icon` です。 |
| `data-locale` | ロケールを設定します。 たとえば、`en-US` または `fr-FR` です。 既定値は英語 `en` です。 |
| `data-icon-px-size` | アイコンのサイズをピクセル単位で設定します。 既定値は 20px です。 |

<br>

## <a name="renderbuttons"></a>renderButtons

「[イマーシブ リーダーのボタンをカスタマイズする方法](./how-to-customize-launch-button.md)」のガイダンスを使用している場合は、```renderButtons``` 関数は必要ありません。

この関数は、ドキュメントのイマーシブ リーダー ボタン要素のスタイル設定と更新を行います。 ```options.elements``` を指定した場合、ボタンは ```options.elements``` で指定した各要素内にレンダリングされます。 ドキュメント内にイマーシブ リーダーを起動するセクションが複数あり、同じスタイルで複数のボタンを簡単にレンダリングしたい場合、またはシンプルな同じデザイン パターンでボタンをレンダリングしたい場合は、```options.elements``` パラメーターを使用すると便利です。 この関数を [renderButtons オプション](#renderbuttons-options)のパラメーターと共に使用するには、次のコード スニペットで示すように、ページの読み込み時に ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` を呼び出します。 そうしないと、「[イマーシブ リーダーのボタンをカスタマイズする方法](./how-to-customize-launch-button.md)」で示されているように、```immersive-reader-button``` クラスが含まれるドキュメントの要素内にボタンがレンダリングされます。

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

レンダリング オプションの詳細については、上の「[省略可能な属性](#optional-attributes)」を参照してください。 これらのオプションを使用するには、いずれかのオプション属性をページの HTML の各 ```HTMLDivElement``` に追加します。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| `options` | [renderButtons のオプション](#renderbuttons-options) | renderButtons 関数の特定の動作を構成するためのオプション。 省略可能。 |

### <a name="renderbuttons-options"></a>renderButtons のオプション

イマーシブ リーダー ボタンをレンダリングするためのオプション。

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>renderButtons のオプションのパラメーター

| 設定 | Type | 説明 |
| ------- | ---- | ----------- |
| 要素 | HTMLDivElement[] | イマーシブ リーダー ボタンを内部にレンダリングする要素。 |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

`ImmersiveReader.launchAsync` の呼び出しからの応答を含みます。 イマーシブ リーダーを含む `iframe` のリファレンスには、`container.firstChild` を使用してアクセスできます。

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse のパラメーター

| 設定 | Type | 説明 |
| ------- | ---- | ----------- |
| container | HTMLDivElement | イマーシブ リーダーの iframe が含まれる HTML 要素。 |
| sessionID | String | このセッションのグローバル一意識別子。デバッグに使用されます。 |
 
## <a name="error"></a>エラー

エラーに関する情報が含まれます。

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>エラーのパラメーター

| 設定 | Type | 説明 |
| ------- | ---- | ----------- |
| code | String | エラー コードのセットの1つ。 [エラー コード](#error-codes)に関するページを参照してください。 |
| message | String | 人が判読できるエラーの表現。 |

#### <a name="error-codes"></a>エラー コード

| コード | 説明 |
| ---- | ----------- |
| BadArgument | 指定した引数が無効です。「[エラー](#error)」の `message` パラメーターを参照してください。 |
| タイムアウト | 指定されたタイムアウト時間内にイマーシブ リーダーを読み込めませんでした。 |
| TokenExpired | 与えられたトークンの期限が切れています。 |
| Throttled | 呼び出しレートの制限を超えました。 |

<br>

## <a name="types"></a>型

### <a name="content"></a>コンテンツ

イマーシブ リーダーで表示するコンテンツを含みます。

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Content のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| title | String | イマーシブ リーダーの上部に表示されるタイトルのテキスト (省略可能) |
| チャンク | [Chunk[]](#chunk) | チャンクの配列 |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>チャンク

1 つのデータ チャンク。イマーシブ リーダーのコンテンツに渡されます。

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Chunk のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| コンテンツ | String | イマーシブ リーダーに送信されるコンテンツが含まれる文字列。 |
| lang | String | テキストの言語。値は IETF BCP 47 の言語タグ形式です (例: en、es-ES)。 指定しないと、言語は自動的に検出されます。 [サポートされている言語](#supported-languages)を参照してください。 |
| mimeType | string | プレーンテキスト、MathML、HTML、Microsoft Word DOCX の形式がサポートされています。 詳細については、「[サポートされている MIME タイプ](#supported-mime-types)」を参照してください。 |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>サポートされている MIME タイプ

| MIME タイプ | 説明 |
| --------- | ----------- |
| text/plain | プレーンテキスト。 |
| text/html | HTML コンテンツ。 [詳細情報](#html-support)|
| application/mathml+xml | Mathematical Markup Language (MathML)。 [詳細については、こちらを参照してください](./how-to/display-math.md)。
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word の .docx 形式のドキュメント。


<br>

## <a name="options"></a>Options

イマーシブ リーダーの特定の動作を構成するプロパティを含みます。

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Options のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| uiLang | String | UI の言語。値は IETF BCP 47 の言語タグ形式です (例: en、es-ES)。 指定しない場合の既定値はブラウザーの言語です。 |
| timeout | 番号 | [launchAsync](#launchasync) がタイムアウト エラーで失敗するまでの期間 (ミリ秒単位) (既定値は 15000 ミリ秒)。 このタイムアウトは、リーダー ページの初回起動にのみ適用されます。リーダー ページが開いてスピナーが開始すると、成功と見なされます。 タイムアウトを調整する必要はありません。 |
| uiZIndex | 番号 | 作成される iframe の Z インデックス (既定値は 1000)。 |
| useWebview | Boolean| Chrome アプリとの互換性のため、iframe ではなく webview タグを使用します (既定値は false)。 |
| onExit | 機能 | イマーシブ リーダーが終了したときに実行されます。 |
| allowFullscreen | Boolean | 全画面表示を切り替える機能 (既定値は true)。 |
| hideExitButton | Boolean | イマーシブ リーダーの終了ボタンの矢印を非表示にするかどうか (既定値は false)。 イマーシブ リーダーを終了するための代替メカニズム (モバイル ツール バーの戻る矢印など) がある場合にのみ、これを true にする必要があります。 |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | イマーシブ リーダーでの Cookie の使用の設定 (既定値は *CookiePolicy.Disable*)。 EU の Cookie コンプライアンス ポリシーに従って必要なユーザーの同意を取得するのは、ホスト アプリケーションの責任となります。 「[CookiePolicy のオプション](#cookiepolicy-options)」を参照してください。 |
| disableFirstRun | Boolean | 最初の実行のエクスペリエンスを無効にします。 |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | 音声読み上げを構成するためのオプション。 |
| translationOptions | [TranslationOptions](#translationoptions) | 翻訳を構成するためのオプション。 |
| displayOptions | [DisplayOptions](#displayoptions) | テキスト サイズ、フォントなどを構成するためのオプション。 |
| preferences | String | イマーシブ リーダーでのユーザーの個人設定を表す、onPreferencesChanged から返される文字列。 詳細については、「[設定のパラメーター](#settings-parameters)」と「[ユーザー設定を格納する方法](./how-to-store-user-preferences.md)」を参照してください。 |
| onPreferencesChanged | 機能 | ユーザーの個人設定が変更されたときに実行されます。 詳細については、「[ユーザー設定を格納する方法](./how-to-store-user-preferences.md)」を参照してください。 |
| customDomain | String | 内部使用のために予約されています。 イマーシブ リーダーの webapp がホストされているカスタム ドメイン (既定値は null)。 |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **重要** イマーシブ リーダー アプリケーションとの間で送受信される `-preferences` の文字列の値を、プログラムで変更しようとしないでください。予期しない動作が発生し、顧客のユーザー エクスペリエンスが低下する可能性があります。 ホスト アプリケーションによってカスタム値を割り当てたり、`-preferences` 文字列を操作したりしないでください。 `-preferences` 文字列オプションを使用する場合は、`-onPreferencesChanged` コールバック オプションから返された正確な値のみを使用してください。

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>ReadAloudOptions のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| voice | String | 音声。"Female" または "Male"。 すべての言語で両方の性別がサポートされているわけではないことに注意してください。 |
| 速度 | 番号 | 再生速度。0.5 から 2.5 の範囲で指定する必要があります。 |
| autoPlay | Boolean | イマーシブ リーダーが読み込まれたら、読み上げを自動的に開始します。 |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> ブラウザーの制限により、Safari では自動再生はサポートされていません。

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>TranslationOptions のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| language | String | 翻訳言語を設定します。値は IETF BCP 47 の言語タグ形式です (例: fr-FR、es-MX、zh-Hans-CN)。 単語またはドキュメントの翻訳を自動的に有効にするために必要です。 |
| autoEnableDocumentTranslation | Boolean | ドキュメント全体を自動的に翻訳します。 |
| autoEnableWordTranslation | Boolean | 単語の翻訳を自動的に有効にします。 |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>DisplayOptions のパラメーター

| 名前 | Type | 説明 |
| ---- | ---- |------------ |
| textSize | 番号 | 選択されたテキストのサイズを設定します。 |
| increaseSpacing | Boolean | テキストの間隔をオンまたはオフに切り替えるかどうかを設定します。 |
| fontFamily | String | 選択されたフォントを設定します ("Calibri"、"ComicSans"、または "Sitka")。 |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>CookiePolicy のオプション

```typescript
enum CookiePolicy { Disable, Enable }
```

**以下の設定は、情報の提供のみを目的としています**。 イマーシブ リーダーの設定 (またはユーザーの個人設定) は、Cookie に格納されます。 この *cookiePolicy* オプションを使用すると、EU の Cookie コンプライアンス法に準拠するため、Cookie の使用が既定で **無効** になります。 Cookie を再度有効にし、イマーシブ リーダーのユーザー個人設定の既定の機能を元に戻す必要がある場合は、Web サイトまたはアプリケーションでユーザーから Cookie を有効にする適切な同意を得たことを確認する必要があります。 その後、イマーシブ リーダーで Cookie を再び有効にするには、イマーシブ リーダーを起動するときに、*cookiePolicy* オプションを明示的に *CookiePolicy.Enable* に設定する必要があります。 次の表では、*cookiePolicy* オプションが有効になっている場合に、イマーシブ リーダーによって Cookie に格納される設定を示します。

#### <a name="settings-parameters"></a>設定のパラメーター

| 設定 | Type | 説明 |
| ------- | ---- | ----------- |
| textSize | 番号 | 選択されたテキストのサイズを設定します。 |
| fontFamily | String | 選択されたフォントを設定します ("Calibri"、"ComicSans"、または "Sitka")。 |
| textSpacing | 番号 | テキストの間隔をオンまたはオフに切り替えるかどうかを設定します。 |
| formattingEnabled | Boolean | HTML の書式設定をオンまたはオフに切り替えるかどうかを設定します。 |
| テーマ | String | 選択されたテーマを設定します ("Light"、"Dark" など)。 |
| syllabificationEnabled | Boolean | 分節法をオンまたはオフに切り替えるかどうかを設定します。 |
| nounHighlightingEnabled | Boolean | 名詞の強調表示をオンまたはオフに切り替えるかどうかを設定します。 |
| nounHighlightingColor | String | 選択された名詞の強調表示の色を設定します。 |
| verbHighlightingEnabled | Boolean | 動詞の強調表示をオンまたはオフに切り替えるかどうかを設定します。 |
| verbHighlightingColor | String | 選択された動詞の強調表示の色を設定します。 |
| adjectiveHighlightingEnabled | Boolean | 形容詞の強調表示をオンまたはオフに切り替えるかどうかを設定します。 |
| adjectiveHighlightingColor | String | 選択された形容詞の強調表示の色を設定します。 |
| adverbHighlightingEnabled | Boolean | 副詞の強調表示をオンまたはオフに切り替えるかどうかを設定します。 |
| adverbHighlightingColor | String | 選択された副詞の強調表示の色を設定します。 |
| pictureDictionaryEnabled | Boolean | 絵辞書をオンまたはオフに切り替えるかどうかを設定します。 |
| posLabelsEnabled | Boolean | 強調表示されている各品詞の上付きテキスト ラベルをオンまたはオフに切り替えるかどうかを設定します。  |

<br>

## <a name="supported-languages"></a>サポートされている言語

イマーシブ リーダーの翻訳機能では、多くの言語がサポートされています。 詳細については、[言語サポート](./language-support.md)に関するページを参照してください。

<br>

## <a name="html-support"></a>HTML サポート

書式設定が有効になっている場合、次のコンテンツはイマーシブ リーダーで HTML としてレンダリングされます。

| HTML | サポートされているコンテンツ |
| --------- | ----------- |
| フォント スタイル | 太字、斜体、下線、コード、取り消し線、上付き文字、下付き文字 |
| 順不同のリスト | ディスク、円、正方形 |
| 順序指定済みリスト | 小数点、大文字アルファベット、小文字アルファベット、大文字ローマ字、小文字ローマ字 |

サポートされていないタグが同等にレンダリングされます。 イメージとテーブルは現在サポートされていません。

<br>

## <a name="browser-support"></a>ブラウザーのサポート

イマーシブ リーダーのエクスペリエンスを最適化するには、次のブラウザーの最新バージョンを使用してください。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>次のステップ

* [GitHub 上の Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) を探索する
* [クイック スタート: イマーシブ リーダーを起動する Web アプリを作成する (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
