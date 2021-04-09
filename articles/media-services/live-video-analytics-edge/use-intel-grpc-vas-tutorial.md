---
title: gRPC 経由で Intel OpenVINO™ DL Streamer – Edge AI Extension を使用してライブ ビデオを分析する
description: このチュートリアルでは、Intel OpenVINO™ DL Streamer – Edge AI Extension を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 20a22d11973f5bb01e2c1345538d5d94ce311dc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465761"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>チュートリアル: Intel OpenVINO™ DL Streamer – Edge AI Extension を使用してライブ ビデオを分析する 

このチュートリアルでは、Intel OpenVINO™ DL Streamer – Edge AI Extension を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 この推論サーバーで、オブジェクト (人物、車両、バイク) を検出するためのさまざまなモデル、オブジェクトの分類 (車両の属性)、オブジェクト (人物、車両、バイク) 追跡用のモデルを利用する方法を見ていきます。 gRPC モジュールとの統合により、ビデオ フレームを AI 推論サーバーに送信できます。 結果は IoT Edge ハブに送信されます。 この推論サービスを Live Video Analytics と同じ計算ノードで実行すると、共有メモリを通じたビデオ データ送信を活用できます。 これにより、ライブ ビデオ フィードのフレーム レートで推論を実行できます (例: 30 フレーム/秒)。 

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

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

このクイックスタートでは、Intel の Intel OpenVINO™ DL Streamer – Edge AI Extension と共に Live Video Analytics on IoT Edge を使用して、車両などのオブジェクトの検出、それらの車両の分類、または車両、人物、バイクの追跡を行います。 その結果として得られた推論イベントを IoT Edge ハブに発行します。

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="LVA MediaGraph の概要":::

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[gRPC 拡張プロセッサ](media-graph-concept.md#grpc-extension-processor) ノードにビデオ フレームを送信します。 

gRPC 拡張プロセッサ ノードは、デコードされたビデオ フレームを入力として受け取り、そのようなフレームを、gRPC サーバーによって公開される [gRPC](terminology.md#grpc) エンドポイントにリレーします。 ノードでは、[共有メモリ](https://en.wikipedia.org/wiki/Shared_memory)を使用したデータの転送や、gRPC メッセージの本文へのコンテンツの直接埋め込みをサポートします。 また、ノードには、ビデオ フレームを gRPC エンドポイントにリレーする前に、スケールおよびエンコードするための組み込みのイメージ フォーマッタがあります。 スケーラーには、画像の縦横比を維持、埋め込み、または拡張するオプションがあります。 イメージ エンコーダーでは、jpeg、png、bmp 形式がサポートされています。 プロセッサの詳細については、[こちら](media-graph-extension-concept.md#grpc-extension-processor)をご覧ください。

このチュートリアルでは次のことを行います。

1. メディア グラフをデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Intel OpenVINO™ DL Streamer – Edge AI Extension モジュールの概要


OpenVINO™ DL Streamer – Edge AI Extension モジュールは、OpenVINO™ DL Streamer を使用して構築されたビデオ分析パイプラインを提供する Intel Video Analytics Serving (VA Serving) に基づくマイクロサービスです。 開発者は、検出、分類、または追跡を実行して結果を返す AI 拡張機能モジュールに、デコードされたビデオ フレームを送信できます。 AI 拡張機能モジュールは、Microsoft の Live Video Analytics on IoT Edge など、ビデオ分析プラットフォームと互換性のある gRPC API を公開します。 

複雑なハイパフォーマンスのライブ ビデオ分析ソリューションを構築するには、Live Video Analytics on IoT Edge モジュールに、エッジのスケールを活かせる強力な推論エンジンを組み合わせる必要があります。 このチュートリアルでは、Live Video Analytics on IoT Edge と連動するように設計されたエッジ モジュールである [Intel OpenVINO™ DL Streamer – Edge AI Extension](https://aka.ms/lva-intel-openvino-dl-streamer) に推論要求が送信されます。 

この推論サーバーの初期リリースでは、次の[モデル](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options)を利用できます。

- person_vehicle_bike_detection のための object_detection ![車両を対象とする物体検出](./media/use-intel-openvino-tutorial/object-detection.png)

- vehicle_attributes_recognition のための object_classification ![車両を対象とするオブジェクト分類](./media/use-intel-openvino-tutorial/object-classification.png)

- person_vehicle_bike_tracking のための object_tracking ![人物および車両を対象とするオブジェクト追跡](./media/use-intel-openvino-tutorial/object-tracking.png)

すぐに開始できるように、物体検出、オブジェクト分類、オブジェクト追跡のプリロード済みパイプラインを使用します。 また、プリロード済みの [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) および [vehicle-attributes-recognition-barrier-0039 モデル](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md)が用意されています。

> [!NOTE]
> エッジ モジュール (Intel の OpenVINO™ DL Streamer – Edge AI Extension とそこに含まれるソフトウェア) をダウンロードして利用したことをもって、ユーザーは[ライセンス契約](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)のご契約条件に同意したものと見なされます。
> Intel は人権を尊重すること、および人権侵害の共謀を回避することに全力で取り組んでいます。 [Intel のグローバルな人権の原則](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)に関するページを参照してください。 Intel の製品やソフトウェアは、国際的に認知された人権への侵害の原因となったり侵害に寄与したりすることのないアプリケーションでの使用のみを意図しています。

デプロイ テンプレートのパイプライン環境変数を変更するだけで、お客様固有のユース ケースに合わせてさまざまなパイプラインを柔軟に使用できます。 これにより、パイプライン モデルを迅速に変更できます。Live Video Analytics と組み合わせると、メディア パイプラインと推論モデルの変更は数秒で済みます。  

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

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * `GraphInstanceSet` で、前のリンクの値と一致するようにグラフ トポロジの名前を編集します。

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * `GraphTopologyDelete` で、名前を編集します。

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

1. *src/edge/deployment.openvino.grpc.cpu.template.json* ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge デプロイ マニフェストの生成\)** を選択します。

    ![IoT Edge 配置マニフェストの生成](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.openvino.grpc.cpu.amd64.json* マニフェスト ファイルが *src/edge/config* フォルダーに作成されます。

> [!NOTE]
> また、Intel OpenVINO DL Streamer - Edge AI Extension モジュール向けの GPU サポートを有効にする *deployment.openvino.grpc.gpu.template.json* テンプレートも用意されています。 これらのテンプレートでは Intel の Docker ハブ イメージを参照します。

前述のテンプレートでは、Intel の Docker ハブ イメージを参照します。 独自の Azure コンテナー レジストリでコピーをホストしたい場合は、下記の手順 1. および 2. に従ってください。
1. Docker CLI ツールがインストールされたデバイス (つまり自分のエッジ デバイス) に SSH 接続し、これらの手順でコンテナーをプル、タグ付け、プッシュします。
    * Docker ハブから Intel のイメージをプルします。

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Intel のイメージに独自の Azure コンテナー レジストリ名でタグを付けます。 {YOUR ACR NAME} を、.env ファイルで確認できる自分の ACR 名に置き換えます。

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * タグ付けしたイメージを自分の Azure コンテナー レジストリにプッシュします。

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. 次に、Azure Container Registry 上でホストされている新しいイメージを参照するように、テンプレートを編集します。
    * *deployment.openvino.grpc.cpu.template.json* を右クリックし、*lavExtension* モジュールの部分に移動し、置換を行います。

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        次の内容に置き換えます。

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * *deployment.openvino.grpc.gpu.template.json* について手順 2. を繰り返します。


3. クイックスタート「[モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)」を完了している場合は、この手順をスキップしてください。 

    それ以外の場合は、左下隅の **[Azure IoT Hub]** ペインの近くにある **[その他のアクション]** アイコンを選択して、 **[Set IoT Hub Connection String]\(IoT Hub 接続文字列の設定\)** を選択します。 この文字列は、*appsettings.json* ファイルからコピーできます。 または、Visual Studio Code 内で適切な IoT ハブが構成されていることを確認するには、[[Select IoT hub]\(IoT ハブの選択\) コマンド](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)を使用します。
    
    ![IoT Hub 接続文字列を設定する](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> IoT ハブに使用する組み込みのエンドポイント情報を入力するよう求められる場合があります。 この情報を入手するには、Azure portal で IoT ハブに移動し、左側のナビゲーション ペインで **[組み込みのエンドポイント]** オプションを探します。 それをクリックし、 **[イベント ハブ互換エンドポイント]** セクションの **[イベント ハブ互換エンドポイント]** を探します。 ボックス内のテキストをコピーして使用します。 エンドポイントは次のようになります。  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. *src/edge/config/deployment.openvino.grpc.cpu.template.json* を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 

    ![単一デバイスのデプロイを作成する](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、 **[lva-sample-device]** を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

    * **lvaEdge** という名前の Live Video Analytics モジュール
    * **rtspsim** モジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します
    * **lvaExtension** モジュール。これは Intel OpenVINO™ DL Streamer – Edge AI Extension です 

### <a name="prepare-to-monitor-events"></a>イベントの監視の準備をする

Live Video Analytics デバイスを右クリックし、 **[組み込みイベント エンドポイントの監視を開始する]** を選択します。 この手順は、Visual Studio Code の **[出力]** ウィンドウで、IoT Hub イベントを監視するために必要です。 

![監視の開始](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>サンプル プログラムを実行して車両、人物、またはバイクを検出する
ブラウザーでこのチュートリアルの [グラフ トポロジ](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json)を開くと、`grpcExtensionAddress` の値が `tcp://lvaExtension:5001` に設定されていることがわかります。*httpExtensionOpenVINO* サンプルとは対照的に、URL を gRPC サーバーに変更する必要はありません。 代わりに、前述のように環境変数によって特定のパイプラインを実行するようモジュールに指示します。 既定のテンプレートでは、"person_vehicle_bike_detection" のための "object_detection" に設定されています。 サポートされている他のパイプラインで実験することができます。 

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
             "topologyName": "InferencingWithOpenVINOgRPC",
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
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

このメッセージでは、以下の詳細に注目します。

* このメッセージは、診断イベントです。 `MediaSessionEstablished` は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* `applicationProperties` 内の `subject` は、メッセージがメディア グラフの RTSP ソース ノードから生成されたことを示しています。
* `applicationProperties` 内の `eventType` は、このイベントが診断イベントであることを示しています。
* `eventTime` はイベントの発生時刻を示しています。
* `body` には、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

### <a name="inference-event"></a>推論イベント

gRPC 拡張プロセッサ ノードが Intel OpenVINO™ DL Streamer – Edge AI Extension から推論の結果を受け取ります。 次に、IoT Hub シンク ノードを介して、推論イベントとして結果を出力します。 

これらのイベントの種類は `entity` に設定されており、それが車やトラックなどのエンティティであることを示しています。 `eventTime` の値は、オブジェクトが検出された UTC 時刻です。 

次の例では、すべて 0.9 を上回る信頼度レベルで車両、車両の種類 (バン)、色 (白) が特定されたことがわかります。また、オブジェクト追跡モデルを使用する際のエンティティに ID が割り当てられました。

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
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
別のモデルを使用するには、デプロイ テンプレートを変更する必要があります。 サポート対象のモデルを切り替えるには、lvaExtenstion モジュール内の環境変数を変更します。 サポートされているモデルの値と組み合わせについては、こちらの [GitHub 上のドキュメント](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options)を参照してください。

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> 可能なパイプラインごとにテンプレートをコピーして新しい名前で保存してください。 そうすることで、それらのいずれかのテンプレートに基づいて新しいデプロイを作成し、モデルを切り替えられます。

変数の変更が完了したら、テンプレートをデバイスに再度デプロイできます。 これで上記の手順を繰り返すと、新しいパイプラインを使用して、再びサンプル プログラムを実行できるようになりました。 推論の結果は (スキーマが) 似たものになります。ただし、表示される情報は選択したパイプライン モデルに応じて増減します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートまたはチュートリアルに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このチュートリアルを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

上級ユーザー向けのその他の課題を確認します。

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで検索できます。 プロファイル G、S、または T に準拠しているデバイスを探します。
* Azure Linux VM ではなく、Intel x64 Linux デバイスを使用します。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../iot-edge/how-to-install-iot-edge.md)に関するページの手順を参照できます。 次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../iot-edge/quickstart-linux.md)」の手順に従って、デバイスを Azure IoT Hub に登録します。
