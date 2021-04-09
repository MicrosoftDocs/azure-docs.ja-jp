---
title: Intel の AI 拡張機能 OpenVINO™ モデル サーバーを使用してライブ ビデオを分析する
description: このチュートリアルでは、Intel が提供する AI モデル サーバーを使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析します。
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 68b5b7561cc31e156a745bcfb07e3203de10d425
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702217"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>チュートリアル:Intel の AI 拡張機能 OpenVINO™ モデル サーバーを使用してライブ ビデオを分析する 

このチュートリアルでは、Intel の AI 拡張機能 OpenVINO™ モデル サーバーを使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 この推論サーバーで、物体 (人物、車両、バイク) を検出するためのモデルや車両を分類するためのモデルにアクセスできるようにする方法を見ていきましょう。 ライブ ビデオ フィード内のフレームのサブセットが推論サーバーに送信され、その結果が IoT Edge ハブに送信されます。

このチュートリアルでは、IoT Edge デバイスとして Azure VM を使用し、シミュレートされたライブ ビデオ ストリームも使用します。 これは、C# で記述されたサンプル コードに基づいており、クイックスタート「[モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)」を基に構築されています。

> [!NOTE]
> このチュートリアルでは、エッジ デバイスとして x86-64 コンピューターを使用する必要があります。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  > [!NOTE]
  > サービス プリンシパルを作成するためのアクセス許可を与えられた Azure サブスクリプションが必要です (**owner role** には、そのアクセス許可があります)。 適切なアクセス許可がない場合は、適切なアクセス許可をアカウント管理者に申請してください。 
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)のクイックスタートを完了していない場合は、必ず [Azure リソースの設定](detect-motion-emit-events-quickstart.md#set-up-azure-resources)手順を完了してください。

> [!TIP]
> Azure IoT Tools をインストールするときに、Docker のインストールを求められる場合があります。 このメッセージは無視してかまいません。

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する

Azure リソースを設定する際に、駐車場の短いビデオが、IoT Edge デバイスとして使用している、Azure の Linux VM にコピーされます。 このクイックスタートでは、このビデオ ファイルを使用してライブ ストリームをシミュレートします。

[VLC メディア プレーヤー](https://www.videolan.org/vlc/)などのアプリケーションを開きます。 Ctrl キーを押しながら N キーを押し、リンクを[ビデオ](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)に貼り付けて、再生を開始します。 駐車場にある車両の映像が表示されます。ほどんどの車両は停車していますが、1 台は動いています。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

このクイックスタートでは、Intel の AI 拡張機能 OpenVINO™ モデル サーバーと共に Live Video Analytics on IoT Edge を使用して、車両などの物体を検出したりそれらを分類したりします。 その結果として得られた推論イベントを IoT Edge ハブに発行します。

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="概要":::

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor)ノードにビデオ フレームを送信します。 

HTTP 拡張ノードは、プロキシの役割を果たします。 これは、 `samplingOptions` フィールドによって設定された受信ビデオフ レームをサンプリングし、ビデオ フレームを指定された画像の種類に変換します。 次に、その画像を、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、REST 経由で中継します。 この例では、そのエッジ モジュールが Intel の AI 拡張機能 OpenVINO™ モデル サーバーです。 HTTP 拡張プロセッサ ノードは、検出結果を収集し、イベントを [IoT Hub シンク](media-graph-concept.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub) に送信します。

このチュートリアルでは、次のことについて説明します。

1. メディア グラフを作成してデプロイし、変更を加える。
1. 結果を解釈する。
1. リソースをクリーンアップする。

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Intel の AI 拡張機能 OpenVINO™ モデル サーバーについて

Intel® のディストリビューション [OpenVINO™ ツールキット](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (Open Visual Inference and Neural Network Optimization) は無料のソフトウェア キットで、開発者やデータ サイエンティストがコンピューター ビジョン ワークロードをスピードアップしたり、ディープ ラーニングの推論およびデプロイを効率化したり、エッジからクラウドまで Intel® プラットフォーム全体で簡単に異種実行環境を実現したりする際に役立ちます。 モデル オプティマイザーと推論エンジンを備えた Intel® ディープ ラーニング デプロイメント ツールキットのほか、40 を超える最適化された事前トレーニング済みモデルを含んだ [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) リポジトリが同梱されています。

複雑なハイパフォーマンスのライブ ビデオ分析ソリューションを構築するには、Live Video Analytics on IoT Edge モジュールに、エッジのスケールを活かせる強力な推論エンジンを組み合わせる必要があります。 このチュートリアルでは、Live Video Analytics on IoT Edge と連動するように設計された Edge モジュールである [Intel の AI 拡張機能 OpenVINO™ モデル サーバー](https://aka.ms/lva-intel-ovms)に推論要求が送信されます。 この推論サーバー モジュールには、OpenVINO™ モデル サーバー (OVMS) が含まれています。これは OpenVINO™ ツールキットを使用する推論サーバーであり、コンピューター ビジョン ワークロード向けに高度に最適化され、また Intel® アーキテクチャ向けに開発されています。 OVMS には、推論サーバーと Live Video Analytics on IoT Edge モジュールの間でビデオ フレームおよび推論結果を簡単に交換するための拡張機能が追加されているため、OpenVINO™ ツールキットに対応したモデルであれば、どのようなモデルでも実行することができます (推論サーバー モジュールは、[コード](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)を変更することでカスタマイズできます)。 さらに、Intel® のハードウェアが提供するさまざまなアクセラレーション メカニズムから選択することができます。 これらには、CPU (Atom、Core、Xeon)、FPGA、VPU が含まれます。

この推論サーバーの初期リリースでは、次の[モデル](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)を利用できます。

- 車両検出 (推論 URL: http://{module-name}:4000/vehicleDetection)
- 人物、車両、バイクの検出 (推論 URL: http://{module-name}:4000/personVehicleBikeDetection)
- 車両の分類 (推論 URL: http://{module-name}:4000/vehicleClassification)
- 顔検出 (推論 URL: http://{module-name}:4000/faceDetection)

> [!NOTE]
> Edge モジュール (Intel の AI 拡張機能 OpenVINO モデル サーバーとそこに含まれるソフトウェア) をダウンロードして利用したことをもって、ユーザーは[ライセンス契約](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)の利用条件に同意したものと見なされます。
> Intel は人権を尊重すること、および人権侵害の共謀を回避することに全力で取り組んでいます。 [Intel のグローバルな人権の原則](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)に関するページを参照してください。 Intel の製品やソフトウェアは、国際的に認知された人権への侵害の原因となったり侵害に寄与したりすることのないアプリケーションでの使用のみを意図しています。

## <a name="create-and-deploy-the-media-graph"></a>メディア グラフを作成してデプロイする

### <a name="examine-and-edit-the-sample-files"></a>サンプル ファイルを調べて編集する

前提条件の一環として、サンプル コードをフォルダーにダウンロードしてあります。 サンプル ファイルを確認して編集するには、これらの手順に従います。

1. Visual Studio Code で、*src/edge* に移動します。 *.env* ファイルと、いくつかの展開テンプレート ファイルがあります。

    展開テンプレートは、エッジ デバイスの配置マニフェストを参照しています。 そこには、いくつかのプレースホルダー値が含まれています。 *.env* ファイルには、それらの変数の値が格納されています。

1. *src/cloud-to-device-console-app* フォルダーに移動します。 ここには、*appsettings.json* ファイルと、他にいくつかのファイルがあります。

    * ***c2d-console-app.csproj*** - Visual Studio Code のプロジェクト ファイルです。
    * ***operations.json*** - プログラムで実行する操作のリストです。
    * ***Program.cs*** - サンプル プログラム コードです。 このコードによって以下が行われます。

        * アプリ設定を読み込みます。
        * Live Video Analytics on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用し、その[ダイレクト メソッド](direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。
        * プログラムの出力を **[ターミナル]** ウィンドウで調べたり、モジュールによって生成されたイベントを **[出力]** ウィンドウで調べたりできるように、一時停止します。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。


1. *operations.json* ファイルを編集します。
    * グラフ トポロジへのリンクを変更します。

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * `GraphInstanceSet` で、前のリンクの値と一致するようにグラフ トポロジの名前を編集します。

      `"topologyName" : "InferencingWithOpenVINO"`

    * `GraphTopologyDelete` で、名前を編集します。

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

1. *src/edge/deployment.openvino.template.json* ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。

    ![IoT Edge 配置マニフェストの生成](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.openvino.amd64.json* マニフェスト ファイルが *src/edge/config* フォルダーに作成されます。

1. クイックスタート「[モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)」を完了している場合は、この手順をスキップしてください。 

    それ以外の場合は、左下隅の **[Azure IoT Hub]** ペインの近くにある **[その他のアクション]** アイコンを選択して、 **[Set IoT Hub Connection String]\(IoT Hub 接続文字列の設定\)** を選択します。 この文字列は、*appsettings.json* ファイルからコピーできます。 または、Visual Studio Code 内で適切な IoT ハブが構成されていることを確認するには、[[Select IoT hub]\(IoT ハブの選択\) コマンド](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)を使用します。
    
    ![IoT Hub 接続文字列を設定する](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> IoT ハブに使用する組み込みのエンドポイント情報を入力するよう求められる場合があります。 この情報を入手するには、Azure portal で IoT ハブに移動し、左側のナビゲーション ペインで **[組み込みのエンドポイント]** オプションを探します。 それをクリックし、 **[イベント ハブ互換エンドポイント]** セクションの **[イベント ハブ互換エンドポイント]** を探します。 ボックス内のテキストをコピーして使用します。 エンドポイントは次のようになります。  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. *src/edge/config/deployment.openvino.amd64.json* を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 

    ![単一デバイスのデプロイを作成する](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、 **[lva-sample-device]** を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

    * **lvaEdge** という名前の Live Video Analytics モジュール
    * **rtspsim** モジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します
    * **openvino** モジュール。Intel の AI 拡張機能モジュールである OpenVINO™ モデル サーバーです。 

### <a name="prepare-to-monitor-events"></a>イベントの監視の準備をする

Live Video Analytics デバイスを右クリックし、 **[組み込みイベント エンドポイントの監視を開始する]** を選択します。 この手順は、Visual Studio Code の **[出力]** ウィンドウで、IoT Hub イベントを監視するために必要です。 

![監視の開始](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>サンプル プログラムを実行して車両を検出する
このチュートリアルの[グラフ トポロジ](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json)をブラウザーで開くと、`inferencingUrl` の値が `http://openvino:4000/vehicleDetection` に設定されていることがわかります。これは、ライブ ビデオで車両が検出された場合に、推論サーバーから結果が返されることを意味します。

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. マウスの右ボタンをクリックし、 **[拡張機能の設定]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="拡張機能の設定":::
1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="詳細メッセージの表示":::
1. デバッグ セッションを開始するには、F5 キーを押します。 **[ターミナル]** ウィンドウにメッセージが出力されるのを確認できます。
1. *operations.json* コードは、ダイレクト メソッド `GraphTopologyList` および `GraphInstanceList` の呼び出しから始まります。 前回のクイックスタートを完了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから、一時停止します。 続行するには、Enter キーを押します。

    **[ターミナル]** ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。

     * 前の `topologyUrl` を使用する `GraphTopologySet` の呼び出し
     * 次の本文を使用する `GraphInstanceSet` の呼び出し。

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               }
             ]
           }
         }
         ```

     * グラフ インスタンスとビデオのフローを開始する `GraphInstanceActivate` の呼び出し
     * グラフ インスタンスが実行状態であることを示す `GraphInstanceList` の 2 回目の呼び出し
1. **[ターミナル]** ウィンドウの出力は `Press Enter to continue` プロンプトで一時停止します。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の **[出力]** ウィンドウに切り替えます。 Live Video Analytics on IoT Edge モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. メディア グラフは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 メディア グラフを停止するには、 **[ターミナル]** ウィンドウに戻り、Enter キーを押します。 

    次の一連の呼び出しによって、リソースがクリーンアップされます。
      * `GraphInstanceDeactivate` の呼び出しによって、グラフ インスタンスが非アクティブ化されます。
      * `GraphInstanceDelete` の呼び出しによって、インスタンスが削除されます。
      * `GraphTopologyDelete` の呼び出しによって、トポロジが削除されます。
      * `GraphTopologyList` の最後の呼び出しによって、リストが空であることが示されます。

## <a name="interpret-results"></a>結果を解釈する

メディア グラフを実行すると、HTTP 拡張プロセッサ ノードから IoT Hub シンク ノードを介して IoT ハブに結果が渡されます。 **[出力]** ウィンドウに表示されるメッセージには、`body` セクションと `applicationProperties` セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージ内のアプリケーションのプロパティと body の内容は、Live Video Analytics モジュールによって定義されています。 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

メディア グラフがインスタンス化されると、RTSP ソース ノードは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続を試みます。 接続に成功すると、次のイベントが出力されます。 イベントの種類は、**Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished** です。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

このメッセージでは、以下の詳細に注目します。

* このメッセージは、診断イベントです。 `MediaSessionEstablished` は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* `applicationProperties` 内の `subject` は、メッセージがメディア グラフの RTSP ソース ノードから生成されたことを示しています。
* `applicationProperties` 内の `eventType` は、このイベントが診断イベントであることを示しています。
* `eventTime` はイベントの発生時刻を示しています。
* `body` には、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

### <a name="inference-event"></a>推論イベント

HTTP 拡張プロセッサ ノードは、AI 拡張機能モジュールの OpenVINO™ モデル サーバーから推論の結果を受け取ります。 次に、IoT Hub シンク ノードを介して、推論イベントとして結果を出力します。 

これらのイベントの種類は `entity` に設定されており、それが車やトラックなどのエンティティであることを示しています。 `eventTime` の値は、オブジェクトが検出された UTC 時刻です。 

次の例では、2 つの車両が検出されています。信頼度の値は 0.9 を超えています。

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

メッセージでは、次の詳細に注目します。

* `applicationProperties` 内の `subject` は、メッセージの生成元となった、グラフ トポロジ内のノードを参照しています。 
* `applicationProperties` 内の `eventType` は、このイベントが分析イベントであることを示しています。
* `eventTime` 値は、イベントが発生した時刻です。
* `body` セクションには、分析イベントに関するデータが含まれています。 このケースでは、イベントは推論イベントであるため、本文には `inferences` データが含まれています。
* `inferences` セクションは、`type` が `entity` であることを示しています。 このセクションには、エンティティに関する追加のデータが含まれています。

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>サンプル プログラムを実行して人物、車両、またはバイクを検出する
別のモデルを使用する場合は、グラフ トポロジと `operations.json` ファイルに変更を加える必要があります。

[グラフ トポロジ](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json)をローカル ファイル (`C:\TEMP\topology.json` など) にコピーします。 そのコピーを開き、`inferencingUrl` の値を `http://openvino:4000/personVehicleBikeDetection` に変更します。

次に、Visual Studio Code で *src/cloud-to-device-console-app* フォルダーに移動し、`operations.json` ファイルを開きます。 `topologyUrl` を含む行を次のように編集します。

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
これで上記の手順を繰り返すと、新しいトポロジを使用して、再びサンプル プログラムを実行できるようになりました。 推論の結果は、車両検出モデルのものと (スキーマ) が似ています。`subtype` が `personVehicleBikeDetection` に設定されるだけです。

## <a name="run-the-sample-program-to-classify-vehicles"></a>サンプル プログラムを実行して車両を分類する
Visual Studio Code で、前の手順で使用した `topology.json` のローカル コピーを開き、`inferencingUrl` の値を `http://openvino:4000/vehicleClassification` に変更します。 前の例を実行して人物、車両、またはバイクを検出した場合、再度 `operations.json` ファイルを変更する必要はありません。

これで上記の手順を繰り返すと、新しいトポロジを使用して、再びサンプル プログラムを実行できるようになりました。 分類の結果のサンプルは次のとおりです。

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>サンプル プログラムを実行して顔を検出する
Visual Studio Code で、前の手順で使用した `topology.json` のローカル コピーを開き、`inferencingUrl` の値を `http://openvino:4000/faceDetection` に変更します。 前の例を実行して人物、車両、またはバイクを検出した場合、再度 `operations.json` ファイルを変更する必要はありません。

これで上記の手順を繰り返すと、新しいトポロジを使用して、再びサンプル プログラムを実行できるようになりました。 サンプルの検出結果は次のとおりです (注: 上で使用した駐車場のビデオには、検出可能な顔が一切含まれていません。このモデルを試すには別のビデオが必要です)。

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートまたはチュートリアルに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このチュートリアルを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

上級ユーザー向けのその他の課題を確認します。

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで検索できます。 プロファイル G、S、または T に準拠しているデバイスを探します。
* Azure Linux VM ではなく、AMD64 または x64 Linux デバイスを使用してください。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../iot-edge/how-to-install-iot-edge.md)に関するページの手順を参照できます。 次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../iot-edge/quickstart-linux.md)」の手順に従って、デバイスを Azure IoT Hub に登録します。
