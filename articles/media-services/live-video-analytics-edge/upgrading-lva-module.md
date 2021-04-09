---
title: Live Video Analytics on IoT Edge 1.0 から 2.0 へのアップグレード
description: この記事では、Azure IoT Edge モジュールで Live Video Analytics (LVA) をアップグレードする際の相違点およびさまざまな考慮事項について説明します。
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955651"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Live Video Analytics on IoT Edge 1.0 から 2.0 へのアップグレード

この記事では、Azure IoT Edge モジュールで Live Video Analytics (LVA) をアップグレードする際の相違点およびさまざまな考慮事項について説明します。

## <a name="change-list"></a>変更一覧

> [!div class="mx-tdCol4BreakAll"]
> |タイトル|Live Video Analytics 1.0|Live Video Analytics 2.0|説明|
> |-------------|----------|---------|---------|
> |コンテナー イメージ|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Azure IoT Edge の Live Video Analytics 用に Microsoft が公開した Docker イメージ|
> |**MediaGraph ノード** |    |   |   |
> |変換元|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP ソース </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub メッセージのソース |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP ソース </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub メッセージのソース | メディア インジェストとメッセージのソースとして機能する MediaGraph ノード。|
> |[プロセッサ]|:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーション検出プロセッサ </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: フレーム レート フィルター プロセッサ </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: HTTP 拡張プロセッサ </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: gRPC 拡張プロセッサ </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::シグナル ゲート プロセッサ |:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーション検出プロセッサ </br>:::image type="icon" source="./././media/upgrading-lva/remove.png"::: **フレーム レート フィルター プロセッサ**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: HTTP 拡張プロセッサ </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: gRPC 拡張プロセッサ </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: シグナル ゲート プロセッサ | AI 推論サーバーに送信する前に、メディアのフォーマットを有効にする MediaGraph ノード。|
> |シンク|:::image type="icon" source="./././media/upgrading-lva/check.png"::: アセット シンク </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: ファイル シンク </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub メッセージ シンク|:::image type="icon" source="./././media/upgrading-lva/check.png"::: アセット シンク </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: ファイル シンク </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub メッセージ シンク| 処理されたメディアを格納できるようにする MediaGraph ノード。|
> |**サポートされている MediaGraph** |    |   |   |
> |トポロジ|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 継続的なビデオ記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーション分析と継続的なビデオ記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析と継続的なビデオ記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーションに関するイベントベースの記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI に関するイベントベースのビデオ記録</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部イベントに関するイベントベースの記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーション分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI 推論が続くモーション分析 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 継続的なビデオ記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーション分析と継続的なビデオ記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析と継続的なビデオ記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーションに関するイベントベースの記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI に関するイベントベースのビデオ記録</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部イベントに関するイベントベースの記録 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: モーション分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI 推論が続くモーション分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **AI 構成** </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **オーディオ記録とビデオ記録** </br>  | 値のプレースホルダーとしてパラメーターを使用して、グラフのブループリントを定義できるようにする MediaGraph トポロジ。|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>1\.0 から 2.0 への Live Video Analytics on IoT Edge モジュールのアップグレード
IoT Edge モジュールで Live Video Analytics をアップグレードするときは、次の情報を必ず更新してください。
### <a name="container-image"></a>コンテナー イメージ
デプロイ テンプレートで、`modules` ノードの IoT Edge モジュールで Live Video Analytics を探し、`image` フィールドを次のように更新します。
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> IoT Edge モジュールで Live Video Analytics の名前を変更していない場合は、モジュール ノードの下で `lvaEdge` を探します。

### <a name="topology-file-changes"></a>トポロジ ファイルの変更
トポロジ ファイルで **`apiVersion`** が 2.0 に設定されていることを確認します

### <a name="mediagraph-node-changes"></a>MediaGraph ノードの変更


* カメラ ソースからのオーディオを、ビデオと共に下流に渡すことができるようになりました。 **`outputSelectors`** を使用して任意のグラフ ノードに **オーディオのみ** を渡すことも **ビデオのみ** を渡すことも **オーディオとビデオの両方を** 渡すこともできます。 たとえば、RTSP ソースのビデオのみを選択して下流に渡す場合は、次のコードを RTSP ソースノードに追加します。
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** は省略可能なプロパティです。 これが使用されていない場合、メディア グラフはオーディオ (有効な場合) とビデオを RTSP カメラから下流に渡します。 

* `MediaGraphHttpExtension` と `MediaGraphGrpcExtension` のプロセッサでは、次の変更点に注意してください。  
    #### <a name="image-properties"></a>イメージのプロパティ
    * `MediaGraphImageFormatEncoded` はサポート対象から除外されました。 
      * 代わりに、 **`MediaGraphImageFormatBmp`** 、 **`MediaGraphImageFormatJpeg`** 、または **`MediaGraphImageFormatPng`** を使用してください。 たとえば、次のように入力します。
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * 未加工のイメージを使用する場合は、 **`MediaGraphImageFormatRaw`** を使用します。 これを使用するには、イメージ ノードを次のように更新します。
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > pixelFormat の使用可能な値には、`yuv420p`、`rgb565be`、`rgb565le`、`rgb555be`、`rgb555le`、`rgb24`、`bgr24`、`argb`、`rgba`、`abgr`、`bgra` があります  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>gRPC 拡張プロセッサの extensionConfiguration  
    * `MediaGraphGrpcExtension` プロセッサでは、 **`extensionConfiguration`** と呼ばれる新しいプロパティが使用でき、これは gRPC コントラクトの一部として使用できる省略可能な文字列です。 推論サーバーにデータを渡すためにこのフィールドを使用でき、推論サーバーでそのデータをどのように使用するかを定義できます。  
    このプロパティのユース ケースの 1 つは、単一の推論サーバーにパッケージ化された複数の AI モデルがある場合です。 このプロパティを使用すると、AI モデルごとにノードを公開する必要はありません。 代わりに、グラフ インスタンスでは、拡張プロバイダーは、 **`extensionConfiguration`** プロパティを使用して、異なる AI モデルを選択する方法を定義でき、実行中に LVA は、推論サーバーにこの文字列を渡し、推論サーバーはこれを使用して目的の AI モデルを呼び出します。  

    #### <a name="ai-composition"></a>AI 構成
    * Live Video Analytics 2.0 では、トポロジ内で複数のメディア グラフ拡張プロセッサの使用がサポートされるようになりました。 RTSP カメラのメディア フレームを別の AI モデルに順番にまたは同時にあるいは両方を組み合わせて渡すことができます。 2 つの AI モデルが順番に使用される場合を示すサンプル トポロジをご覧ください。

### <a name="disk-space-management-with-sink-nodes"></a>シンク ノードを使用したディスク領域の管理
* **ファイル シンク** ノードで、IoT Edge モジュールの Live Video Analytics が、処理されたイメージの格納に使用できるディスク領域の大きさを指定できるようになりました。 これを行うには、 **`maximumSizeMiB`** フィールドを FileSink ノードに追加します。 サンプルのファイル シンク ノードは次のとおりです。
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* **アセット シンク** ノードでは、IoT Edge モジュールの Live Video Analytics が、処理されたイメージの格納に使用できるディスク領域の大きさを指定できます。 これを行うには、 **`localMediaCacheMaximumSizeMiB`** フィールドをアセット シンク ノードに追加します。 サンプルのアセット シンク ノードは次のとおりです。
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  **ファイル シンク** パスは、ベース ディレクトリのパスとファイル名のパターンに分割されますが、**アセット シンク** パスには基本ディレクトリ パスが含まれます。  

### <a name="frame-rate-management"></a>フレーム レートの管理
* **`MediaGraphFrameRateFilterProcessor`** は **Live Video Analytics on IoT Edge 2.0** モジュールで非推奨になっています。
    * 処理のために着信ビデオをサンプリングするには、 **`samplingOptions`** プロパティを MediaGraph 拡張プロセッサ (`MediaGraphHttpExtension` または `MediaGraphGrpcExtension`) に追加します  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Telegraf を使用した Prometheus 形式のモジュール メトリック
このリリースでは、Telegraf を使用して、Azure Monitor にメトリックを送信できます。 そこから、メトリックは Log Analytics またはイベント ハブに転送される場合があります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="イベントの分類":::

Docker を使用して簡単にカスタム構成で Telegraf イメージを作成できます。 詳細については、「[監視とログ記録](monitoring-logging.md#azure-monitor-collection-via-telegraf)」のページを参照してください。

## <a name="next-steps"></a>次のステップ

[Live Video Analytics on IoT Edge の概要](get-started-detect-motion-emit-events-quickstart.md)