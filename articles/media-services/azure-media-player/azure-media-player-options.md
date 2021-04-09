---
title: Azure Media Player のオプション
description: Azure Media Player の埋め込みコードは単なる HTML5 の video タグであるため、多くのオプションでは、標準のタグ属性を使用してオプションを設定できます。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366872"
---
# <a name="options"></a>Options #

## <a name="setting-options"></a>オプションの設定 ##

Azure Media Player の埋め込みコードは単なる HTML5 の video タグであるため、多くのオプションでは、標準のタグ属性を使用してオプションを設定できます。

`<video controls autoplay ...>`

または、data-setup 属性を使用して、[JSON](http://json.org/example.html) 形式でオプションを指定することもできます。 これは、video タグに標準ではないオプションを設定する方法でもあります。

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

最後に、プレーヤーのセットアップをトリガーするために data-setup 属性を使用していない場合、JavaScript セットアップ関数の 2 番目の引数として、プレーヤー オプションを指定してオブジェクトを渡すことができます。

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> コンストラクターのオプションは、ソースが設定される前に最初の初期化時にのみ設定されます。  初期化された同じ Azure Media Player 要素のオプションを変更する場合は、ソースを変更する前にオプションを更新する必要があります。 JavaScript のオプションは、`myPlayer.options({/*updated options*/});` を使用して更新できます。 変更されたオプションのみが影響を受け、それ以外の事前に設定されたオプションはすべて保持されることに注意してください。

## <a name="individual-options"></a>個別のオプション ##

> [!NOTE]
>video タグ属性は true または false (ブール値) にのみ設定できます。オンにするには属性 (等号なし) を含め、オフにするにはそれを除外します。 たとえば、controls をオンにする場合、`<video controls="true" ...>` は誤りで、正しい指定は `<video controls ...>` です。ユーザーが直面する最大の問題は、値として false を使用してこれらの値を false に設定しようとすることです (例: controls = "false")。このようにすると、属性がまだ含まれているため、実際には逆の操作となり、値は true に設定されます。

### <a name="controls"></a>controls ###

controls オプションは、ユーザーが操作できるコントロールをプレーヤーが持つかどうかを設定します。 コントロールを使用しない場合、動画再生を開始する唯一の方法は、自動再生属性または API を使用することです。

`<video controls ...>` または `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

autoplay が true の場合、 (ユーザーの操作なしに) ページが読み込まれるとすぐに動画の再生が開始されます。

> [!NOTE]
> このオプションは、Windows Phone、Apple iOS、Android などのモバイル デバイスではサポートされていません。 モバイル デバイスでは、利用者の月次データプラン (多くの場合は高額) の使い過ぎを防ぐために自動再生機能がブロックされています。 この場合、動画を開始するにはユーザーのタッチ/クリックが必要です。

`<video autoplay ...>` または `{ "autoplay": true }`

### <a name="poster"></a>poster ###
poster 属性は、動画の再生が開始される前に表示されるイメージを設定します。 これは多くの場合、動画のフレームまたはカスタムのタイトル画面です。 ユーザーが再生をクリックするとすぐに、このイメージは消えます。

`<video poster="myPoster.jpg" ...>` または `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

width 属性は、動画の表示の幅を設定します。

`<video width="640" ...>` または `{ "width": 640 }`

### <a name="height"></a>height ###

height 属性は、動画の表示の高さを設定します。

`<video height="480" ...>` または `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

plugins JSON では、AMP のインスタンスでどのプラグインが読み込まれるかが決定され、プラグインが持つオプションを構成することができます。

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

プラグインの開発と使用の詳細については、[プラグインの作成](azure-media-player-writing-plugins.md)に関する記事を参照してください。

### <a name="other-options"></a>その他のオプション ###

その他のオプションは、JSON を受け取る `data-setup` パラメーターを使用して、`<video>` タグに設定できます。
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

これは明示的に false に設定されます。 false に設定することで、Azure Media Player のスキンをプラットフォーム間で同じようにレンダリングできます。  また、名前に反してタッチは引き続き有効になります。

### <a name="fluid"></a>fluid ###

このオプションを true に設定すると、動画要素は親コンテナーの全幅を占め、標準の 16:9 縦横比で動画に合わせて高さが調整されます。

`<video ... data-setup='{"fluid": true}'>`

`fluid` オプションは、明示的な `width` と `height` の設定をオーバーライドします。 このオプションは Azure Media Player バージョン `2.0.0` 以降でのみ使用できます。

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` オプションは、playbackSpeed コントロールと、ユーザーが使用できる再生速度設定のセットを制御します。 `playbackSpeed` はオブジェクトを受け取ります。 コントロール バーの再生速度制御を有効にするには、オブジェクトのプロパティ `enabled` を true に設定する必要があります。 マークアップで再生速度を有効にする例を次に示します。

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


`playbackSpeed` 設定のその他のプロパティは [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) オブジェクトで指定されます。

JavaScript で再生速度オプションを設定する例:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

このオプションは Azure Media Player バージョン 2.0.0 以降でのみ使用できます。

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

`staleDataTimeLimitInSec` オプションは、mediaSource バッファーに何秒分の古いデータを保持するかを構成するための最適化機能です。 この機能は、既定では無効になっています。

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

enabled を true に設定すると、ライブ ストリームとライブ アーカイブに ライブ CEA キャプションを表示できます。 label 属性は必須ではありません。含まれていない場合、プレーヤーでは既定のラベルが使用されます。

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

このオプションは Azure Media Player バージョン 2.1.1 以降でのみ使用できます。

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)
