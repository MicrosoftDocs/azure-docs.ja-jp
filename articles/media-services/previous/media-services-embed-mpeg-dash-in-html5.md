---
title: DASH.js を使用した HTML5 アプリケーションへの MPEG-DASH アダプティブ ストリーミング ビデオの埋め込み | Microsoft Docs
description: このトピックでは、DASH.js を使用した HTML5 アプリケーションに MPEG-DASH アダプティブ ストリーミング ビデオを埋め込む方法を説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 820c10a2f5bb43ff931027954b7ecf10c5b4c59f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013569"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>DASH.js を使用した HTML5 アプリケーションへの MPEG-DASH アダプティブ ストリーミング ビデオの埋め込み

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

## <a name="overview"></a>概要
MPEG DASH は、高品質なアダプティブ ビデオ ストリーミング出力を配信する必要がある開発者に多くのメリットを提供する、ビデオ コンテンツのアダプティブ ストリーミングの ISO 標準です。 MPEG DASH では、ネットワークが混雑すると、ビデオ ストリームが自動的に低解像度に調整されます。 これにより、次の数秒の再生分がプレーヤーにダウンロードされている間に、"一時停止" されたビデオが視聴者に表示される可能性が低くなります (つまり、バッファーリングの可能性が低くなります)。 ネットワークの混雑が緩和されると、ビデオ プレーヤーは高品質ストリームに戻ります。 この機能は必要な帯域幅に対応するもので、結果的にビデオの開始時間が高速化されます。 つまり、最初の数秒は高速にダウンロードされる低品質のセグメントで再生され、十分なコンテンツがバッファリングされると、より高い品質にステップアップします。

Dash.js は JavaScript で記述された、オープン ソースの MPEG-DASH ビデオ プレーヤーです。 ビデオ再生機能を必要とするアプリケーションで自由に再使用できる、堅牢なクロスプラットフォーム プレイヤーを提供することが目的です。 W3C Media Source Extensions (MSE) をサポートするどのブラウザーでも MPEG-DASH を再生できるようになります。現在では Chrome、Microsoft Edge、IE11 がこれに該当します (他のブラウザーも今後 MSE に対応する予定です)。 DASH.js の詳細については、「jGitHub dash.js リポジトリ」をご覧ください。

## <a name="creating-a-browser-based-streaming-video-player"></a>ブラウザー ベースのストリーミング ビデオ プレーヤーの作成
再生、一時停止、巻き戻しなどのコントロールを備えたビデオ プレーヤーを表示するシンプルな Web ページを作成するには:

1. HTML ページを作成します
2. ビデオ タグを追加します
3. Dash.js プレーヤーを追加します
4. プレイヤーを初期化します
5. CSS スタイルを追加します
6. MSE を実装するブラウザーに結果を表示します

プレーヤーの初期化は、数行の JavaScript コードで実行できます。 dash.js を使用すると、ブラウザー ベースのアプリケーションに MPEG-DASH ビデオを簡単に埋め込むことができます。

## <a name="creating-the-html-page"></a>HTML ページの作成
最初の手順では、**ビデオ** 要素を含む標準の HTML ページを作成し、以下の例のようにこのファイルを basicplayer.html に保存します。

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>DASH.js プレーヤーの追加
dash.js リファレンス実装をアプリケーションに追加するには、最新バージョンの dash.js プロジェクトから dash.all.js ファイルを入手する必要があります。 このファイルはアプリケーションの JavaScript フォルダーに保存します。 このファイルは、必要なすべての dash.js コードを 1 つのファイルにまとめることのできる便利なファイルです。 dash.js リポジトリを参照すると、個別のファイルやテスト コードなどさまざまな情報が見つかりますが、dash.js の使用のみが目的であれば、必要になるのは dash.all.js ファイルのみです。

dash.js プレーヤーをアプリケーションを追加するには、basicPlayer.html のヘッド セクションにスクリプト タグを追加します。

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

次に、ページの読み込み時にプレーヤーを初期化する関数を作成します。 dash.all.js を読み込む行の後ろに次のスクリプトを追加します。

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

この関数では、まず DashContext が作成されます。 これは、特定のランタイム環境でのアプリケーションの構成に使用されます。 技術的な観点では、この関数はアプリケーションの構築時に依存関係挿入フレームワークが使用するクラスを定義します。 ほとんどの場合、Dash.di.DashContext を使用します。

次に、dash.js フレームワークの MediaPlayer のプライマリ クラスをインスタンス化します。 このクラスには、再生や一時停止などの必要なコア メソッドが含まれ、ビデオ要素との関係の管理や再生するビデオを示す Media Presentation Description (MPD) ファイルの解釈が管理されます。

プレーヤーがビデオを再生する準備ができていることを確認するため、MediaPlayer クラスの startup() 関数が呼び出されます。 特に、その関数では、すべての必要な (コンテキストで定義されたとおりの) クラスが正常に読み込まれたことを確認できます。 プレイヤーの準備が整ったら、attachView() 関数を使用して、ビデオ要素をアタッチします。 スタートアップ関数により、MediaPlayer がビデオ ストリームを要素に挿入し、必要に応じて、再生をコントロールすることもできます。

MediaPlayer に MPD ファイルの URL を渡して、再生予定のビデオについて通知します。 作成した setupVideo() 関数は、ページを完全に読み込んだ後に実行する必要があります。 この操作は、Body 要素の OnLoad イベントを使用して行います。 `<body>` 要素を次に変更します。

```html
    <body onload="setupVideo()">
```

最後に、CSS を使用してビデオ要素のサイズを設定します。 アダプティブ ストリーミング環境では、ネットワークの状況に応じて再生するビデオのサイズが変化するため、この操作は特に重要です。 以下の簡単なデモでは、次の CSS をページのヘッド セクションに追加して、ビデオ要素を利用可能なブラウザー ウィンドウの 80% に設定します。

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>ビデオの再生
ビデオを再生するには、basicPlayback.html ファイルでブラウザーをポイントし、表示されるビデオ プレーヤーで再生をクリックします。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>参照

[GitHub dash.js リポジトリ](https://github.com/Dash-Industry-Forum/dash.js) 

