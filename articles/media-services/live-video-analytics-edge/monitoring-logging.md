---
title: 監視とログ記録 - Azure
description: この記事では、Live Video Analytics on IoT Edge の監視とログ記録の概要について説明します。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e1f31c6bb3ea344286ad9af89417ca9f8fd59527
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934295"
---
# <a name="monitoring-and-logging"></a>監視およびログ記録

この記事では、リモート監視のために Live Video Analytics on IoT Edge モジュールからイベントを受信する方法について説明します。 

また、モジュールによって生成されるログを制御する方法についても説明します。

## <a name="taxonomy-of-events"></a>イベントの分類

Live Video Analytics on IoT Edge では、次の分類に従ってイベントまたはテレメトリ データが出力されます。

![Live Video Analytics on IoT Edge のスキーマ](./media/telemetry-schema/taxonomy.png)

* 操作: ユーザーによって実行されるアクションの一部として、または[メディア グラフ](media-graph-concept.md)の実行中に生成されるイベント。
   
   * ボリューム: 低くなると予想される (1 分間に数回、またはさらに低い割合)。
   * 例 :

      記録開始 (下記)、記録停止
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 診断: 問題やパフォーマンスに関する問題を診断するのに役立つイベント。

   * ボリューム: 高くなる可能性あり (1 分間に数回)。
   * 例 :
   
      RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 情報 (下記)、または受信ビデオ フィードのギャップ。

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 分析: ビデオ分析の一部として生成されるイベント。

   * ボリューム: 高くなる可能性あり (1 分間に数回以上)。
   * 例 :
      
      モーションが検出された (以下)、推論の結果。
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
モジュールによって出力されたイベントは [IoT Edge ハブ](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)に送信され、そこから他の送信先にルーティングできます。 

### <a name="timestamps-in-analytic-events"></a>分析イベントのタイムスタンプ
上記のとおり、ビデオ分析の一部として生成されたイベントには、タイムスタンプが関連付けられています。 グラフ トポロジの一部として[ライブ ビデオを記録した](video-recording-concept.md)場合は、このタイムスタンプを使用すると、記録したビデオ内のどこで特定のイベントが発生したかを見つけることができます。 次に示すのは、[Azure Media Service 資産](terminology.md#asset)に記録されたビデオのタイムラインに分析イベントのタイムスタンプをマップする方法に関するガイドラインです。

まず、`eventTime` 値を抽出します。 [時間範囲フィルター](playback-recordings-how-to.md#time-range-filters)でこの値を使用して、記録から適切な部分を取得します。 たとえば、`eventTime` の 30 秒前に開始し、30 秒後に終了するビデオを取り込むことができます。 上記の例では、`eventTime` が 2020-05-12T23:33:09.381Z の場合、+/- 30 秒間の時間枠に対する HLS マニフェストの要求は次のようになります。
```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```
上記の URL は、メディアのプレイリストが含まれる、いわゆる[マスター プレイリスト](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)を返します。 メディアのプレイリストには、次のようなエントリが含まれます。

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
上記のエントリは、タイムスタンプ値 `143039375031270` で開始するビデオ フラグメントが使用可能であることを報告しています。 分析イベントの `timestamp` 値には、メディアのプレイリストと同じタイムスケールが使用されます。また、この値を使用して、関連するビデオ フラグメントの識別、および適切なフレームのシークを行うことができます。

詳細については、HLS でのフレームに正確なシークに関して数多くある[記事](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS)のいずれかを参照してください。

## <a name="controlling-events"></a>イベントの制御

[モジュール ツインの JSON スキーマ](module-twin-configuration-schema.md)に記載されているように、次のモジュール ツイン プロパティを使用して、Live Video Analytics on IoT Edge モジュールによって発行された操作イベントと診断イベントを制御できます。

`diagnosticsEventsOutputName` – モジュールから診断イベントを取得するには、このプロパティに (任意の) 値を含めて指定します。 モジュールによる診断イベントの発行を止めるには、これを省略するか、空のままにします。
   
`operationalEventsOutputName` – モジュールから操作イベントを取得するには、このプロパティに (任意の) 値を含めて指定します。 モジュールによる操作イベントの発行を止めるには、これを省略するか、空のままにします。
   
分析イベントは、モーション検出プロセッサや HTTP 拡張プロセッサなどのノードによって生成され、それらは IoT ハブ シンクを使用して IoT Edge Hub に送信されます。 

[上記のすべてのイベントのルーティング](../../iot-edge/module-composition.md#declare-routes)は、$EdgeHub モジュール ツインの必要なプロパティ (配置マニフェスト内) を使用して制御できます。

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

上の例では、lvaEdge は Live Video Analytics on IoT Edge モジュールの名前で、ルーティング規則は[ルートの宣言](../../iot-edge/module-composition.md#declare-routes)で定義されているスキーマに従います。

> [!NOTE]
> 分析イベントが IoT Edge Hub に確実に到着するようにするには、任意のモーション検出プロセッサ ノードや任意の HTTP 拡張機能プロセッサ ノードの下流に IoT ハブ シンクノードがある必要があります。

## <a name="event-schema"></a>イベント スキーマ

イベントは Edge デバイス上で生成され、Edge またはクラウドで使用できます。 Live Video Analytics on IoT Edge によって生成されるイベントは、Azure IoT Hub によって確立された[ストリーミング メッセージング パターン](../../iot-hub/iot-hub-devguide-messages-construct.md) (システム プロパティ、アプリケーション プロパティ、および本文を含む) に準拠しています。

### <a name="summary"></a>まとめ

IoT Hub によって観察されたすべてのイベントには、以下に示す一連の共通プロパティがあります。

|プロパティ   |プロパティの種類| データ型   |説明|
|---|---|---|---|
|message-id |システム |guid|  一意のイベント ID。|
|topic| applicationProperty |string|    Media Services アカウントの Azure Resource Manager パス。|
|subject|   applicationProperty |string|    イベントを出力するエンティティへのサブパス。|
|eventTime| applicationProperty|    string| イベントが生成された時刻。|
|eventType| applicationProperty |string|    イベントの種類の識別子 (下記参照)。|
|body|body  |object|    特定のイベント データ。|
|dataVersion    |applicationProperty|   string  |{Major}.{Minor}|

### <a name="properties"></a>Properties

#### <a name="message-id"></a>message-id

イベントのグローバル一意識別子 (GUID)

#### <a name="topic"></a>topic

グラフに関連付けられている Azure Media Service アカウントを表します。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

イベントを出力しているエンティティ:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

subject プロパティを使用すると、汎用イベントをそれを生成しているモジュールにマップできます。 たとえば、RTSP のユーザー名またはパスワードが無効な場合は、生成されたイベントは `/graphInstances/myGraph/sources/myRtspSource` ノードで `Microsoft.Media.Graph.Diagnostics.ProtocolError` になります。

#### <a name="event-types"></a>イベントの種類

イベントの種類は、次のスキーマに従って名前空間に割り当てられます。

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>イベント クラス

|Class Name (クラス名)|説明|
|---|---|
|Analytics  |コンテンツ分析の一環として生成されるイベント。|
|診断    |問題とパフォーマンスの診断を支援するイベント。|
|運用時    |リソース操作の一環として生成されるイベント。|

イベントの種類は、各イベント クラスに固有です。

例 :

* Microsoft.Media.Graph.Analytics.Inference
* Microsoft.Media.Graph.Diagnostics.AuthorizationError
* Microsoft.Media.Graph.Operational.GraphInstanceStarted

### <a name="event-time"></a>イベント時間

イベント時間は ISO8601 文字列で記述され、イベントが発生した時刻です。

## <a name="logging"></a>ログ記録

他の IoT Edge モジュールと同様に、Edge デバイスで[コンテナーのログを調べる](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)こともできます。 ログに書き込まれる情報は、[次のモジュール ツイン](module-twin-configuration-schema.md) プロパティによって制御できます。

* logLevel

   * 使用できる値は Verbose、Information、Warning、Error、None です。
   * 既定値は Information です。ログには、エラー、警告、および情報の メッセージが含まれます。
   * 値を Warning に設定すると、ログにはエラー メッセージと警告メッセージが記録されます。
   * 値を Error に設定すると、ログにはエラー メッセージだけが記録されます。
   * 値を None に設定すると、ログは生成されません (これは推奨されません)。
   * Verbose は、問題を診断するために Azure サポートとログを共有する必要がある場合にのみ使用してください。
* logCategories

   * 次のいずれかまたは複数のコンマ区切りのリスト。Application、Events、MediaPipeline。
   * 既定値はApplication、Events です。
   * Application – これは、モジュールのスタートアップ メッセージ、環境エラー、ダイレクト メソッド呼び出しなど、モジュールからの概要情報です。
   * Events – これらは、この記事で既に説明したすべてのイベントです。
   * MediaPipeline –これらは、RTSP 対応カメラとの接続を確立するのが難しいなどの問題をトラブルシューティングする際に、分析情報を提供できる詳細なログです。
   
### <a name="generating-debug-logs"></a>デバッグ ログの生成

場合によっては、Azure サポートの問題解決を支援するため、上記のログより詳細なログを生成することが必要になる場合があります。 これを実現するには、次の 2 つの手順を実行します。

まず、createOptions を使用して、[モジュール ストレージをデバイス ストレージにリンク](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage)します。 クイックスタートから[配置マニフェスト テンプレート](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json)を確認すると、次のように表示されます。

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

これにより、Edge モジュールが、(デバイス) ストレージ パス "/var/local/mediaservices/" にログを書き込むことができます。 次の必要なプロパティをモジュールに追加するとします。

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

すると、モジュールによってデバッグ ログがバイナリ形式で (デバイス) ストレージ パス /var/local/mediaservices/debuglogs/ に書き込まれます。このパスは、Azure サポートと共有できます。

## <a name="faq"></a>よく寄せられる質問

[FAQ](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>次のステップ

[継続的なビデオ記録](continuous-video-recording-tutorial.md)
