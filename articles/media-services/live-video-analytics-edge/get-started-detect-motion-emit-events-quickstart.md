---
title: Live Video Analytics on IoT Edge の概要 - Azure
description: このクイックスタートでは、Live Video Analytics on IoT Edge の使用を開始する方法について説明します。 ライブ ビデオ ストリーム内のモーションを検出する方法について説明します。
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 16c3c849e7d936c6e94539176d8f171f52bd15de
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067696"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>クイック スタート:はじめに - Live Video Analytics on IoT Edge

このクイックスタートでは、Live Video Analytics on IoT Edge の基本的な操作手順について説明します。 IoT Edge デバイスとして Azure VM を使用します。 また、シミュレートされたライブ ビデオ ストリームも使用します。 

セットアップ手順の完了後、メディア グラフを通じて、シミュレートされたライブ ビデオ ストリームを実行できます。そのストリーム内のあらゆるモーションがメディア グラフによって検出、レポートされます。 次の図は、そのメディア グラフをグラフィカルに表しています。

![モーション検出に基づく Live Video Analytics](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol (AMQP) が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。

> [!TIP]
> Azure IoT Tools 拡張機能のインストール中に Docker のインストールを求められる場合があります。 このプロンプトは無視してかまいません。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

このチュートリアルでは、次の Azure リソースが必要です。

* IoT Hub
* ストレージ アカウント
* Azure Media Services アカウント
* Azure 内の Linux VM ([IoT Edge ランタイム](../../iot-edge/how-to-install-iot-edge-linux.md)がインストール済み)

このクイックスタートでは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)を使用して、ご利用の Azure サブスクリプションに必要なリソースをデプロイすることをお勧めします。 これを行うには、次のステップに従います。

1. [Azure Cloud Shell](https://shell.azure.com) に移動します。
1. Cloud Shell の初回使用時には、ストレージ アカウントと Microsoft Azure Files 共有を作成するためのサブスクリプションの選択を求められます。 **[ストレージの作成]** を選択して、Cloud Shell のセッション情報用のストレージ アカウントを作成します。 このストレージ アカウントは、Azure Media Services アカウントで使用するためにスクリプトによって作成されるアカウントとは別のものです。
1. Cloud Shell ウィンドウの左側にあるドロップダウン メニューから **[Bash]** をご利用の環境として選択します。

    ![環境セレクター](./media/quickstarts/env-selector.png)

1. 次のコマンドを実行します。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
スクリプトが正常に終了すれば、必要なすべてのリソースがご利用のサブスクリプションに表示されます。 スクリプトの出力では、リソースの表に IoT ハブ名が一覧表示されます。 リソースの種類 `Microsoft.Devices/IotHubs` を探し、名前を書き留めます。 この名前は次の手順で必要になります。 

このスクリプトにより、いくつかの構成ファイルが *~/clouddrive/lva-sample/* ディレクトリに生成されます。 これらのファイルは、後ほど、このクイックスタートで必要になります。

## <a name="deploy-modules-on-your-edge-device"></a>エッジ デバイスにモジュールをデプロイする

Cloud Shell から次のコマンドを実行します。

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

このコマンドは、次のモジュールをエッジ デバイス (今回のケースでは Linux VM) にデプロイします。

* Live Video Analytics on IoT Edge (モジュール名 `lvaEdge`)
* リアルタイム ストリーミング プロトコル (RTSP) シミュレーター (モジュール名 `rtspsim`)

RTSP シミュレーター モジュールは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)の実行時にエッジ デバイスにコピーされたビデオ ファイルを使用してライブ ビデオ ストリームをシミュレートします。 

これでモジュールはデプロイされましたが、メディア グラフはアクティブになっていません。

## <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT Tools 拡張機能を構成する

Azure IoT Tools 拡張機能を使用して IoT ハブに接続するには、次の手順に従います。

1. Visual Studio Code で、 **[表示]**  >  **[エクスプローラー]** を選択します。 または、Ctrl + Shift + E キーを押します。
1. **[エクスプローラー]** タブの左下隅で、 **[Azure IoT Hub]** を選択します。
1. **[その他のオプション]** アイコンを選択して、コンテキスト メニューを表示します。 次に、 **[Set IoT Hub Connection String]\(IoT Hub 接続文字列を設定する\)** を選択します。
1. 入力ボックスが表示されたら、IoT Hub 接続文字列を入力します。 Cloud Shell では、接続文字列を *~/clouddrive/lva-sample/appsettings.json* から取得できます。

接続に成功した場合、エッジ デバイスの一覧が表示されます。 少なくとも 1 つのデバイス (名前は **lva-sample-device**) が表示されます。 これでコンテキスト メニューから IoT Edge デバイスを管理し、Azure IoT Hub を操作できるようになりました。 エッジ デバイスにデプロイされたモジュールを表示するには、**lva-sample-device** の下の **[モジュール]** ノードを展開します。

![lva-sample-device ノード](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-method-calls"></a>ダイレクト メソッドの呼び出しを使用する

ライブ ビデオ ストリームの分析は、モジュールを使用し、ダイレクト メソッドを呼び出すことによって行うことができます。 詳細については、[Live Video Analytics on IoT Edge のダイレクト メソッド](direct-methods.md)に関する記事を参照してください。 

### <a name="invoke-graphtopologylist"></a>GraphTopologyList を呼び出す

モジュール内のすべての[グラフ トポロジ](media-graph-concept.md#media-graph-topologies-and-instances)を列挙するには、次のようにします。

1. Visual Studio Code で、**lvaEdge** モジュールを右クリックし、 **[Invoke Module Direct Method]\(モジュールのダイレクト メソッドを呼び出す\)** を選択します。
1. 表示されたボックスに、「*GraphTopologyList*」と入力します。
1. 次の JSON ペイロードをコピーして、ボックスに貼り付けます。 次に、Enter キーを押します。

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    数秒以内に、 **[出力]** ウィンドウに次の応答が表示されます。

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    グラフ トポロジが作成されていないため、この応答は予期されたものです。
    

### <a name="invoke-graphtopologyset"></a>GraphTopologySet を呼び出す

`GraphTopologyList` を呼び出す手順を使用することで、`GraphTopologySet` を呼び出して、[グラフ トポロジ](media-graph-concept.md#media-graph-topologies-and-instances)を設定できます。 ペイロードとして次の JSON を使用します。

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

この JSON ペイロードにより、3 つのパラメーターを定義するグラフ トポロジが作成されます。 これらのパラメーターのうち 2 つには既定値があります。 このトポロジには、ソース (RTSP ソース) ノード、プロセッサ (モーション検出プロセッサ) ノード、シンク (IoT Hub シンク) ノードがそれぞれ 1 つずつ存在します。

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

返される状態は 201 です。 この状態は、新しいトポロジが作成されたことを示しています。 

次の手順を試してみましょう。

1. `GraphTopologySet` を再度呼び出します。 返された状態コードは 200 です。 このコードは、既存のトポロジが正常に更新されたこと示しています。
1. `GraphTopologySet` を再度呼び出しますが、説明文字列を変更します。 返された状態コードは 200 であり、説明は新しい値に更新されています。
1. 前のセクションで説明したように `GraphTopologyList` を呼び出します。 返されたペイロードに `MotionDetection` トポロジが表示されるようになりました。

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet を呼び出す

次のペイロードを使用して `GraphTopologyGet` を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

応答ペイロードで、次の詳細に注目します。

* 状態コードは 200 であり、成功を示しています。
* ペイロードには、`created` タイム スタンプと `lastModified` タイム スタンプが含まれています。

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet を呼び出す

前出のグラフ トポロジを参照するグラフ インスタンスを作成します。 グラフ インスタンスを使用すると、同じグラフ トポロジを使用して複数のカメラからのライブ ビデオ ストリームを分析できます。 詳細については、「[メディア グラフのトポロジとインスタンス](media-graph-concept.md#media-graph-topologies-and-instances)」を参照してください。

次のペイロードを使用して、ダイレクト メソッド `GraphInstanceSet` を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

このペイロードについて次の点に注目します。

* インスタンスを作成する必要のあるトポロジ名 (`MotionDetection`) が指定されています。
* `rtspUrl` のパラメーターの値が含まれています。これの既定値は、グラフ トポロジのペイロードに設定されていませんでした。

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

応答ペイロードで、次の点に注目します。

* 状態コードは 201 であり、新しいインスタンスが作成されたことを示しています。
* 状態は `Inactive` であり、グラフ インスタンスは作成されたものの、アクティブにされていないことを示しています。 詳細については、「[メディア グラフの状態](media-graph-concept.md)」を参照してください。

次の手順を試してみましょう。

1. 同じペイロードを使用して `GraphInstanceSet` を再度呼び出します。 返された状態コードは 200 であることに注目してください。
1. `GraphInstanceSet` を再度呼び出しますが、別の説明を使用します。 応答ペイロードの更新された説明に注目してください。これはグラフ インスタンスが正常に更新されたことを示しています。
1. `GraphInstanceSet` を呼び出しますが、名前を `Sample-Graph-2` に変更します。 応答ペイロードで、新しく作成されたグラフ インスタンス (つまり、状態コード 201) に注目してください。

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate を呼び出す

今度は、グラフ インスタンスをアクティブにし、モジュールを使用してライブ ビデオのフローを開始します。 次のペイロードを使用して、ダイレクト メソッド `GraphInstanceActivate` を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、グラフ インスタンスが正常にアクティブにされたことを示しています。

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet を呼び出す

今度は、次のペイロードを使用して、ダイレクト メソッド `GraphInstanceGet` を呼び出します。

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

応答ペイロードで、次の詳細に注目します。

* 状態コードは 200 であり、成功を示しています。
* 状態は `Active` であり、グラフ インスタンスがアクティブになったこと示しています。

## <a name="observe-results"></a>結果を観察する

先ほど作成してアクティブにしたグラフ インスタンスは、モーション検出プロセッサ ノードを使用して、受信ライブ ビデオ ストリーム内のモーションを検出します。 これは、IoT Hub シンク ノードにイベントを送信します。 これらのイベントは IoT Edge Hub にリレーされます。 

結果を確認するには、次の手順に従います。

1. Visual Studio Code で **[エクスプローラー]** ペインを開きます。 左下隅で **[Azure IoT Hub]** を探します。
2. **[デバイス]** ノードを展開します。
3. **lva-sample-device** を右クリックし、 **[組み込みイベント エンドポイントの監視を開始します]** を選択します。

    ![IoT Hub イベントの監視を開始する](./media/quickstarts/start-monitoring-iothub-events.png)
    
**[出力]** ウィンドウに次のメッセージが表示されます。

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

次の詳細に注目します。

* メッセージには、`body` セクションと `applicationProperties` セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。
* `applicationProperties` 内の `subject` は、メッセージの生成元の `MediaGraph` 内のノードを参照しています。 今回のケースでは、メッセージはモーション検出プロセッサから生成されています。
* `applicationProperties` 内の `eventType` は、このイベントが分析イベントであることを示しています。
* `eventTime` 値は、イベントが発生した時刻です。
* `body` セクションには、分析イベントに関するデータが含まれています。 今回のケースでは、イベントは推論イベントであるため、本文には `timestamp` および `inferences` のデータが含まれています。
* `inferences` セクションは、`type` が `motion` であることを示しています。 `motion` イベントに関する追加データが提供されています。

メディア グラフをしばらく実行していると、 **[出力]** ウィンドウに次のメッセージが表示されます。

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

このメッセージでは、次の詳細に注目します。

* `applicationProperties` 内の `subject` は、メッセージがメディア グラフの RTSP ソース ノードから生成されたことを示しています。
* `applicationProperties` 内の `eventType` は、このイベントが診断であることを示しています。
* `body` には、診断イベントに関するデータが含まれています。 今回のケースでは、イベントが `MediaSessionEstablished` であるため、メッセージに本文が含まれています。

## <a name="invoke-additional-direct-methods-to-clean-up"></a>その他のダイレクト メソッドを呼び出してクリーンアップする

ダイレクト メソッドを呼び出して、まずグラフ インスタンスを非アクティブ化し、その後削除します。

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate を呼び出す

次のペイロードを使用して、ダイレクト メソッド `GraphInstanceDeactivate` を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、グラフ インスタンスが正常に非アクティブ化されたことを示しています。

次に、この記事で前述したように `GraphInstanceGet` の呼び出しを試してみます。 `state` 値を確認します。

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete を呼び出す

次のペイロードを使用して、ダイレクト メソッド `GraphInstanceDelete` を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、グラフ インスタンスが正常に削除されたことを示しています。

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete を呼び出す

次のペイロードを使用して、ダイレクト メソッド `GraphTopologyDelete` を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、グラフ トポロジが正常に削除されたことを示しています。

次の手順を試してみましょう。

1. `GraphTopologyList` を呼び出し、モジュールにグラフ トポロジが含まれていないことを確認します。
1. `GraphTopologyList` と同じペイロードを使用して `GraphInstanceList` を呼び出します。 グラフ インスタンスが列挙されないことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用する予定がない場合は、このクイックスタートで作成したリソースを削除します。

## <a name="next-steps"></a>次のステップ

* [Live Video Analytics on IoT Edge を使用してビデオを記録する](continuous-video-recording-tutorial.md)方法を学習します。
* [診断メッセージ](monitoring-logging.md)について詳しく学習します。
