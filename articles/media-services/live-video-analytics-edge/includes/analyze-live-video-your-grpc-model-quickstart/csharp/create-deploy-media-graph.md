---
ms.openlocfilehash: 8a9149119bc754ff0715f2841925da01301faecd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749933"
---
### <a name="examine-and-edit-the-sample-files"></a>サンプル ファイルを調べて編集する

前提条件の一環として、サンプル コードをフォルダーにダウンロードしてあります。 サンプル ファイルを確認して編集するには、これらの手順に従います。

1. Visual Studio Code で、*src/edge* に移動します。 *.env* ファイルと、いくつかの展開テンプレート ファイルがあります。

    deployment.grpcyolov3icpu.template.json は、エッジ デバイスの配置マニフェストを参照しています。 そこには、いくつかのプレースホルダー値が含まれています。 .env ファイルには、それらの変数の値が格納されています。
1. *src/cloud-to-device-console-app* フォルダーに移動します。 ここには、*appsettings.json* ファイルと、他にいくつかのファイルがあります。
    
    * c2d-console-app.csproj - Visual Studio Code のプロジェクト ファイルです。
    * operations.json - プログラムで実行する操作のリストです。
    * Program.cs - サンプル プログラム コードです。 このコードによって以下が行われます。

        * アプリ設定を読み込みます。
        * Live Video Analytics on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用し、そのダイレクト メソッドを呼び出すことで、ライブ ビデオ ストリームを分析できます。
        * プログラムの出力を **[ターミナル]** ウィンドウで調べたり、モジュールによって生成されたイベントを **[出力]** ウィンドウで調べたりできるように、一時停止します。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。
1. *operations.json* ファイルを編集します。
 
    * グラフ トポロジへのリンクを変更します。
    * `"topologyUrl"` : `"https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-with-grpcExtension/2.0/topology.json"`
    * GraphInstanceSet で、前のリンクの値と一致するようにグラフ トポロジの名前を編集します。
    * `"topologyName"` : `"EVROnMotionPlusGrpcExtension"`
    * GraphTopologyDelete で、名前を編集します。
    * `"name"` : `"EVROnMotionPlusGrpcExtension"`

> [!NOTE]
> <p>
> <details>
> <summary>これを展開し、MediaGraphGrpcExtension ノードがトポロジで実装されているしくみを確認する</summary>
> <pre><code>
> {
>   "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
>   "name": "grpcExtension",
>   "endpoint": {
>       "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
>       "url": "${grpcExtensionAddress}",
>       "credentials": {
>           "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
>           "username": "${grpcExtensionUserName}",
>           "password": "${grpcExtensionPassword}"
>       }
>   },
>   "dataTransfer": {
>       "mode": "sharedMemory",
>       "SharedMemorySizeMiB": "256"
>   },
>   "image": {
>       "scale": {
>           "mode": "${imageScaleMode}",
>           "width": "${frameWidth}",
>           "height": "${frameHeight}"
>       },
>       "format": {
>           "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
>           "encoding": "${imageEncoding}",
>           "quality": "${imageQuality}"
>       }
>   },
>   "inputs": [
>       {
>           "nodeName": "motionDetection"
>       }
>   ]
> }          
> </code></pre>
> </details>    
> </p>

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

1. *src/edge/* *deployment.grpcyolov3icpu.template.json* ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。

    ![IoT Edge 配置マニフェストの生成](../../../media/quickstarts/generate-iot-edge-deployment-manifest-grpc.png)

    *deployment.grpcyolov3icpu.amd64.json* マニフェスト ファイルが *src/edge/config* フォルダーに作成されます。
1. クイックスタート「[モーションの検出とイベントの生成](../../../detect-motion-emit-events-quickstart.md)」を完了している場合は、この手順をスキップしてください。

    それ以外の場合は、左下隅の **[Azure IoT Hub]** ペインの近くにある **[その他のアクション]** アイコンを選択して、 **[Set IoT Hub Connection String]\(IoT Hub 接続文字列の設定\)** を選択します。 この文字列は、*appsettings.json* ファイルからコピーできます。 または、Visual Studio Code 内で適切な IoT ハブが構成されていることを確認するには、[[Select IoT hub]\(IoT ハブの選択\) コマンド](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)を使用します。

    ![IoT Hub 接続文字列](../../../media/quickstarts/iot-hub-connection-string-grpc.png)

    > [!NOTE]
    > IoT ハブに使用する組み込みのエンドポイント情報を入力するよう求められる場合があります。 この情報を入手するには、Azure portal で IoT ハブに移動し、左側のナビゲーション ペインで **[組み込みのエンドポイント]** オプションを探します。 それをクリックし、 **[イベント ハブ互換エンドポイント]** セクションの **[イベント ハブ互換エンドポイント]** を探します。 ボックス内のテキストをコピーして使用します。 エンドポイントは次のようになります。  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. *src/edge/config/* *deployment.grpcyolov3icpu.amd64.json* を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

    ![単一デバイスのデプロイの作成](../../../media/quickstarts/create-deployment-single-device-grpc.png)
1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、 **[lva-sample-device]** を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

    * **lvaEdge** という名前の Live Video Analytics モジュール。
    * **rtspsim** モジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します。

        > [!NOTE]
        > 上記の手順は、セットアップ スクリプトによって作成された仮想マシンの使用を前提としています。 独自のエッジ デバイスを使用している場合は、エッジ デバイスにアクセスし、**管理者権限** で次のコマンドを実行して、このクイックスタートで使用するサンプル ビデオ ファイルをプルして保存します。  

        ```
        mkdir /home/lvaedgeuser/samples
        mkdir /home/lvaedgeuser/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
        chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
        ```
    * **lvaExtension** モジュール。これは、通信方法として gRPC を使用し、コンピューター ビジョンを画像に適用して、オブジェクトの種類のクラスを複数返す YOLOv3 オブジェクト検出モデルです。
    
    ![lva 拡張機能](../../../media/quickstarts/lvaextension-grpc.png)

### <a name="prepare-to-monitor-events"></a>イベントの監視の準備をする

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. マウスの右ボタンをクリックし、 **[拡張機能の設定]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="拡張機能の設定":::
1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="詳細メッセージの表示":::
1. Live Video Analytics デバイスを右クリックし、 **[組み込みイベント エンドポイントの監視を開始する]** を選択します。 この手順は、Visual Studio Code の **[出力]** ウィンドウで、IoT Hub イベントを監視するために必要です。

   ![監視の開始](../../../media/quickstarts/start-monitoring-built-event-endpoint-grpc.png)

    > [!NOTE]
    > IoT ハブに使用する組み込みのエンドポイント情報を入力するよう求められる場合があります。 この情報を入手するには、Azure portal で IoT ハブに移動し、左側のナビゲーション ペインで **[組み込みのエンドポイント]** オプションを探します。 それをクリックし、 **[イベント ハブ互換エンドポイント]** セクションの **[イベント ハブ互換エンドポイント]** を探します。 ボックス内のテキストをコピーして使用します。 エンドポイントは次のようになります。  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
### <a name="run-the-sample-program"></a>サンプル プログラムを実行する

1. デバッグ セッションを開始するには、F5 キーを押します。 [ターミナル] ウィンドウにメッセージが出力されるのを確認できます。
1. *operations.json* コードは、ダイレクト メソッド `GraphTopologyList` および `GraphInstanceList` の呼び出しから始まります。 前回のクイックスタートを完了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから、一時停止します。 続行するには、Enter キーを押します。
    
    ```
    -------------------------------Executing operation GraphTopologyList-----------------------  
    Request: GraphTopologyList  --------------------------------------------------
    {
    "@apiVersion": "2.0"
    }
    ---------------  
    Response: GraphTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    **[ターミナル]** ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。
    
    * 前の topologyUrl を使用する `GraphTopologySet` の呼び出し。
    * 次の本文を使用する `GraphInstanceSet` の呼び出し。
    
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph-1",
      "properties": {
        "topologyName": "EVROnMotionPlusGrpcExtension",
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
    
    * グラフ インスタンスとビデオのフローを開始する `GraphInstanceActivate` の呼び出し。
    * グラフ インスタンスが実行状態であることを示す `GraphInstanceList` の 2 回目の呼び出し。
1. **[ターミナル]** ウィンドウの出力が、[続行するには Enter キーを押してください] というプロンプトで一時停止されます。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の **[出力]** ウィンドウに切り替えます。 Live Video Analytics on IoT Edge モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. メディア グラフは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 メディア グラフを停止するには、 **[ターミナル]** ウィンドウに戻り、Enter キーを押します。
1. 次の一連の呼び出しによって、リソースがクリーンアップされます。
    
    * `GraphInstanceDeactivate` の呼び出しによって、グラフ インスタンスが非アクティブ化されます。
    * `GraphInstanceDelete` の呼び出しによって、インスタンスが削除されます。
    * `GraphTopologyDelete` の呼び出しによって、トポロジが削除されます。
    * `GraphTopologyList` の最後の呼び出しによって、リストが空であることが示されます。

