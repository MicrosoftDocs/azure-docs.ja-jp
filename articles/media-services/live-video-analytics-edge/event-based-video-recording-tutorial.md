---
title: クラウドへのイベントベースのビデオ記録とクラウドからの再生に関するチュートリアル - Azure
description: このチュートリアルでは、Azure Live Video Analytics on Azure IoT Edge を使用して、イベントベースのビデオ録画をクラウドに記録し、これをクラウドから再生する方法について説明します。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: ea98b4c8981be9fffe7911e4c8402a8f522976f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702319"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>チュートリアル:クラウドへのイベントベースのビデオ記録とクラウドからの再生

このチュートリアルでは、Azure Live Video Analytics on Azure IoT Edge を使用して、クラウド内の Azure Media Services にライブ ビデオ ソースの一部を選択的に記録する方法について説明します。 このチュートリアルでは、このユース ケースを[イベントベースのビデオ記録](event-based-video-recording-concept.md) (EVR) と呼んでいます。 ライブ ビデオの一部を記録するには、オブジェクト検出 AI モデルを使用してビデオ内のオブジェクトを検索し、特定の種類のオブジェクトが検出された場合にのみビデオ クリップを記録します。 また、記録したビデオ クリップを Media Services を使用して再生する方法についても説明します。 この機能は、関心のあるビデオ クリップのアーカイブを保持する必要があるさまざまなシナリオで役立ちます。 

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * 関連リソースを設定する。
> * EVR を実行するコードを確認する。
> * サンプル コードを実行する。
> * 結果を確認し、ビデオを表示する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

始める前に、以下の記事をお読みください。

* [IoT Edge の Live Video Analytics (プレビュー)](overview.md)
* [Live Video Analytics on IoT Edge の用語](terminology.md)
* [メディア グラフの概念](media-graph-concept.md) 
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)
* [チュートリアル:IoT Edge モジュールを開発する](../../iot-edge/tutorial-develop-for-linux.md)
* [deployment.*.template.json を編集する方法](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* [IoT Edge 配置マニフェスト内でルートを宣言する方法](../../iot-edge/module-composition.md#declare-routes)に関するセクション

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。

* [Visual Studio Code](https://code.visualstudio.com/)。[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) および [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 拡張機能と共に、ご自分の開発マシンにインストールされている必要があります。

    > [!TIP]
    > Docker のインストールを求められる場合があります。 このメッセージは無視してください。
* 開発マシンにインストールされた [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* [Live Video Analytics リソース設定スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)の完了と[環境の設定](./detect-motion-emit-events-quickstart.md?pivots=programming-language-csharp#set-up-your-development-environment)

これらの手順を完了すると、以下の関連する Azure リソースがご自分の Azure サブスクリプションにデプロイされます。

* Azure IoT Hub
* Azure ストレージ アカウント
* Azure Media Services アカウント
* Azure 上の Linux VM ([IoT Edge ランタイム](../../iot-edge/how-to-install-iot-edge.md)がインストール済み)

> [!TIP]
> 作成された Azure リソースで問題が発生した場合は、 **[トラブルシューティング ガイド](troubleshoot-how-to.md#common-error-resolutions)** を参照して、よく発生する問題を解決してください。

## <a name="concepts"></a>概念

イベントベースのビデオ記録とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 このイベントは、以下から生成されます。
- ビデオ信号自体の処理 (移動するオブジェクトがビデオ内で検出された場合など)。
- 独立したソース (ドアが開いた場合など)。 

また、特定のイベントが発生したことを推論サービスが検出した場合にのみ記録をトリガーすることもできます。 このチュートリアルでは、高速道路を移動する車両のビデオを使用し、トラックが検出されるたびにビデオ クリップを記録します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="メディア グラフ":::

図は、目的のシナリオを実現する[メディア グラフ](media-graph-concept.md)と追加のモジュールを視覚的に表現したものです。 次の 4 つの IoT Edge モジュールが関与します。

* IoT Edge モジュール上の Live Video Analytics。
* HTTP エンドポイントの背後で AI モデルを実行する Edge モジュール。 この AI モジュールでは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) モデルを使用します。
* オブジェクトをカウントおよびフィルター処理するためのカスタム モジュール (図では、Object Counter (オブジェクト カウンター) となっています)。 このチュートリアルでは、オブジェクト カウンターを作成してデプロイします。
* RTSP カメラをシミュレートする[RTSP シミュレーター モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)。
    
図に示すように、メディア グラフ内の [RTSP ソース](media-graph-concept.md#rtsp-source) ノードを使用して、シミュレートされた (高速道路のトラフィックの) ライブ ビデオをキャプチャし、そのビデオを次の 2 つのパスに送信します。

* 1 つ目は、HTTP 拡張ノードへのパスです。 このノードは、`samplingOptions` フィールドを使用して設定された値にビデオ フレームをサンプリングした後、フレームを画像として AI モジュール YOLO v3 (オブジェクト検出器) にリレーします。 ノードは結果 (モデルによって検出されたオブジェクト、つまりトラフィック内の車両) を受信します。 次に、HTTP 拡張ノードは、IoT Hub メッセージ シンク ノードを介して IoT Edge ハブに結果を発行します。
* objectCounter モジュールは、IoT Edge ハブからメッセージを受信するように設定されています。このメッセージには、オブジェクト検出の結果 (トラフィック内の車両) が含まれています。 モジュールではこれらのメッセージを確認して、(設定を介して構成された) 特定の種類のオブジェクトを探します。 そのようなオブジェクトが見つかると、このモジュールから IoT Edge ハブにメッセージが送信されます。 "物体が見つかった" というこれらのメッセージは、メディア グラフの IoT Hub ソース ノードにルーティングされます。 このようなメッセージを受信すると、メディア グラフの IoT Hub ソース ノードによって[シグナル ゲート プロセッサ](media-graph-concept.md#signal-gate-processor) ノードがトリガーされます。 すると、構成された時間だけシグナル ゲート プロセッサ ノードが開きます。 その間、ビデオがゲートを通じて資産シンク ノードに流れます。 ライブ ストリームのその部分は、[資産シンク](media-graph-concept.md#asset-sink) ノードを介して、ご自分の Azure Media Services アカウント内の[資産](terminology.md#asset)に記録されます。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

開始する前に、[前提条件](#prerequisites)の 3 番目の項目が完了していることを確認してください。 リソース設定スクリプトが完了したら、中かっこを選択してフォルダー構造を展開します。 ~/clouddrive/lva-sample ディレクトリに、いくつかのファイルが作成されていることがわかります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="アプリ設定":::

このチュートリアルで注目するのは、次のファイルです。

* **~/clouddrive/lva-sample/edge-deployment/.env**: Visual Studio Code がエッジ デバイスにモジュールをデプロイするために使用するプロパティが格納されています。
* **~/clouddrive/lva-sample/appsetting.json**: サンプル コードを実行するために、Visual Studio Code によって使用されます。

これらのファイルは、以下の手順で必要になります。

1. GitHub リンク https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp からリポジトリをクローンします。
1. Visual Studio Code を起動して、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、src/cloud-to-device-console-app フォルダーに移動し、**appsettings.json** という名前のファイルを作成します。 このファイルには、プログラムを実行するために必要な設定が格納されています。
1. ~/clouddrive/lva-sample/appsettings.json ファイルの内容をコピーします。 このテキストは次のようになっています。

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    IoT Hub 接続文字列を使用すると、Visual Studio Code を使用して Azure IoT Hub 経由でエッジ モジュールにコマンドを送信できます。
    
1. 次に、src/edge フォルダーに移動して、 **.env** という名前のファイルを作成します。
1. ~/clouddrive/lva-sample/edge-deployment/.env ファイルの内容をコピーします。 このテキストは次のようになっています。

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>テンプレート ファイルを詳しく調べる 

前の手順では、Visual Studio Code を起動し、サンプル コードが含まれているフォルダーを開きました。

Visual Studio Code で src/edge に移動します。 作成した .env ファイルと、いくつかの展開テンプレート ファイルを確認できます。 このテンプレートでは、エッジ デバイス (Azure Linux VM) にデプロイするエッジ モジュールを定義します。 .env ファイルには、Media Services の資格情報など、これらのテンプレート内で使用される変数の値が含まれています。

src/edge/deployment.objectCounter.template.json を開きます。 **modules** セクションには、前の「概念」のセクションに示した項目に対応する 4 つのエントリがあります。

* **lvaEdge**: これは Live Video Analytics on IoT Edge モジュールです。
* **yolov3**: これは、YOLO v3 モデルを使用して構築された AI モジュールです。
* **rtspsim**: これは RTSP シミュレーターです。
* **objectCounter**: これは、yolov3 の結果から特定のオブジェクトを検索するモジュールです。

ObjectCounter モジュールについては、"image" 値に使用される (${MODULES.objectCounter}) 文字列を参照してください。 これは、IoT Edge モジュールの開発に関する[チュートリアル](../../iot-edge/tutorial-develop-for-linux.md)に基づいています。 Visual Studio Code は、objectCounter モジュールのコードが src/edge/modules/objectCounter にあることを自動的に認識します。 

IoT Edge 配置マニフェスト内でルートを宣言する方法に関する[このセクション](../../iot-edge/module-composition.md#declare-routes)を参照してください。 次に、テンプレート JSON ファイル内でルートを確認します。 次の方法に注目してください。

* LVAToObjectCounter は、objectCounter モジュール内の特定のエンドポイントに特定のイベントを送信するために使用されます。
* ObjectCounterToLVA は、lvaEdge モジュール内の特定のエンドポイント (IoT Hub ソース ノード) にトリガー イベントを送信するために使用されます。
* objectCounterToIoTHub は、デバッグ ツールとして使用され、このチュートリアルを実行した際に objectCounter からの出力を確認するのに役立ちます。

> [!NOTE]
> objectCounter モジュールの必要なプロパティを確認してください。このモジュールは、信頼レベルが 50% 以上で "トラック" とタグ付けされたオブジェクトを検索するように設定されています。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする 

エッジ デバイスにデプロイされるモジュールとそれらのモジュールの構成設定は、配置マニフェストによって定義されます。 マニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、これらの手順に従います。

Visual Studio Code を使用し、[こちらの手順](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)に従って Docker にサインインします。 次に、 **[Build and Push IoT Edge Solution]\(IoT Edge ソリューションをビルドしてプッシュする\)** を選択します。 この手順では、src/edge/deployment.objectCounter.template.json を使用します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="IoT Edge ソリューションをビルドしてプッシュする":::

このアクションにより、オブジェクト カウント用の objectCounter モジュールがビルドされ、お使いの Azure Container Registry に画像がプッシュされます。

* 環境変数 CONTAINER_REGISTRY_USERNAME_myacr と CONTAINER_REGISTRY_PASSWORD_myacr が .env ファイル内で定義されていることを確認してください。

この手順により、src/edge/config/deployment.objectCounter.amd64.json に IoT Edge 配置マニフェストが作成されます。 そのファイルを右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="単一デバイスのデプロイを作成する":::

これが Live Video Analytics on IoT Edge を使用する最初のチュートリアルである場合は、IoT Hub 接続文字列を入力するように Visual Studio Code から求められます。 これは、appsettings.json ファイルからコピーできます。

> [!NOTE]
> IoT ハブに使用する組み込みのエンドポイント情報を入力するよう求められる場合があります。 この情報を入手するには、Azure portal で IoT ハブに移動し、左側のナビゲーション ペインで **[組み込みのエンドポイント]** オプションを探します。 それをクリックし、 **[イベント ハブ互換エンドポイント]** セクションの **[イベント ハブ互換エンドポイント]** を探します。 ボックス内のテキストをコピーして使用します。 エンドポイントは次のようになります。  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

次に、IoT Hub デバイスを選択するように Visual Studio Code から求められます。 IoT Edge デバイス (lva-sample-device) を選択します。

この段階で、お使いの IoT Edge デバイスへのエッジ モジュールのデプロイが開始されました。
30 秒ほど経過したら、Visual Studio Code の左下のセクションで Azure IoT Hub を最新の情報に更新してください。 そうすると、lvaEdge、rtspsim、yolov3、および objectCounter という名前の 4 つのモジュールがデプロイされていることがわかります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="デプロイされた 4 つのモジュール":::

## <a name="prepare-for-monitoring-events"></a>イベントを監視するための準備をする

objectCounter モジュールおよび Live Video Analytics on IoT Edge モジュールからのイベントを表示するには、これらの手順を実行します。

1. Visual Studio Code の [エクスプローラー] ペインを開き、左下隅にある **[Azure IoT Hub]** を探します。
1. **[デバイス]** ノードを展開します。
1. lva-sample-device ファイルを右クリックし、 **[組み込みイベント エンドポイントの監視を開始します]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="組み込みイベント エンドポイントの監視を開始する":::

    > [!NOTE]
    > IoT ハブに使用する組み込みのエンドポイント情報を入力するよう求められる場合があります。 この情報を入手するには、Azure portal で IoT ハブに移動し、左側のナビゲーション ペインで **[組み込みのエンドポイント]** オプションを探します。 それをクリックし、 **[イベント ハブ互換エンドポイント]** セクションの **[イベント ハブ互換エンドポイント]** を探します。 ボックス内のテキストをコピーして使用します。 エンドポイントは次のようになります。  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
## <a name="run-the-program"></a>プログラムの実行

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. マウスの右ボタンをクリックし、 **[拡張機能の設定]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="拡張機能の設定":::
1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="詳細メッセージの表示":::
1. <!--In Visual Studio Code, go-->src/cloud-to-device-console-app/operations.js に移動します。
1. **GraphTopologySet** ノードで、次を編集します。

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json"`
    
1. 次に、**GraphInstanceSet** ノードと **GraphTopologyDelete** ノードで、次のように編集します。

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. F5 キーを押して、デバッグ セッションを開始します。 **[ターミナル]** ウィンドウにいくつかのメッセージが出力されます。
1. operations.json ファイルは、GraphTopologyList および GraphInstanceList の呼び出しで開始されます。 前回のクイックスタートまたはチュートリアルの後にリソースをクリーンアップした場合は、このアクションによって空のリストが返された後、一時停止して、以下のように **Enter** キーの入力待ち状態になります。

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
1. **[ターミナル]** ウィンドウで **Enter** キーを押すと、次に示す一連のダイレクト メソッド呼び出しが実行されます。
   * 前の topologyUrl を使用した GraphTopologySet の呼び出し
   * 次の body を使用した GraphInstanceSet の呼び出し
     
        ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
            "description": "Sample graph description",
            "parameters": [
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
    
   * グラフ インスタンスを起動し、ビデオのフローを開始する GraphInstanceActivate の呼び出し
   * グラフ インスタンスが実行状態であることを表示する GraphInstanceList の 2 回目の呼び出し
     
1. **[ターミナル]** ウィンドウの出力が、今度は **[続行するには Enter キーを押してください]** というプロンプトで一時停止状態となります。 この時点では **Enter** キーは押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. ここで Visual Studio Code の **[出力]** ウィンドウに切り替えると、Live Video Analytics on IoT Edge モジュールによって IoT Hub に送信されたメッセージが表示されます。

   これらのメッセージについては、次のセクションで説明します。
1. 引き続きグラフ インスタンスが実行され、ビデオが記録されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 次のセクションで説明するように、メッセージを確認します。 次に、インスタンスを停止するには、 **[ターミナル]** ウィンドウに戻り、**Enter** キーを押します。 次の一連の呼び出しが行われて、リソースがクリーンアップされます。

   * グラフ インスタンスを非アクティブ化する GraphInstanceDeactivate の呼び出し。
   * インスタンスを削除する GraphInstanceDelete の呼び出し。
   * トポロジを削除する GraphTopologyDelete の呼び出し。
   * リストが空になったことを示す GraphTopologyList の最後の呼び出し。

## <a name="interpret-the-results"></a>結果を解釈する 

メディア グラフを実行すると、Live Video Analytics on IoT Edge モジュールによって、特定の診断および操作イベントが IoT Edge ハブに送信されます。 これらのイベントは Visual Studio Code の **出力** ウィンドウに表示されるメッセージです。 それらには、body セクションと applicationProperties セクションが存在します。 これらのセクションが表す内容については、「[IoT Hub メッセージを作成し、読み取る](../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージ内のアプリケーションのプロパティと body の内容は、Live Video Analytics モジュールによって定義されています。

## <a name="diagnostics-events"></a>診断イベント

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント 

メディア グラフがインスタンス化されると、RTSP ソース ノードは、RTSP シミュレーター コンテナーで実行されている RTSP サーバーへの接続を試みます。 成功した場合、このイベントが出力されます。 イベントの種類は、**Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished** です。

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* メッセージは診断イベント (MediaSessionEstablished) です。 これは、RTSP ソース ノード (subject) によって RTSP シミュレーターとの接続が確立され、(シミュレートされた) ライブ フィードの受信が開始されたことを示します。
* applicationProperties の subject セクションが、メッセージの生成元となったグラフ トポロジ内のノードを参照しています。 このケースでは、RTSP ソース ノードからメッセージが生成されています。
* applicationProperties の eventType セクションには、これが Diagnostics イベントであることが示されています。
* eventTime セクションにはイベントの発生時刻が示されています。 これは、トラフィック ビデオ (MKV ファイル) がライブ ストリームとしてモジュールに到達し始めた時刻です。
* body セクションには、診断イベントに関するデータ (このケースでは [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細) が含まれています。


## <a name="operational-events"></a>操作イベント

しばらくの間メディア グラフが実行されると、最終的に objectCounter モジュールからイベントが返されます。 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties セクションには、イベントの時刻が含まれています。 これは、objectCounter モジュールが、yolov3 モジュールの結果に対象オブジェクト (トラック) が含まれていることを確認した時刻です。

ビデオ内で他のトラックが検出された場合は、このイベントが他にも表示されているのを確認できます。

### <a name="recordingstarted-event"></a>RecordingStarted イベント

オブジェクト カウンターからイベントが送信されると、そのほぼ直後に種類が **Microsoft.Media.Graph.Operational.RecordingStarted** のイベントが次のように表示されます。

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

applicationProperties の subject セクションが、このメッセージの生成元となったグラフ内の資産シンク ノードを参照しています。 body セクションには、出力場所に関する情報が含まれています。 このケースでは、ビデオが記録される Azure Media Services 資産の名前です。 この値を書き留めておきます。

### <a name="recordingavailable-event"></a>RecordingAvailable イベント

資産シンク ノードがビデオを資産にアップロードすると、種類が **Microsoft.Media.Graph.Operational.RecordingAvailable** のイベントが次のように出力されます。

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

このイベントは、プレーヤーまたはクライアントがビデオの再生を開始するのに十分なデータが資産に書き込まれたことを示します。 applicationProperties の subject セクションが、このメッセージの生成元となったグラフ内の AssetSink ノードを参照しています。 body セクションには、出力場所に関する情報が含まれています。 このケースでは、ビデオが記録される Azure Media Services 資産の名前です。

### <a name="recordingstopped-event"></a>RecordingStopped イベント

[トポロジ](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)内のシグナル ゲート プロセッサ ノードのアクティブ化設定 (maximumActivationTime) を確認すると、ビデオが 30 秒間送信された後にゲートが閉じるように設定されていることがわかります。 RecordingStarted イベントの約 30 秒後に、種類が **Microsoft.Media.Graph.Operational.RecordingStopped** のイベントが表示されます。 このイベントは、資産シンク ノードが資産へのビデオ記録を停止したことを示します。

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

このイベントは、記録が停止したことを示します。 applicationProperties の subject セクションが、このメッセージの生成元となったグラフ内の AssetSink ノードを参照しています。 body セクションには、出力場所に関する情報が含まれています。 このケースでは、ビデオが記録される Azure Media Services 資産の名前です。

## <a name="media-services-asset"></a>Media Services 資産  

グラフによって作成された Media Services 資産を確認するには、Azure portal にログインし、ビデオを表示します。

1. Web ブラウザーを開き、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。
1. ご自分のサブスクリプション内のリソースの中からお使いの Media Services アカウントを見つけます。 アカウント ペインを開きます。
1. **[Media Services]** の一覧で、 **[資産]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="継続的なビデオ記録":::
1. sampleAssetFromEVR-LVAEdge-{DateTime} という名前の資産が見つかります。 これは、RecordingStarted イベントの outputLocation プロパティに指定されている名前です。 この名前の生成方法は、トポロジ内の assetNamePattern によって決定されます。
1. 資産を選択します。
1. 資産の詳細ページで、 **[ストリーミング URL]** テキスト ボックスの下の **[新規作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="新しい資産":::
1. 開いたウィザードで、既定のオプションをそのまま使用し、 **[追加]** を選択します。 詳細については、[ビデオ再生](video-playback-concept.md)に関するページをご覧ください。

    > [!TIP]
    > [ストリーミング エンドポイントが実行されている](../latest/streaming-endpoint-concept.md)ことを確認します。
1. プレーヤーでビデオを読み込む必要があります。 **[再生]** を選択して、それを表示します。

> [!NOTE]
> このビデオのソースはカメラ フィードをシミュレートするコンテナーであったため、ビデオのタイム スタンプは、グラフ インスタンスをアクティブ化した時点と、非アクティブ化した時点に関連付けられています。 「[数日間の記録の再生](playback-multi-day-recordings-tutorial.md)」チュートリアルに組み込まれている再生コントロールを使用している場合は、画面に表示されているビデオのタイム スタンプを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のチュートリアルに取り組む場合は、作成したリソースをそのまま残します。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このチュートリアルで使用したリソース グループを選択して、それらのリソース グループを削除してください。

## <a name="next-steps"></a>次のステップ

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラを検索するには、[ONVIF 準拠製品のページ](https://www.onvif.org/conformant-products/)でプロファイル G、S、または T に準拠しているデバイスを検索します。
* AMD64 または X64 Linux デバイスを使用します (Azure Linux VM を使用するのではなく)。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../iot-edge/how-to-install-iot-edge.md)に関するページの手順に従います。 「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../iot-edge/quickstart-linux.md)」クイックスタートの手順に従って、Azure IoT Hub にデバイスを登録します。