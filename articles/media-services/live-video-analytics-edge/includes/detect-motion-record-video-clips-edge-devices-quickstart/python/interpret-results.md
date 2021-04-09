---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88682337"
---
メディア グラフを実行すると、モーション検出プロセッサ ノードの結果が IoT Hub シンク ノードを介して IoT バブに渡されます。 Visual Studio Code の **[出力]** ウィンドウに表示されるメッセージには、`body` セクションと `applicationProperties` セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージ内のアプリケーションのプロパティと body の内容は、Live Video Analytics モジュールによって定義されています。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

メディア グラフがインスタンス化されると、RTSP ソース ノードは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続を試みます。 接続に成功すると、次のイベントが出力されます。

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

前の出力は、次のような内容を表します。 

* このメッセージは、診断イベント `MediaSessionEstablished` です。 これは、RTSP ソース ノード (subject) が RTSP シミュレーターとの接続を確立し、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* `applicationProperties` 内の `subject` は、メッセージの生成元となった、グラフ トポロジ内のノードを参照しています。 このケースでは、RTSP ソース ノードからメッセージが生成されます。
* `applicationProperties` 内の `eventType` は、このイベントが診断イベントであることを示しています。
* `eventTime` 値は、イベントが発生した時刻です。
* `body` セクションには、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

### <a name="recordingstarted-event"></a>RecordingStarted イベント

モーションが検出されると、シグナル ゲート プロセッサ ノードがアクティブになり、メディア グラフのファイル シンク ノードが MP4 ファイルの書き込みを開始します。 ファイル シンク ノードは、操作イベントを送信します。 `type` は、これがモーション検出プロセッサからの結果であることを示す `motion` に設定されています。 `eventTime` 値は、モーションが発生した UTC 時刻です。 このプロセスの詳細については、このクイックスタートの「[概要](#overview)」セクションを参照してください。

このメッセージの例を次に示します。

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

前のメッセージは、次のような内容を表します。 

* `applicationProperties` 内の `subject` は、メッセージの生成元となった、メディア グラフ内のノードを参照しています。 このケースでは、メッセージはファイル シンク ノードから生成されています。
* `applicationProperties` 内の `eventType` は、このイベントが操作であることを示しています。
* `eventTime` 値は、イベントが発生した時刻です。 この時刻は、`MediaSessionEstablished` の後、ビデオのフローが開始されて 5 から 6 秒後です。 この時刻は、駐車場へと[車が移動を開始した](#review-the-sample-video)時点 (5 から 6 秒あたりのマーク) に対応しています。
* `body` セクションには、操作イベントに関するデータが含まれています。 このケースでは、データは `outputType` および `outputLocation` で構成されます。
* `outputType` 変数は、この情報がファイル パスに関するものであることを示しています。
* `outputLocation` 値は、エッジ モジュール内の MP4 ファイルの場所です。

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped イベントと RecordingAvailable イベント

[グラフ トポロジ](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)でシグナル ゲート プロセッサ ノードのプロパティを確認すると、アクティブ化の時間が 5 秒に設定されていることがわかります。 `RecordingStarted` イベントを受信してから約 5 秒後に、次のイベントを取得します。

* `RecordingStopped` イベント。記録が停止したことを示します。
* `RecordingAvailable` イベント。MP4 ファイルを表示に使用できるようになったことを示します。

この 2 つのイベントは、通常、数秒以上間隔を開けずに出力されます。
