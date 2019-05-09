---
title: Azure Media Clipper の概要 | Microsoft Docs
description: AMS アセットからビデオ クリップを作成するためのツールである Azure Media Clipper の概要
services: media-services
keywords: クリップ;サブクリップ;エンコード;メディア
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51848b9ba4d18b3ac7d652cfbd97cab6b85f2ee8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842364"
---
# <a name="create-clips-with-azure-media-clipper"></a>Azure Media Clipper を使用したクリップの作成
このセクションでは、Azure Media Clipper の使用を開始する基本的な手順を示します。 以降のセクションで、Azure Media Clipper の構成方法の詳細を提供します。

- 最初に、Azure Media Player と Azure Media Clipper の以下のリンクを自分のドキュメントの先頭に追加します。 Clipper と Azure Media Player のバージョンを URL に明示的に指定することをお勧めします。 これらのリソースの最新バージョンは必要に応じて変更されることがあるため、運用環境では使用しないでください。

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- 次に、Clipper をインスタンス化する div 要素に以下のクラスを追加します。

```javascript
<div id="root" class="azure-subclipper" />
```

必要に応じて、ダーク テーマを有効にするには、ダーク スキン クラスを追加します。

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- 次に、以下の API 呼び出しで Clipper をインスタンス化します。

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

初期化メソッド呼び出しのパラメーターは次のとおりです。
- `selector`{必須、文字列}:ウィジェットをレンダリングする、一致した HTML 要素の CSS セレクター。
- `restVersion`{必須、文字列}:対象とする Azure Media Services REST API バージョン。 REST バージョンは、ウィジェットによって生成される出力の形式を定義します。 現時点では、2.0 のみがサポートされています。
- `submitSubclipCallback`{必須、promise} ウィジェットの [送信] ボタンがクリックされたときに呼び出されるコールバック関数。 このコールバック関数では、ウィジェットによって生成される出力 (レンダー ジョブ構成やフィルター定義) を予期します。 詳細については、サブクリップ送信コールバックに関するページをご覧ください。
- `logLevel`{省略可能、{'info', 'warn', 'error'}}:ブラウザーのコンソールに表示されるログ記録レベル。 既定値: error
- `minimumMarkerGap` {省略可能、int}:サブクリップの最小サイズ (秒単位)。 注: この値は 6 (既定値) 以上である必要があります。
- `singleBitrateMp4Profile` {省略可能、JSON オブジェクト} ウィジェットで生成されたレンダー ジョブの構成に使用する、シングル ビットレート MP4 プロファイル。 指定されていない場合は、[既定のシングルビットレート MP4 プロファイル](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p)が使用されます。
- `multiBitrateMp4Profile` {省略可能、JSON オブジェクト} ウィジェットで生成されたレンダー ジョブの構成に使用する、マルチ ビットレート MP4 プロファイル。 指定されていない場合は、[既定のマルチビットレート MP4 プロファイル](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p)が使用されます。
- `keymap` {省略可能、JSON オブジェクト} ウィジェットのキーボード ショートカットのカスタマイズを可能にします。 詳細については、[カスタマイズ可能なキーボード ショートカット](media-services-azure-media-clipper-keyboard-shortcuts.md)に関するページをご覧ください。
- `assetsPanelLoaderCallback`{省略可能、promise} ユーザーがウィンドウの下部までスクロールするたびに、資産ウィンドウに資産の新しいページを (非同期で) 読み込むように呼び出されるコールバック関数。 詳細については、資産ウィンドウ ローダー コールバックに関するページをご覧ください。
- `height` {省略可能、数字} ウィジェット全体の高さ (高さの最小値は、資産ウィンドウなしで 600 px、資産ウィンドウありで 850 px)。
- `subclippingMode`(省略可能、{'all', 'render', 'filter'}):サブクリップ モードを許可します。 既定値は all です。
- `filterAssetsTypes`(省略可能、ブール値): filterAssetsTypes を使用すると、資産ウィンドウでフィルター ドロップダウン リストを表示したり隠したりできます。 既定値は true です。
- `speedLevels` (省略可能、配列): speedLevels を使用すると、ビデオ プレーヤーに異なる速度レベルを設定できます。詳細については、[Azure Media Player ドキュメント](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions)に関するページをご覧ください。
- `resetOnJobDone`(省略可能、ブール値): resetOnJobDone を使用すると、ジョブが正常に送信された場合に Clipper がサブクリッパーを初期状態にリセットできます。
- `autoplayVideo`(省略可能、ブール値): autoplayVideo を使用すると、Clipper が読み込まれたビデオを自動再生できます。 既定値は true です。
- `language` {省略可能、文字列}: language を指定すると、ウィジェットの言語が設定されます。 指定しない場合、ウィジェットはブラウザーの言語に基づいてメッセージをローカライズします。 ブラウザーの言語が検出されない場合、ウィジェットの既定は英語となります。 詳しくは、「[ローカライズの構成](media-services-azure-media-clipper-localization.md)」セクションをご覧ください。
- `languages`{省略可能、JSON}: languages パラメーターを指定すると、言語の既定の辞書が、ユーザーが定義したカスタム辞書に置き換えられます。 詳しくは、「[ローカライズの構成](media-services-azure-media-clipper-localization.md)」セクションをご覧ください。
- `extraLanguages`(省略可能、JSON): extraLanguages パラメーターを指定すると、既定の辞書に新しい言語が追加されます。 詳しくは、「[ローカライズの構成](media-services-azure-media-clipper-localization.md)」セクションをご覧ください。

## <a name="typescript-definition"></a>TypeScript 定義
Clipper の [TypeScript](https://www.typescriptlang.org/) 定義ファイルは、[ここ](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts)にあります。

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
このセクションでは、Clipper によって提供される API サーフェスを説明します。

- `ready(handler)`: Clipper が完全に読み込まれて使用できるようになったら直ちに JavaScript を実行する手段を提供します。
- `load(assets)`: ウィジェットのタイムラインに資産の一覧を読み込みます (assetsPanelLoaderCallback と共に使うことはできません)。 Clipper に資産を読み込む方法の詳細については、[この記事](media-services-azure-media-clipper-load-assets.md)をご覧ください。
- `setLogLevel(level)`: ブラウザーのコンソールに表示されるログ記録レベルを設定します。 指定できる値: `info`、`warn`、および `error`。
- `setHeight(height)` ウィジェット全体の高さをピクセル単位で設定します (高さの最小値は、資産ウィンドウなしで 600 px、資産ウィンドウありで 850 px)。
- `version`: ウィジェットのバージョンを取得します。

## <a name="next-steps"></a>次の手順
Azure Media Clipper の構成については、次のステップをご覧ください。
- [Azure Media Clipper への資産の読み込み](media-services-azure-media-clipper-load-assets.md)
- [カスタム キーボード ショートカットの構成](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Clipper からのクリッピング ジョブの送信](media-services-azure-media-clipper-submit-job.md)
- [ローカライズの構成](media-services-azure-media-clipper-localization.md)