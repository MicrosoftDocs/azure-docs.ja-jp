---
title: Azure Media Player のプラグインの作成
description: JavaScript を使用して Azure Media Player のプラグインを作成する方法について説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: c1a64bff8b81735d9c4c9a14d2c1e12bd0bfe57e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91296163"
---
# <a name="writing-plugins-for-azure-media-player"></a>Azure Media Player のプラグインの作成 #

プラグインは、プレーヤーの機能を拡張または強化するために作成される JavaScript です。 Azure Media Player の外観や機能を変更するプラグインや、他のサービスとのインターフェイスを持つプラグインを作成できます。 これは 2 つの簡単な手順で行うことができます。

## <a name="step-1"></a>手順 1 ##

次のような関数で JavaScript を作成します。

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

コードは HTML ページに `<script>` タグで囲んで直接作成することや、外部の JavaScript ファイル内に作成することができます。 後者を実行する場合は、AMP スクリプトの "*後*" に HTML ページの `<head>` に JavaScript ファイルを必ず含めるようにしてください。

例:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>手順 2. ##
次の 2 つの方法のいずれかで、JavaScript を使用してプラグインを初期化します。

方法 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

方法 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

プラグイン オプションは必須ではありません。これらを含めると、プラグインを使用する開発者は、ソース コードを変更しなくても動作を構成できます。

プラグインの作成に関するヒントやその他の例については、[ギャラリー](azure-media-player-plugin-gallery.md)をご覧ください。

>[!NOTE]
> プラグイン コードは、視聴者のプレーヤー エクスペリエンスの有効期間中に DOM 内の項目を動的に変更します。プレーヤーのソース コードに永続的な変更を加えることはありません。 ここでは、ブラウザーの開発者ツールについて理解しておくと便利です。 たとえば、プレーヤーの要素の外観を変更する場合は、その HTML 要素をそのクラス名で検索し、そこから属性を追加または変更することができます。 [HTML 属性の変更](http://www.w3schools.com/js/js_htmldom_html.asp)に関する優れたリソースを示します。

### <a name="integrated-plugins"></a>統合プラグイン ###

 現在、 [time-tip](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) と [hotkeys](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html) の 2 つのプラグインが AMP に組み込まれています。 これらのプラグインは、最初はプレーヤー用のモジュール式のプラグインとして開発されましたが、現在はプレーヤーのソース コードに含まれています。

### <a name="plugin-gallery"></a>プラグイン ギャラリー ###

[プラグイン ギャラリー](https://aka.ms/ampplugins)には、タイムライン マーカー、ズーム、分析などの機能についてコミュニティが既に提供しているいくつかのプラグインがあります。 このページでは、プラグインへのアクセス、プラグインを設定するための手順、およびプラグインの動作を示すデモが提供されます。 このギャラリーに含めるべき優れたプラグインを作成された場合は、Microsoft で確認できるように遠慮なく送信してください。

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)
