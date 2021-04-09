---
title: 空間分析操作
titleSuffix: Azure Cognitive Services
description: 空間分析操作。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: af220106c415165a0dbe7cda64a31a6068f53164
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981784"
---
# <a name="spatial-analysis-operations"></a>空間分析操作

空間分析により、カメラ デバイスからのリアルタイム ストリーミング ビデオの分析が可能になります。 構成したカメラ デバイスごとに、空間分析の操作によって、Azure IoT Hub のインスタンスに送信される JSON メッセージの出力ストリームが生成されます。 

空間分析コンテナーは、次の操作を実装します。

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | カメラの視野内の指定されたゾーン内の人数をカウントします。 PersonCount によって正確な合計を記録するには、ゾーンを 1 台のカメラで完全にカバーする必要があります。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | 人がゾーンに出入りするときに _personZoneEnterExitEvent_ イベントを発行し、交差したゾーンの番号付き側に方向情報を提供します。 人がゾーンを出るときに _personZoneDwellTimeEvent_ を発行し、方向情報と人がゾーン内で過ごしたミリ秒数を提供します。 |
| cognitiveservices.vision.spatialanalysis-persondistance | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |

上記のすべての操作は、処理中のビデオ フレームを視覚化する機能を備えた `.debug` バージョンでも使用できます。 ビデオ フレームとイベントの視覚化を有効にするには、ホスト コンピューターで `xhost +` を実行する必要があります。

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | カメラの視野内の指定されたゾーン内の人数をカウントします。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | 人がゾーンに出入りするときに _personZoneEnterExitEvent_ イベントを発行し、交差したゾーンの番号付き側に方向情報を提供します。 人がゾーンを出るときに _personZoneDwellTimeEvent_ を発行し、方向情報と人がゾーン内で過ごしたミリ秒数を提供します。 |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |

空間分析は、[Live Video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) でビデオ AI モジュールとして実行することもできます。 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | カメラの視野内の指定されたゾーン内の人数をカウントします。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | 人がゾーンに出入りするときに _personZoneEnterExitEvent_ イベントを発行し、交差したゾーンの番号付き側に方向情報を提供します。 人がゾーンを出るときに _personZoneDwellTimeEvent_ を発行し、方向情報と人がゾーン内で過ごしたミリ秒数を提供します。  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |

Live Video Analytics の操作は、処理中のビデオ フレームを視覚化する機能を備えた `.debug` バージョン (例: cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug) でも使用できます。 ビデオ フレームとイベントの視覚化を有効にするには、ホスト コンピューターで `xhost +` を実行する必要があります。

> [!IMPORTANT]
> コンピューター ビジョン AI モデルは、ビデオ映像で人の存在を検出してその位置を特定し、人体を囲む境界ボックスを使用して出力します。 これらの AI モデルは、個人の ID や人口統計情報の検出を試みることはありません。

これらの各空間分析操作に必要なパラメーターを次に示します。

| 操作のパラメーター| 説明|
|---------|---------|
| 操作 ID | 上記の表の操作識別子。|
| enabled | ブール値: true または false。|
| VIDEO_URL| カメラ デバイスの RTSP URL (例: `rtsp://username:password@url`)。 空間分析では、RTSP、http、または mp4 を使用して H.264 でエンコードされたストリームをサポートしています。 Video_URL は、AES 暗号化を使用して難読化された base64 文字列値として指定できます。ビデオの URL が難読化されている場合は、環境変数として `KEY_ENV` と `IV_ENV` を指定する必要があります。 キーと暗号化を生成するためのサンプル ユーティリティについては、[こちら](/dotnet/api/system.security.cryptography.aesmanaged)を参照してください。 |
| VIDEO_SOURCE_ID | カメラ デバイスまたはビデオ ストリームのフレンドリ名。 これは、イベントの JSON 出力と共に返されます。|
| VIDEO_IS_LIVE| カメラ デバイスの場合は true、録画されたビデオの場合は false。|
| VIDEO_DECODE_GPU_INDEX| ビデオ フレームをデコードする GPU。 既定では 0 です。 `VICA_NODE_CONFIG`、`DETECTOR_NODE_CONFIG` などの他のノード構成の `gpu_index` と同じである必要があります。|
| INPUT_VIDEO_WIDTH | ビデオまたはストリームのフレーム幅を入力します (例: 1920)。 これは省略可能なフィールドであり、指定された場合、フレームはこの寸法に合わせてスケーリングされますが、縦横比は保持されます。|
| DETECTOR_NODE_CONFIG | 検出ノードを実行する GPU を示す JSON。 `"{ \"gpu_index\": 0 }",` という形式にする必要があります。|
| SPACEANALYTICS_CONFIG | 後述するゾーンとラインの JSON 構成。|
| ENABLE_FACE_MASK_CLASSIFIER | `True` を使用すると、ビデオ ストリームでフェイス マスクを着用している人を検出できるようになり、`False` を使用すると無効になります。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 `"INPUT_VIDEO_WIDTH": 1920` にする必要があります。 検出された人物がカメラに向いていないか、カメラから離れすぎている場合、フェイス マスク属性は返されません。 詳細については、[カメラの配置](spatial-analysis-camera-placement.md)ガイドを参照してください。 |

すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーターの例を次に示します。

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sampling_num": 80,
"calibration_quality_check_sampling_times": 5,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000,
"recalibration_score": 75
}
```

| Name | Type| 説明|
|---------|---------|---------|
| `gpu_index` | string| この操作が実行される GPU インデックス。|
| `do_calibration` | string | 調整がオンになっていることを示します。 **cognitiveservices.vision.spatialanalysis-persondistance** が正しく機能するには、`do_calibration` が true である必要があります。 do_calibration は、既定では True に設定されています。 |
| `enable_recalibration` | [bool] | 自動再調整がオンになっているかどうかを示します。 既定値は `true` です。|
| `calibration_quality_check_frequency_seconds` | INT | 再調整が必要かどうかを判断するための、各品質チェック間の最小秒数。 既定値は `86400` (24 時間) です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_sampling_num` | INT | 品質チェック エラーの測定ごとに使用する、ランダムに選択された格納データ サンプルの数。 既定値は `80` です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_sampling_times` | INT | 品質チェックごとにランダムに選択されたデータ サンプルのさまざまなセットに対してエラー測定が実行される回数。 既定値は `5` です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_sample_collect_frequency_seconds` | INT | 再調整と品質チェックのために新しいデータ サンプルを収集する間の最小秒数。 既定値は `300` (5 分間) です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_one_round_sample_collect_num` | INT | サンプル コレクションのラウンドごとに収集する新しいデータ サンプルの最小数。 既定値は `10` です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_queue_max_size` | INT | カメラ モデルの調整時に格納するデータ サンプルの最大数。 既定値は `1000` です。 `enable_recalibration=True` の場合のみ使用されます。|
| `recalibration_score` | INT | 再調整を開始する最大品質しきい値。 既定値は `75` です。 `enable_recalibration=True` の場合のみ使用されます。 調整品質は、画像ターゲットの再投影エラーとの逆の関係に基づいて計算されます。 2D 画像フレームで検出されたターゲットがある場合、ターゲットは 3D 空間に投影され、既存のカメラ調整パラメーターを使用して 2D 画像フレームに再投影されます。 再投影エラーは、検出されたターゲットと再投影されたターゲットの間の平均距離によって測定されます。|
| `enable_breakpad`| [bool] | デバッグ用のクラッシュ ダンプを生成するために使用される breakpad を有効にするかどうかを示します。 既定値は `false` です。 `true` に設定した場合は、コンテナー `createOptions` の `HostConfig` 部分に `"CapAdd": ["SYS_PTRACE"]` も追加する必要があります。 既定では、クラッシュ ダンプは [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter アプリにアップロードされます。クラッシュ ダンプを独自の AppCenter アプリにアップロードする場合は、環境変数 `RTPT_APPCENTER_APP_SECRET` をアプリのアプリ シークレットでオーバーライドできます。


### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>cognitiveservices.vision.spatialanalysis-personcount のゾーン構成

 これは、ゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
    }]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。多角形ポイントは、正規化された座標 (0-1) に基づいています。左上隅が (0.0, 0.0)、右下隅が (1.0, 1.0) になります。   
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcount** の場合、これは `count` である必要があります。|
| `trigger` | string| イベントを送信するためのトリガーの種類。 サポートされている値は、`event` (カウントが変わったときにイベントを送信する場合)、または `interval` (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。
| `output_frequency` | INT | イベントが送信される頻度。 `output_frequency` = X の場合、X おきにイベントが送信されます。たとえば、 `output_frequency` = 2 は、1 つおきにイベントが送信されることを意味します。 `output_frequency` は、`event` と `interval` の両方に適用されます。 |
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>cognitiveservices.vision.spatialanalysis-personcrossingline のライン構成

これは、ラインを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には、交差する複数のラインを構成できます。

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `lines` | list| ラインのリスト。|
| `name` | string| このラインのフレンドリ名。|
| `line` | list| ラインの定義。 これは、"入る" と "出る" を理解できるようにするための方向線です。|
| `start` | 値のペア| ラインの始点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 |
| `end` | 値のペア| ラインの終点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 |
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 既定値は 16 です。 これは、最大の精度を実現するために推奨される値です。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcrossingline** の場合、これは `linecrossing` である必要があります。|
|`trigger`|string|イベントを送信するためのトリガーの種類。<br>サポートされている値: "event": だれかがラインを越えたときに発生します。|
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。 既定値は footprint です。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon のゾーン構成

これは、ゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。  |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** の場合、これは `zonecrossing` または `zonedwelltime` である必要があります。|
| `trigger`|string|イベントを送信するためのトリガーの種類。<br>サポートされている値: "event": だれかがゾーンに入ったときまたはゾーンから出たときに発生します。|
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。 既定値は footprint です。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>cognitiveservices.vision.spatialanalysis-persondistance のゾーン構成

これは、**cognitiveservices.vision.spatialanalysis-persondistance** のゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
    "type": "persondistance",
    "config":{
        "trigger": "event",
        "output_frequency":1,
        "minimum_distance_threshold":6.0,
        "maximum_distance_threshold":35.0,
           "threshold": 16.00,
           "focus": "footprint"
            }
    }]
   }]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は人数をカウントする領域を表し、人と人との距離が測定されます。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-persondistance** の場合、これは `people_distance` である必要があります。|
| `trigger` | string| イベントを送信するためのトリガーの種類。 サポートされている値は、`event` (カウントが変わったときにイベントを送信する場合)、または `interval` (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。
| `output_frequency` | INT | イベントが送信される頻度。 `output_frequency` = X の場合、X おきにイベントが送信されます。たとえば、 `output_frequency` = 2 は、1 つおきにイベントが送信されることを意味します。 `output_frequency` は、`event` と `interval` の両方に適用されます。|
| `minimum_distance_threshold` | float| 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。|
| `maximum_distance_threshold` | float| 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。|
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。|

ゾーンとラインの構成については、[カメラの配置](spatial-analysis-camera-placement.md)のガイドラインを参照してください。

## <a name="spatial-analysis-operation-output"></a>空間分析操作の出力

各操作のイベントは、JSON 形式で Azure IoT Hub に送信されます。

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcount の AI 分析情報の JSON 形式

この操作によって出力されるイベントのサンプル JSON。

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_Mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
                "face_noMask": 0.99
            }
            }
    }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `zone` | string | 越えられたゾーンを表す多角形の "name" フィールド|
| `trigger` | string| SPACEANALYTICS_CONFIG の `trigger` の値に応じて、トリガーの種類は "event" または "interval" になります。|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|
| `face_Mask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用していることを示します |
| `face_noMask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用して **いない** ことを示します |

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|
| `cameraCallibrationInfo` | collection | 値のコレクション|
| `status` | string | `state[;progress description]` の形式での調整の状態。 状態は、`Calibrating`、`Recalibrating` (再調整が有効な場合)、または `Calibrated` になります。 進行状況の説明部分は、現在の調整プロセスの進行状況を示すために使用される `Calibrating` および `Recalibrating` 状態の場合にのみ有効です。|
| `cameraHeight` | float | 地面からのカメラの高さ (フィート単位)。 これは自動調整から推論されます。 |
| `focalLength` | float | カメラの焦点距離 (ピクセル単位)。 これは自動調整から推論されます。 |
| `tiltUpAngle` | float | 垂直線からのカメラの傾斜角度。 これは自動調整から推論されます。|

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingline の AI 分析情報の JSON 形式

この操作によって出力される検出のサンプル JSON。

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_Mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `status` | string| ラインを越える方向 ("CrossLeft" または "CrossRight")|
| `zone` | string | 越えられたラインの "name" フィールド|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|
| `face_Mask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用していることを示します |
| `face_noMask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用して **いない** ことを示します |

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|


> [!IMPORTANT]
> AI モデルは、人がカメラに顔を向けているか、カメラから顔をそらしているかに関係なく、その人を検出します。 AI モデルでは、顔認識は実行されず、生体認証情報も生成されません。 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon の AI 分析情報の JSON 形式

`zonecrossing` 型の SPACEANALYTICS_CONFIG を使用してこの操作によって出力された検出用のサンプル JSON。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_Mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

`zonedwelltime` 型の SPACEANALYTICS_CONFIG を使用してこの操作によって出力された検出用のサンプル JSON。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
        "durationMs": 7132.0
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類。 この値には、_personZoneDwellTimeEvent_ または _personZoneEnterExitEvent_ のいずれかを指定できます。|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `status` | string| 多角形を越える方向 ("Enter" または "Exit")|
| `side` | INT| 人が越えた多角形の辺の数。 各辺は、ゾーンを表す多角形の 2 つの頂点の間の番号付きのエッジです。 多角形の最初の 2 つの頂点間のエッジは、最初の辺を表します|
| `durationMs` | float | 人がゾーンで過ごした時間を表すミリ秒数。 このフィールドは、イベントの種類が _personZoneDwellTimeEvent_ の場合に指定されます。|
| `zone` | string | 越えられたゾーンを表す多角形の "name" フィールド|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|
| `face_Mask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用していることを示します |
| `face_noMask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用して **いない** ことを示します |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>cognitiveservices.vision.spatialanalysis-persondistance の AI 分析情報の JSON 形式

この操作によって出力される検出のサンプル JSON。

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `personCount` | INT| イベントの生成時に検出された人数|
| `averageDistance` | float| 検出されたすべての人の間の平均距離 (フィート単位)|
| `minimumDistanceThreshold` | float| 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。|
| `maximumDistanceThreshold` | float| 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。|
| `eventName` | string| イベント名は、`TooClose` (`minimumDistanceThreshold` に違反している場合)、`TooFar` (`maximumDistanceThreshold` に違反している場合)、または `unknown` (自動調整が完了していない場合) です。|
| `distanceViolationPersonCount` | INT| `minimumDistanceThreshold` または `maximumDistanceThreshold` の違反で検出された人数|
| `zone` | string | 人と人との間の距離について監視されていたゾーンを表す多角形の "name" フィールド|
| `trigger` | string| SPACEANALYTICS_CONFIG の `trigger` の値に応じて、トリガーの種類は "event" または "interval" になります。|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|
| `centerGroundPoint` | 2 つの浮動小数点値| 地面上での人の推論された位置の座標の `x` および `y` 値 (フィート単位)。 `x` と `y` は、フロアが階層であると仮定した場合の床面上の座標です。 カメラの位置は原点です。 |

`centerGroundPoint` を計算する場合、`x` は、カメラの画像平面に垂直になる線に沿った、カメラから人までの距離です。 `y` は、カメラの画像平面に平行な線に沿った、カメラから人までの距離です。 

![中心の地表点の例](./media/spatial-analysis/x-y-chart.png) 

この例では `centerGroundPoint` は `{x: 4, y: 5}` です。 これは、カメラから 4 フィート、右に 5 フィート離れた場所に、部屋を見下ろしている人がいることを意味します。


| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|
| `cameraCallibrationInfo` | collection | 値のコレクション|
| `status` | string | `state[;progress description]` の形式での調整の状態。 状態は、`Calibrating`、`Recalibrating` (再調整が有効な場合)、または `Calibrated` になります。 進行状況の説明部分は、現在の調整プロセスの進行状況を示すために使用される `Calibrating` および `Recalibrating` 状態の場合にのみ有効です。|
| `cameraHeight` | float | 地面からのカメラの高さ (フィート単位)。 これは自動調整から推論されます。 |
| `focalLength` | float | カメラの焦点距離 (ピクセル単位)。 これは自動調整から推論されます。 |
| `tiltUpAngle` | float | 垂直線からのカメラの傾斜角度。 これは自動調整から推論されます。|


## <a name="use-the-output-generated-by-the-container"></a>コンテナーによって生成された出力を使用する

空間分析の検出またはイベントをアプリケーションに統合できます。 検討すべきいくつかの方法を次に示します。 

* 選択したプログラミング言語の Azure Event Hub SDK を使用して、Azure IoT Hub エンドポイントに接続し、イベントを受信します。 詳細については、「[デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)」をご覧ください。 
* イベントを他のエンドポイントに送信したり、イベントをデータ ストレージに保存したりするには、Azure IoT Hub の **メッセージ ルーティング** を設定します。 詳細については、[IoT Hub メッセージ ルーティング](../../iot-hub/iot-hub-devguide-messages-d2c.md)に関する記事をご覧ください。 
* 到着したイベントをリアルタイムで処理し、視覚化を作成する Azure Stream Analytics ジョブを設定します。 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>空間分析操作を大規模に展開する (複数のカメラ)

GPU のパフォーマンスと使用率を最大限に引き出すには、グラフ インスタンスを使用して複数のカメラに空間分析操作を展開できます。 次に示すのは、15 台のカメラで `cognitiveservices.vision.spatialanalysis-personcrossingline` 操作を実行するためのサンプルです。

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| 名前 | Type| 説明|
|---------|---------|---------|
| `batch_size` | INT | すべてのカメラの解像度が同じ場合は、`batch_size` をその操作で使用されるカメラの数に設定します。それ以外の場合は、`batch_size` を 1 に設定するか、既定値 (1) のままにします。これは、バッチがサポートされないことを示します。 |

## <a name="next-steps"></a>次のステップ

* [人数カウント Web アプリをデプロイする](spatial-analysis-web-app.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)
