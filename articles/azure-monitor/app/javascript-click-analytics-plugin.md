---
title: Application Insights JavaScript SDK 用のクリック分析自動収集プラグイン
description: Application Insights JavaScript SDK 用のクリック分析自動収集プラグインをインストールして使用する方法。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: e48d669321ad8c58681e8a92e68f2089962bdc17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102429852"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 用のクリック分析自動収集プラグイン

このプラグインを使用すると、Web ページのクリック イベントが自動的に追跡され、HTML 要素の data-* 属性を使用してイベント テレメトリにデータが設定されます。

## <a name="getting-started"></a>作業の開始

ユーザーは、npm を使用してクリック分析自動収集プラグインを設定できます。

### <a name="npm-setup"></a>npm の設定

npm パッケージをインストールします。

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>このプラグインを効果的に使用する方法

1. クリック イベントから生成されたテレメトリ データは、Azure portal の [Application Insights] セクションに `customEvents` として格納されます。
2. customEvent の `name` は、次の規則に基づいて設定されます。
    1.  `data-*-id` で指定された `id` は customEvent 名として使用されます。 たとえば、クリックされた HTML 要素に属性 "data-sample-id"="button1" が含まれている場合は、"button1" が customEvent 名になります。
    2. このような属性が存在せず、構成で `useDefaultContentNameOrId` が `true` に設定されている場合は、クリックされた要素の HTML 属性 `id` またはその要素のコンテンツ名が customEvent 名として使用されます。
    3. `useDefaultContentNameOrId` が false である場合、customEvent 名は "not_specified" になります。

    > [!TIP]
    > 意味のあるデータを生成するために、`useDefaultContentNameOrId` を true に設定することをお勧めします。  
3. `parentDataTag` は次の 2 つの処理を行います。
    1. このタグが存在する場合、このプラグインでは、クリックされた要素のすべての親 HTML 要素から `data-*` 属性と値をフェッチします。
    2. 効率を向上させるために、このプラグインではこのタグをフラグとして使用し、これを検出すると、DOM (ドキュメント オブジェクト モデル) のそれ以上の上向きの処理を停止します。
    
    > [!CAUTION]
    > `parentDataTag` が使用されると、SDK により、使用した HTML 要素だけでなく、アプリケーション全体で親タグの検索が開始されます。
4. ユーザーによって指定される `customDataPrefix` は、常に `data-` (`data-sample-` など) で始まる必要があります。 HTML では、`data-*` グローバル属性によって、カスタム データ属性と呼ばれる属性のクラスが形成されます。これにより、HTML と、スクリプトによるその DOM 表現の間で機密情報を交換できるようになります。 以前のブラウザー (Internet Explorer、Safari) では、`data-` で始まっていない限り、認識できない属性を削除します。

    `data-*` の `*` は、[XML 名の生成規則](https://www.w3.org/TR/REC-xml/#NT-Name)に従った任意の名前で置き換えることができます。ただし、次の制限があります。
    - この名前を "xml" で始めることはできません (大文字と小文字のどちらであっても)。
    - この名前にセミコロン (U+003A) を含めることはできません。
    - この名前に大文字を含めることはできません。

## <a name="configuration"></a>構成

| 名前                  | Type                               | Default | 説明                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | true    | 自動キャプチャの構成。                                                                                                         |
| コールバック (callback)              | [IValueCallback](#ivaluecallback)  | null    | コールバックの構成。                                                                                                                 |
| pageTags              | string                             | null    | ページ タグ。                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | クリック データのキャプチャに使用される既定のタグをオーバーライドするために指定されるカスタム データ タグ。                                                           |
| urlCollectHash        | boolean                            | false   | URL の "#" 文字の後の値のログ記録を有効にします。                                                                          |
| urlCollectQuery       | boolean                            | false   | URL のクエリ文字列のログ記録を有効にします。                                                                                      |
| behaviorValidator     | 機能                           | null  | `data-*-bhvr` 値の検証に使用するコールバック関数。 詳細については、[behaviorValidator のセクション](#behaviorvalidator)に移動してください。|
| defaultRightClickBhvr | 文字列 (または) 数値                 | ''      | [右クリック] イベントが発生したときの [既定の動作] 値。 この値は、要素に `data-*-bhvr` 属性が含まれている場合にオーバーライドされます。 |
| dropInvalidEvents     | boolean                            | false   | 有効なクリック データがないイベントを削除するためのフラグ。                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| 名前               | Type     | Default | 説明                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | 機能 | null    | 既定の pageName キャプチャ動作をオーバーライドするための関数。                           |
| pageActionPageTags | 機能 | null    | pageAction イベント中に収集される既定の pageTags を拡張するためのコールバック関数。  |
| contentName        | 機能 | null    | カスタマイズされた contentName を設定するためのコールバック関数。                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| 名前                      | Type    | Default   | HTML で使用する既定のタグ |   説明                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | 該当なし         |特定の要素が既定の customDataPrefix でタグ付けされていない場合、または customDataPrefix がユーザーによって指定されていない場合、contentName の標準の HTML 属性を収集します。 |
| customDataPrefix          | string  | `data-`   | `data-*`| 指定されたプレフィックスでタグ付けされている要素のコンテンツ名と値を自動キャプチャします。 たとえば、HTML タグ内で `data-*-id`、`data-<yourcustomattribute>` を使用できます。   |
| aiBlobAttributeTag        | string  | `ai-blob` |  `data-ai-blob`| プラグインでは、個々の `data-*` 属性ではなく JSON BLOB 属性がサポートされています。 |
| metaDataPrefix            | string  | null      | N/A  | キャプチャ時に指定されたプレフィックスを持つ HTML 見出しのメタ要素名とコンテンツを自動キャプチャします。 たとえば、HTML メタ タグ内で `custom-` を使用できます。 |
| captureAllMetaDataContent | boolean | false     | 該当なし   | すべての HTML 見出しのメタ要素名とコンテンツを自動キャプチャします。 既定値は false です。 有効になっている場合は、指定された metaDataPrefix がオーバーライドされます。 |
| parentDataTag             | string  | null      |  N/A  | このタグが検出されると、要素のコンテンツ名と値をキャプチャするための DOM の走査を停止します。 たとえば、HTML タグ内で `data-<yourparentDataTag>` を使用できます。|
| dntDataTag                | string  | `ai-dnt`  |  `data-ai-dnt`| この属性を含む HTML 要素は、テレメトリ データをキャプチャするためにこのプラグインによって無視されます。|

### <a name="behaviorvalidator"></a>behaviorValidator

behaviorValidator 関数を使用すると、コード内のタグ付けされた動作が、事前に定義された一覧に準拠しているかどうかを自動的に確認できます。 これにより、タグ付けされた動作を、企業が設定した分類と確実に一致させることができます。 Azure Monitor のほとんどの顧客がこれを使用することは求められておらず、それは予測されていませんが、これは高度なシナリオに使用できます。 この拡張機能の一部として公開されている behaviorValidator のコールバック関数には 3 種類あります。 ただし、公開されている関数によって要件が解決されない場合、ユーザーは独自のコールバック関数を使用できます。 その意図はユーザー独自の動作のデータ構造を定義することにあり、このプラグインでは、データ タグから動作を抽出するときにこの検証関数を使用します。

| 名前                   | [説明]                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | このコールバック関数は、動作のデータ構造が文字列の配列である場合に使用します。|
| BehaviorMapValidator   | このコールバック関数は、動作のデータ構造がディクショナリである場合に使用します。       |
| BehaviorEnumValidator  | このコールバック関数は、動作のデータ構造が列挙型である場合に使用します。            |

#### <a name="sample-usage-with-behaviorvalidator"></a>behaviorValidator の使用例

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>サンプル アプリ

[クリック分析自動収集プラグインが有効になっている単純な Web アプリ](https://go.microsoft.com/fwlink/?linkid=2152871)。

## <a name="next-steps"></a>次の手順

- クリック分析自動収集プラグインについては、[GitHub リポジトリ](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js)と [NPM パッケージ](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js)を参照してください。
- [使用法エクスペリエンスでのイベントの分析](usage-segmentation.md)を使用して上位のクリックを分析し、使用可能なディメンションでスライスします。
- [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query) の CustomEvents テーブルの customDimensions 属性内のコンテンツ フィールドでクリック データを見つけます。 その他のガイダンスについては、[サンプル アプリ](https://go.microsoft.com/fwlink/?linkid=2152871)に関するページを参照してください。
- [ブック](../visualize/workbooks-overview.md)を作成するか [Power BI にエクスポート](../logs/log-powerbi.md#integrating-queries)して、クリック データのカスタム視覚化を作成します。
