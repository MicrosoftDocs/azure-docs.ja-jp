---
title: Live Video Analytics on IoT Edge と Azure Custom Vision でライブ ビデオを分析する
description: Azure Custom Vision を使用して、おもちゃのトラックを検出できるコンテナー化されたモデルを構築し、Azure Live Video Analytics on Azure IoT Edge の AI 拡張機能を使用して、ライブ ビデオ ストリームからおもちゃのトラックを検出するためにそのモデルをエッジにデプロイする方法について説明します。
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e6b911ad555c4ce10a9576861e1c2e650e63cd94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698809"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>チュートリアル:Live Video Analytics on IoT Edge と Azure Custom Vision でライブ ビデオを分析する

このチュートリアルでは、Azure [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) を使用して、おもちゃのトラックを検出できるコンテナー化されたモデルを構築し、Azure Live Video Analytics on Azure IoT Edge の [AI 拡張機能](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model)を使用して、ライブ ビデオ ストリームからおもちゃのトラックを検出するためにそのモデルをエッジにデプロイする方法について説明します。

ここでは、Custom Vision の機能を集約して、いくつかの画像をアップロードしてラベル付けすることでコンピューター ビジョン モデルを構築してトレーニングする方法について説明します。 データ サイエンス、機械学習、AI の知識は必要ありません。 また、簡単にカスタム モデルをコンテナーとしてエッジにデプロイし、シミュレートされたライブ ビデオ フィードを分析するために、Live Video Analytics の機能についても説明します。

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * 関連リソースを設定する。
> * おもちゃのトラックを検出するための Custom Vision モデルをクラウドに構築してエッジにデプロイする。
> * HTTP 拡張を使用したメディア グラフを作成して Custom Vision モデルにデプロイする。
> * サンプル コードを実行する。
> * 結果を確認して解釈する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>推奨される事前読み取り  

開始する前に、次の記事に目を通してください。

* [IoT Edge の Live Video Analytics (プレビュー)](overview.md)
* [Azure Custom Vision の概要](../../cognitive-services/custom-vision-service/overview.md)
* [Live Video Analytics on IoT Edge の用語](terminology.md)
* [メディア グラフの概念](media-graph-concept.md)
* [ビデオ記録を行わない Live Video Analytics](analyze-live-video-concept.md)
* [ご自分のモデルを使用して Live Video Analytics を実行する](use-your-model-quickstart.md)
* [チュートリアル:IoT Edge モジュールを開発する](../../iot-edge/tutorial-develop-for-linux.md)
* [deployment.*.template.json を編集する方法](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>前提条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end

> [!IMPORTANT]
> この Custom Vision モジュールでは、**Intel x86 および amd64** アーキテクチャだけがサポートされています。 続行する前に、エッジ デバイスのアーキテクチャを確認してください。

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する

このチュートリアルでは、[おもちゃの車の推論ビデオ](https://lvamedia.blob.core.windows.net/public/t2.mkv) ファイルを使用してライブ ストリームをシミュレートします。 このビデオは、[VLC media player](https://www.videolan.org/vlc/) などのアプリケーションを使用して確認できます。 [Ctrl+N](https://lvamedia.blob.core.windows.net/public/t2.mkv) キーを押し、**おもちゃの車の推論ビデオ** へのリンクを貼り付けて、再生を開始します。 ビデオを見ると、36 秒の時点でおもちゃのトラックがビデオに出てくることがわかります。 このカスタム モデルは、この特定のおもちゃのトラックを検出するようにトレーニングされています。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LPwK]

このチュートリアルでは、Live Video Analytics on IoT Edge を使用して、このようなおもちゃのトラックを検出し、関連付けられた推論イベントを IoT Edge ハブに発行します。

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Custom Vision の概要を示す図。":::

この図は、このチュートリアルでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor)ノードにビデオ フレームを送信します。

HTTP 拡張ノードは、プロキシの役割を果たします。  これは、`samplingOptions` フィールドを使用して設定された受信ビデオフ レームをサンプリングし、ビデオ フレームを指定された画像の種類に変換します。 次に、その画像を、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、REST 経由で転送します。 この例では、このエッジ モジュールは Custom Vision を使用して構築された、おもちゃのトラックの検出モデルです。 HTTP 拡張プロセッサ ノードは、検出結果を収集し、イベントを [Azure IoT Hub シンク](media-graph-concept.md#iot-hub-message-sink) ノードに発行します。 その後、このノードはこれらのイベントを [IoT Edge ハブ](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)に送信します。

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Custom Vision のおもちゃ検出モデルを構築してデプロイする 

Custom Vision という名前が示唆するように、これを使用して、独自のカスタム オブジェクト検出器または分類器をクラウドに構築できます。 これには、コンテナーを介してクラウドまたはエッジにデプロイできる Custom Vision モデルを構築するためのシンプルで使いやすい、直感的なインターフェイスが用意されています。

おもちゃのトラックの検出器を構築するには、「[クイックスタート: Custom Vision の Web サイトでオブジェクト検出器を構築する](../../cognitive-services/custom-vision-service/get-started-build-detector.md)」を参照してください。

> [!IMPORTANT]
> この Custom Vision モジュールでは、**Intel x86 および amd64** アーキテクチャだけがサポートされています。 続行する前に、エッジ デバイスのアーキテクチャを確認してください。

その他のメモ:
 
* このチュートリアルでは、このクイックスタート記事の[「前提条件」セクション](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)にあるサンプル画像を使用しないでください。 ここでは、代わりに特定の画像セットを使用して、おもちゃの検出器の Custom Vision モデルを構築しました。 [これらの画像](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip)は、クイックスタートで[トレーニング画像を選択する](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images)よう求められたときに使用してください。
* クイック スタートの画像へのタグ付けに関するセクションでは、図に示されているおもちゃのトラックに "delivery truck" というタグを付けていることを確認してください。

完了したら、 **[パフォーマンス]** タブの **[エクスポート]** ボタンを使用して、モデルを Docker コンテナーにエクスポートできます。コンテナー プラットフォームの種類として Linux を選択するようにしてください。 これは、コンテナーが実行されるプラットフォームです。 コンテナーをダウンロードするコンピューターは、Windows でも Linux でもかまいません。 次の手順は、Windows コンピューターにダウンロードされたコンテナー ファイルに基づいています。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Dockerfile が選択されていることを示す画面。":::
 
1. `<projectname>.DockerFile.Linux.zip` という名前の zip ファイルがローカル コンピューターにダウンロードされている必要があります。 
1. Docker がインストールされているかどうかを確認します。 されていない場合は、お使いの Windows デスクトップ用の [Docker](https://docs.docker.com/get-docker/) をインストールします。
1. ダウンロードしたファイルを任意の場所に解凍します。 コマンド ラインを使用して、解凍したフォルダー ディレクトリにアクセスします。
    
    次のコマンドを実行します。
    
    1. `docker build -t cvtruck` 
    
        このコマンドは、多くのパッケージをダウンロードし、Docker 画像を構築して、それに `cvtruck:latest` をタグ付けします。
    
        > [!NOTE]
        > 成功した場合は、`Successfully built <docker image id>` および `Successfully tagged cvtruck:latest` というメッセージが表示されます。 このビルド コマンドが失敗した場合は、もう一度試してください。 場合によっては、最初は依存関係パッケージがダウンロードされないことがあります。
    1. `docker  image ls`

        このコマンドは、新しいイメージがローカル レジストリにあるかどうかを確認します。
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        このコマンドは、Docker の公開ポート (80) をローカル コンピューターのポート (80) に公開します。
    1. `docker container ls`
    
        このコマンドは、ポート マッピングと、Docker コンテナーが自分のマシン上で正常に実行されているかどうかを確認します。 出力は次のようになります。

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            このコマンドは、ローカル コンピューター上のコンテナーをテストします。 モデルをトレーニングしたときと同じデリバリー トラックが画像に含まれている場合、出力は次の例のようになります。 これは、デリバリー トラックが 90.12% の確率で検出されたことを示します。
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>サンプル ファイルを詳しく調べる

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>配置マニフェストを生成してデプロイする

1. Visual Studio Code で、src/cloud-to-device-console-app/operations.json に移動します。

1. `GraphTopologySet` で、次に該当することを確認します。<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`
1. `GraphInstanceSet` で、以下を確認します。
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. パラメーター配列の先頭に `{"name": "inferencingUrl","value": "http://cv:80/image"},` を追加します。
    1. `rtspUrl` パラメーター値を `"rtsp://rtspsim:554/media/t2.mkv"` に変更します。
1. `GraphTopologyDelete` で、`"name": "InferencingWithHttpExtension"` を確認します。
1. src/edge/ deployment.customvision.template.json ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge デプロイ マニフェストの生成\)** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="[Generate IoT Edge Deployment Manifest]\(IoT Edge デプロイ マニフェストの生成\) を示すスクリーンショット。":::
  
    この操作により、deployment.customvision.amd64.json という名前のマニフェスト ファイルが src/edge/config フォルダーに作成されます。
1. src/edge/ deployment.customvision.template.json ファイルを開き、`registryCredentials` JSON ブロックを見つけます。 このブロックでは、Azure コンテナー レジストリのアドレスと共に、そのユーザー名とパスワードを確認できます。
1. コマンド ラインで次の手順を実行して、ローカルの Custom Vision コンテナーを Azure Container Registry インスタンスにプッシュします。

    1. 次のコマンドを実行して、レジストリにサインインします。
    
        `docker login <address>`
    
        認証を求められたら、ユーザー名とパスワードを入力します。
        
        > [!NOTE]
        > このパスワードはコマンド ラインに表示されません。
    1. 次のコマンドを使用して画像にタグを付けます。 <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. 次のコマンドを使用して画像をプッシュします。 <br/>`docker push <address>/cvtruck`.

        成功した場合は、コマンド ラインにその画像の SHA と共に `Pushed` と表示されます。
    1. また、Azure portal で Azure Container Registry インスタンスをチェックすることによって確認することもできます。 ここでは、リポジトリの名前がタグと共に表示されます。
1. 左下隅の **[Azure IoT Hub]** ペインの横にある **[その他のアクション]** アイコンを選択して、IoTHub 接続文字列を設定します。 この文字列は、appsettings.json ファイルからコピーできます (Visual Studio Code 内で適切な IoT ハブを確実に構成するために推奨されるもう 1 つの方法として、[[Select IoT Hub]\(IoT ハブの選択\) コマンド](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)の使用があります)。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="[Set IoT Hub Connection String]\(IoT ハブ接続文字列を設定\) を示すスクリーンショット。":::
1. 次に、src/edge/config/ deployment.customvision.amd64.json を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\) を示すスクリーンショット":::
1. その後、IoT Hub デバイスを選択するよう求められます。 ドロップダウン リストから **[lva-sample-device]** を選択します。
1. 30 秒ほど経過したら、左下のセクションで Azure IoT ハブを更新します。 エッジ デバイスに次のモジュールがデプロイされた状態になります。

    * `lvaEdge` という名前の Live Video Analytics on IoT Edge モジュール。
    * `rtspsim` という名前のモジュール。ライブ ビデオ フィードのソースとして機能する RTSP サーバーをシミュレートします。
    * `cv` という名前のモジュール。これは名前が示唆するように、Custom Vision を画像に適用して複数のタグの種類を返す、Custom Vision のおもちゃのトラックの検出モデルです (このモデルは delivery truck という 1 つのタグのみでトレーニングされています)。

## <a name="prepare-for-monitoring-events"></a>イベントを監視するための準備をする

Live Video Analytics デバイスを右クリックし、 **[組み込みイベント エンドポイントの監視を開始します]** を選択します。 この手順は、Visual Studio Code の **[出力]** ウィンドウで、IoT Hub イベントを監視するために必要です。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="[組み込みイベント エンドポイントの監視を開始します] を示すスクリーンショット。":::

## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

このチュートリアルのグラフ トポロジをブラウザーで開くと、`inferencingUrl` の値が `http://cv:80/image` に設定されていることがわかります。 この設定は、推論サーバーがライブ ビデオでおもちゃのトラック (ある場合) を検出した後に結果を返すことを意味します。

1. Visual Studio Code で **[拡張機能]** タブを開き (または **Ctrl + Shift + X** キーを押し)、Azure IoT Hub を検索します。
1. 右クリックして、 **[拡張機能の設定]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="[拡張機能の設定] を示すスクリーンショット。":::
1. **[Show Verbose Message]\(詳細メッセージの表示\)** を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="[Show Verbose Message]\(詳細メッセージの表示\) を示すスクリーンショット。":::
1. デバッグ セッションを開始するには、**F5** キーを押します。 **[ターミナル]** ウィンドウにメッセージが出力されるのを確認できます。
1. operations.json コードは、ダイレクト メソッド `GraphTopologyList` および `GraphInstanceList` の呼び出しから始まります。 前のクイックスタートを完了した後にリソースをクリーンアップした場合、このプロセスは空のリストを返した後に一時停止します。 続行するには、**Enter** キーを押します。
    
   **[ターミナル]** ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。
    
   * 前述の `topologyUrl` を使用する `GraphTopologySet` の呼び出し。
   * 次の本文を使用する `GraphInstanceSet` の呼び出し。
        
   ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
1. **[ターミナル]** ウィンドウの出力が、 **[続行するには Enter キーを押してください]** というプロンプトで一時停止されます。 **Enter** キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の **[出力]** ウィンドウに切り替えます。 Live Video Analytics on IoT Edge モジュールから IoT ハブに送信されているメッセージが表示されます。 このチュートリアルの次のセクションでは、これらのメッセージについて説明します。
1. メディア グラフは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 メディア グラフを停止するには、 **[ターミナル]** ウィンドウに戻り、**Enter** キーを押します。
次の一連の呼び出しによって、リソースがクリーンアップされます。
    
   * `GraphInstanceDeactivate` の呼び出しによって、グラフ インスタンスが非アクティブ化されます。
   * `GraphInstanceDelete` の呼び出しによって、インスタンスが削除されます。
   * `GraphTopologyDelete` の呼び出しによって、トポロジが削除されます。
   * `GraphTopologyList` の最後の呼び出しによって、リストが空であることが示されます。
    
## <a name="interpret-the-results"></a>結果を解釈する

メディア グラフを実行すると、HTTP 拡張プロセッサ ノードから IoT Hub シンク ノードを介して IoT ハブに結果が渡されます。 **[出力]** ウィンドウに表示されるメッセージには、body セクションと `applicationProperties` セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージ内のアプリケーションのプロパティと body の内容は、Live Video Analytics モジュールによって定義されています。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

メディア グラフがインスタンス化されると、RTSP ソース ノードは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続を試みます。 接続に成功すると、次のイベントが出力されます。 イベントの種類は、**Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished** です。

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

このメッセージでは、以下の詳細に注目します。

* このメッセージは、診断イベントです。 `MediaSessionEstablished` は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、シミュレートされたライブ フィードの受信を開始したことを示します。
* `applicationProperties` 内の `subject` は、メッセージがメディア グラフの RTSP ソース ノードから生成されたことを示しています。
* `applicationProperties` 内のイベントの種類は、このイベントが診断イベントであることを示します。
* イベント時刻は、イベントの発生時刻を示します。
* body には、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

### <a name="inference-event"></a>推論イベント

HTTP 拡張プロセッサ ノードは、Custom Vision コンテナーから推論結果を受け取り、その結果を推論イベントとして IoT Hub シンク ノードを介して出力します。

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

上記のメッセージに含まれる次の情報に注意してください。

* `applicationProperties` 内の subject は、メッセージの生成元となった、MediaGraph 内のノードを参照しています。 この場合、このメッセージは HTTP 拡張プロセッサから生成されます。
* `applicationProperties` 内のイベントの種類は、これが分析推論イベントであることを示しています。
* イベント時刻は、イベントの発生時刻を示します。
* body には、分析イベントに関するデータが含まれています。 この場合、イベントは推論イベントであるため、body には predictions という推論の配列が含まれています。
* predictions セクションには、フレーム内でおもちゃのデリバリー トラック (タグは "delivery truck") が見つかる予測のリストが含まれています。 ご存じのとおり、"delivery truck" は、おもちゃのトラック用にトレーニングされたカスタム モデルに指定したカスタム タグです。 このモデルは、さまざまな確率の信頼度スコアで入力ビデオ内のおもちゃのトラックを推論して特定します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のチュートリアルやクイックスタートに取り組む予定がある場合は、作成したリソースをそのまま残しておいてください。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このチュートリアルで使用したリソース グループを選択して、すべてのリソースを削除してください。

## <a name="next-steps"></a>次のステップ

上級ユーザー向けのその他の課題を確認します。

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで検索できます。 プロファイル G、S、または T に準拠しているデバイスを探します。
* Azure Linux VM ではなく、AMD64 または x64 Linux デバイスを使用してください。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../iot-edge/how-to-install-iot-edge.md)に関するページの手順を参照できます。

次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../iot-edge/quickstart-linux.md)」の手順に従って、デバイスを Azure IoT Hub に登録します。
