---
title: Azure Media Player API のメソッド
description: Azure Media Player API を使用すると、ブラウザーでビデオの再生に HTML5 ビデオ、Flash、Silverlight、またはその他のサポートされている再生テクノロジを使用しているかどうかに関係なく、JavaScript を介してビデオを操作できます。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81725908"
---
# <a name="api"></a>API #

Azure Media Player API を使用すると、ブラウザーでビデオの再生に HTML5 ビデオ、Flash、Silverlight、またはその他のサポートされている再生テクノロジを使用しているかどうかに関係なく、JavaScript を介してビデオを操作できます。

## <a name="referencing-the-player"></a>プレーヤーの参照 ##

API 関数を使用するには、プレーヤー オブジェクトにアクセスする必要があります。 幸い取得は簡単です。 ビデオ タグに ID があることを確認するだけです。 埋め込みコード例の ID は `vid1` です。 1 つのページに複数のビデオがある場合は、すべてのビデオ タグに一意の ID があることを確認します。

`var myPlayer = amp('vid1');`

> [!NOTE]
> プレーヤーがまだ data-setup 属性または別のメソッドを介して初期化されていない場合は、プレーヤーも初期化されます。

## <a name="wait-until-the-player-is-ready"></a>プレーヤーの準備が整うまで待機する ##

Azure Media Player がビデオと API をセットアップするためにかかる時間は、使用されている再生テクノロジによって異なります。 多くの場合、HTML5 は Flash や Silverlight よりも読み込みがはるかに高速です。 そのため、プレーヤーの API を必要とするコードをトリガーするには、プレーヤーの 'ready' 関数を使用する必要があります。

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OR

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API のメソッド ##

準備ができたプレーヤーにアクセスできるようになったので、ビデオの制御、値の取得、ビデオ イベントへの応答を行うことができます。 Azure Media Player API の関数名は、[HTML5 Media API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html) に準じて名付けられています。 主な違いは、ゲッター関数とセッター関数がビデオ プロパティに使用されることです。

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>イベントの登録 ##
すべてのイベントをアプリケーションに適切に報告するには、プレーヤーを初めて初期化した直後にイベントを登録するようにします。また、準備完了イベントの外部で実行するようにします。

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>次のステップ ##

<!---Some context for the following links goes here--->
- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)