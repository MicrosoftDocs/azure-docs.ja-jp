---
title: Open Source Media Framework 用スムーズ ストリーミング プラグイン
description: Adobe Open Source Media Framework 用 Azure Media Services スムーズ ストリーミング プラグインを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 70042a5c4c27ec5d163e5f05089ae50b92140c28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011257"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Adobe Open Source Media Framework 用 Microsoft スムーズ ストリーミング プラグインを使用する方法

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>概要
Open Source Media Framework 2.0 用 Microsoft スムーズ ストリーミング プラグイン (OSMF 用 SS) は、OSMF の既定の機能を拡張し、新規または既存の OSMF プレーヤーに Microsoft スムーズ ストリーミング コンテンツ再生機能を追加します。 また、このプラグインは、Strobe Media Playback (SMP) にもスムーズ ストリーミング再生機能を追加します。

OSMF 用 SS には、次に示す 2 つのバージョンのプラグインが含まれています。

* OSMF 用 Static スムーズ ストリーミング プラグイン (.swc)
* OSMF 用 Dynamic スムーズ ストリーミング プラグイン (.swf)

このドキュメントでは、OSMF および OSMF プラグインに関する一般的な実務知識を持つ読者を想定しています。OSMF の詳細については、 [OSMF の公式サイト](http://osmf.org/)にあるドキュメントを参照してください。

### <a name="smooth-streaming-plugin-for-osmf-20"></a>OSMF 2.0 用スムーズ ストリーミング プラグイン
このプラグインは、オンデマンド スムーズ ストリーミング コンテンツの読み込みおよび再生を次の機能でサポートしています。

* オンデマンド スムーズ ストリーミング再生 (再生、一時停止、シーク、停止)
* ライブ スムーズ ストリーミング再生 (再生)
* ライブ DVR 機能 (一時停止、シーク、DVR 再生、Go-to-Live)
* ビデオ コーデック (H.264) のサポート
* オーディオ コーデック (AAC) のサポート
* OSMF ビルトイン API による複数オーディオ言語切り替え
* OSMF ビルトイン API による再生時の最高画質選択
* OSMF キャプション プラグインによるサイドカー クローズド キャプション
* Adobe&reg; Flash&reg; Player 11.4 以上。
* このバージョンでは OSMF 2.0 のみをサポート

## <a name="supported-features-and-known-issues"></a>サポートされている機能と既知の問題
サポートされる機能、サポートされていない機能、および既知の問題の一覧については、「 [このドキュメント](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/)を参照してください。

## <a name="loading-the-plugin"></a>プラグインの読み込み
OSMF プラグインは、静的 (コンパイル時) または動的 (実行時) に読み込むことができます。 OSMF 用スムーズ ストリーミング プラグインのダウンロードには、静的バージョンと動的バージョンの両方が含まれています。

* 静的読み込み: 静的に読み込むには、静的ライブラリ (SWC) ファイルが必要です。 静的プラグインは参照としてプロジェクトに追加され、コンパイル時に最終的な出力ファイル内部でマージされます。
* 動的読み込み: 動的に読み込むには、プリコンパイル済み (SWF) ファイルが必要です。 動的プラグインはランタイムに読み込まれ、プロジェクト出力(コンパイル済み出力) には含まれません。 動的プラグインは HTTP および FILE プロトコルを使用して読み込むことができます。

静的読み込みと動的読み込みの詳細については、 [OSMF 公式サイトのプラグインに関するページ](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)を参照してください。

### <a name="ss-for-osmf-static-loading"></a>OSMF 用 SS の静的読み込み
次のコード スニペットは、OSMF 用 SS プラグインを静的に読み込み、OSMF MediaFactory クラスを使用して基本的なビデオを再生する方法を示しています。 OSMF 用 SS をコードに含める前に、プロジェクト参照に静的プラグイン "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" が含まれていることを確認してください。

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>OSMF 用 SS の動的読み込み
次のコード スニペットは、OSMF 用 SS プラグインを動的に読み込み、OSMF MediaFactory クラスを使用して基本的なビデオを再生する方法を示しています。 OSMF 用 SS をコードに含める前に、動的プラグイン "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" をプロジェクト フォルダーにコピー (FILE プロトコルを使用して読み込む場合) するか、Web サーバーにコピー (HTTP プロトコルを使用して読み込む場合) してください。 プロジェクト参照に "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" を含める必要はありません。

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe Media Playback と OSMF 用 SS 動的プラグイン
OSMF 用スムーズ ストリーミング動的プラグインには、 [Strobe Media Playback (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/)との互換性があります。 OSMF 用 SS プラグインを使用すると、スムーズ ストリーミング コンテンツ再生機能を SMP に追加することができます。 これには、"MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" を Web サーバーにコピーし、次に示す手順を使用して HTTP 読み込みを行ってください。

1. [Strobe Media Playback セットアップ ページ](http://osmf.org/dev/2.0gm/setup.html)に移動します。 
2. [src] をスムーズ ストリーミング ソース (例: http:\//devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) に設定します。 
3. 必要な構成変更を行い、[Preview and Update] をクリックします。
   
   **注** : コンテンツ Web サーバーには有効な crossdomain.xml が必要です。 
4. コードをコピーし、好みのテキスト エディターで作成した HTML ページに、次の例のようにコードを貼り付けます。

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. OSMF 用スムーズ ストリーミング プラグインを埋め込みコードに追加して保存します。
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. HTML ページを保存して、Web サーバーに発行します。 Flash&reg; Player 対応の好みのインターネット ブラウザー (Internet Explorer、Chrome、Firefox など) を使用して、発行済みの Web ページに移動します。
3. Adobe&reg; Flash&reg; Player でスムーズ ストリーミング コンテンツをお楽しみください。

全般的な OSMF 開発の詳細については、 [OSMF 公式サイトの開発に関するページ](http://osmf.org/resources.html)を参照してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>参照
[OSMF を更新するためのMicrosoft Adaptive Streamingプラグイン](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

