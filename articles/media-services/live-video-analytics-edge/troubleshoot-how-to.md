---
title: Live Video Analytics on IoT Edge のトラブルシューティング - Azure
description: この記事では、Live Video Analytics on IoT Edge のトラブルシューティング手順について説明します。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d766843f58bc2cdd0dcdddfad337b23fefb28768
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698741"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Live Video Analytics on IoT Edge のトラブルシューティング

この記事では、Live Video Analytics (LVA) on Azure IoT Edge のトラブルシューティング手順について説明します。

## <a name="troubleshoot-deployment-issues"></a>デプロイに関する問題のトラブルシューティング

### <a name="diagnostics"></a>診断

Live Video Analytics をデプロイする一環として、IoT Hub デバイスや IoT Edge デバイスなどの Azure リソースを設定します。 問題を診断するための最初の手順として、次の指示に従って、エッジ デバイスが適切に設定されていることを必ず確認してください。

1. [`check` コマンドを実行する](../../iot-edge/troubleshoot.md#run-the-check-command)。
1. [IoT Edge のバージョンを確認する](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)。
1. [IoT Edge Security Manager の状態とそのログを確認する](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)。
1. [IoT Edge ハブを通過するメッセージを表示する](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)。
1. [コンテナーを再起動する](../../iot-edge/troubleshoot.md#restart-containers)。
1. [ファイアウォール規則とポート構成規則を確認する](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="pre-deployment-issues"></a>デプロイ前の問題

エッジ インフラストラクチャが正常な場合は、配置マニフェスト ファイルの問題を調べることができます。 Live Video Analytics on IoT Edge モジュールを他の IoT モジュールと共に IoT Edge デバイスにデプロイするには、IoT Edge ハブ、IoT Edge エージェント、その他のモジュールとそれらのプロパティを含む配置マニフェストを使用します。 次のコマンドを使用すれば、マニフェスト ファイルをデプロイできます。

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
JSON コードの形式が正しくない場合、次のエラーが表示されることがあります。   
&nbsp;&nbsp;&nbsp; **Failed to parse JSON from file: '<deployment manifest.json>' for argument 'content' with exception:"Extra data: line 101 column 1 (char 5325)"**

このエラーが発生した場合は、かっこの不足やファイルの構造に関するその他の問題がないか、JSON を確認することをお勧めします。 ファイル構造を検証するには、[JSON Viewer プラグイン搭載の Notepad++](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) などのクライアントや、[JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/) などのオンライン ツールを使用できます。

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>デプロイ中:メディア グラフのダイレクト メソッドを使用した診断 

Live Video Analytics on IoT Edge モジュールが IoT Edge デバイスに正しくデプロイされると、[ダイレクト メソッド](direct-methods.md)を呼び出すことによって、メディア グラフを作成して実行できるようになります。  
>[!NOTE]
>  ダイレクト メソッド呼び出しは、 **`lvaEdge`** モジュールに対してのみ行う必要があります。

Azure portal を使用して、ダイレクト メソッドを使用してメディア グラフの診断を実行できます。

1. Azure portal で、IoT Edge デバイスに接続されている IoT ハブにアクセスします。

1. **[デバイスの自動管理]** を探し、 **[IoT Edge]** を選択します。  

1. エッジ デバイスの一覧で、診断するデバイスを選択します。  
         
    ![エッジ デバイスの一覧が表示された Azure portal のスクリーンショット](./media/troubleshoot-how-to/lva-sample-device.png)


1. 応答コードが *200-OK* かどうかを確認します。 [IoT Edge ランタイム](../../iot-edge/iot-edge-runtime.md)の応答コードには、他にも次のものがあります。
    * 400 - デプロイ構成が正しくない形式であるか、無効です。
    * 417 - デバイスにデプロイ構成セットがありません。
    * 412 - デプロイ構成にあるスキーマ バージョンが無効です。
    * 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
    * 500 - IoT Edge ランタイムでエラーが発生しました。

    > [!TIP]
    > お使いの環境で Azure IoT Edge モジュールを実行するときに問題が発生した場合は、トラブルシューティングと診断のガイドとして **[Azure IoT Edge の標準的な診断手順](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** に関する記事を参照してください。
### <a name="post-deployment-direct-method-error-code"></a>デプロイ後:ダイレクト メソッドのエラー コード
1. 状態 `501 code` のコードが表示された場合は、ダイレクト メソッド名が正確であることを確認してください。 メソッド名と要求ペイロードが正確である場合は、成功コード = 200 と共に結果が返されます。 
1. 要求ペイロードが不正確な場合は、状態 `400 code` と、ダイレクト メソッド呼び出しに関する問題の診断に役立つエラー コードとメッセージを示す応答ペイロードが表示されます。
    * 報告される、および必要なプロパティを確認すると、モジュールのプロパティがデプロイと同期されているかどうかを理解するのに役立ちます。 同期されていない場合は、IoT Edge デバイスを再起動できます。 
    * 「[ダイレクト メソッド](direct-methods.md)」ガイドを使用して、いくつかのメソッド (特に GraphTopologyList のような単純なメソッド) を呼び出します。 このガイドでは、予想される要求、応答のペイロードとエラーコードも指定します。 単純なダイレクト メソッドが正常に完了する場合は、Live Video Analytics on IoT Edge モジュールが機能的には問題ないことを確認できます。
        
       ![IoT Edge モジュールの [ダイレクト メソッド] ペインのスクリーンショット。](./media/troubleshoot-how-to/direct-method.png) 

1. **[デプロイで指定]** 列と **[デバイス別に報告]** 列に *[はい]* が示されている場合は、Live Video Analytics on IoT Edge モジュールでダイレクト メソッドを呼び出すことができます。 モジュールを選択すると表示されるページで、必要な、および報告されるプロパティを確認し、ダイレクト メソッドを呼び出すことができます。 以下の点にご注意ください。 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>デプロイ後:実行時の問題に関するログの診断 

IoT Edge モジュールのコンテナー ログには、モジュールの実行時の問題をデバッグするのに役立つ診断情報が含まれています。 [コンテナー ログで問題を確認](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)し、その問題を自己診断できます。 

上記のすべてのチェックを実行しても問題が解決しない場合は、Azure チームがさらに分析できるように、[`support bundle` コマンドを使用](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)して、IoT Edge デバイスからログを収集してください。 [サポート ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)で Microsoft に問い合わせて、収集したログを送信していただくことができます。

## <a name="common-error-resolutions"></a>よくあるエラーの解決方法

Live Video Analytics は、IoT Edge デバイスに IoT Edge モジュールとしてデプロイされ、IoT Edge エージェントおよびハブ モジュールと連携して動作します。 Live Video Analytics のデプロイで発生する一般的なエラーのいくつかは、基になる IoT インフラストラクチャの問題が原因で生じます。 このようなエラーには次のものが含まれます。

* [約 1 分後に IoT Edge エージェントが停止する](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)。
* [IoT Edge エージェントがモジュールのイメージにアクセスできない (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403)。
* [IoT Edge エージェント モジュールで "空の構成ファイル" がレポートされ、デバイスでモジュールが開始しない](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
* [IoT Edge ハブが起動に失敗する](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)。
* [IoT Edge セキュリティ デーモンが無効なホスト名で失敗する](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
* [Live Video Analytics またはその他のカスタム IoT Edge モジュールがエッジ ハブへのメッセージ送信に 404 エラーで失敗する](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
* [IoT Edge モジュールがデプロイに成功したのに、デバイスに表示されなくなる](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device)。

    > [!TIP]
    > お使いの環境で Azure IoT Edge モジュールを実行するときに問題が発生した場合は、トラブルシューティングと診断のガイドとして **[Azure IoT Edge の標準的な診断手順](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** に関する記事を参照してください。

**[Live Video Analytics リソース設定スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)** の実行時に問題が発生する場合もあります。 一般的な問題としては、次のようなものがあります。

* 所有者特権を持っていないサブスクリプションの使用。 この場合、**ForbiddenError** または **AuthorizationFailed** のエラーでスクリプトが失敗します。
    * この問題を解決するには、使用する予定のサブスクリプションの **所有者** 特権が自身に付与されるようにします。 これを自分で行うことができない場合は、サブスクリプション管理者に連絡して、適切な特権を付与するよう依頼してください。
* **ポリシー違反に起因してテンプレートのデプロイが失敗しました。**
    * この問題を解決するには、IT 管理者と協力して、仮想マシンを作成するための呼び出しで、ssh 認証のブロックがバイパスされるようにしてください。 Azure リソースとの通信にはユーザー名とパスワードを必要とするセキュリティで保護された Bastion ネットワークを使用しているため、これは必要ありません。 これらの資格情報は、仮想マシンが正常に作成され、IoT Hub にデプロイされアタッチされた後、Cloud Shell の **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** ファイルに保存されます。
* セットアップ スクリプトで、サービス プリンシパル、Azure リソース、またはこの両方を作成できない。
    * この問題を解決するには、サブスクリプションと Azure テナントがサービスの上限に達していないことを確認してください。 詳細については、「[Azure AD サービスの制限と制約](../../active-directory/enterprise-users/directory-service-limits-restrictions.md)」と「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。

> [!TIP]
> 支援が必要な追加の問題がある場合は、 **[ログを収集し、サポート チケットを送信](#collect-logs-for-submitting-a-support-ticket)** してください。 **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** 宛にメールで問い合わせることもできます。
### <a name="live-video-analytics-working-with-external-modules"></a>Live Video Analytics による外部モジュールの操作

メディア グラフ拡張プロセッサ経由の Live Video Analytics では、メディア グラフを拡張し、他の IoT Edge モジュールに対して HTTP または gRPC プロトコルを使用してデータを送受信することができます。 [具体的な例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)として、メディア グラフでは、Yolo v3 などの外部推論モジュールへビデオ フレームをイメージとして送信し、HTTP プロトコルを使用して JSON ベースの分析結果を受け取ることができます。 このようなトポロジでは、イベントの送信先は主に IoT ハブです。 ハブで推論イベントが表示されない場合は、次の点を確認します。

* メディア グラフの公開先となるハブと、調査中のハブが同じであるかどうかを確認します。 複数のデプロイを作成すると、ハブが複数作成され、イベントに対して間違ったハブを誤って確認していることがあります。
* Azure portal で、外部モジュールがデプロイされて実行されているかどうかを確認します。 この例の図では、rtspsim、yolov3、tinyyolov3、logAnalyticsAgent は、lvaEdge モジュールの外部で実行されている IoT Edge モジュールです。

    [ ![Azure IoT Hub のモジュールの実行状態を表示するスクリーンショット。](./media/troubleshoot-how-to/iot-hub-azure.png) ](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* 正しい URL エンドポイントにイベントを送信しているかどうかを確認します。 外部 AI コンテナーは、URL と、POST 要求からデータを受信して返すポートを公開します。 この URL は、HTTP 拡張プロセッサの `endpoint: url` プロパティとして指定されます。 [トポロジ URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) に示されているように、エンドポイントは推論の URL パラメーターに設定されています。 このパラメーターの既定値、または渡された値が正確であることを確認します。 クライアント URL (cURL) を使用することにより、それが機能しているかどうかをテストできます。  

    例として、ローカル コンピューター上で実行されている Yolo v3 コンテナー (IP アドレス: 172.17.0.3) があるとします。  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    返される結果:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > **[Docker 検査コマンド](https://docs.docker.com/engine/reference/commandline/inspect/)** を使用して、マシンの IP アドレスを検索します。
    
* メディア グラフ拡張プロセッサを利用するグラフのインスタンスを 1 つ以上実行している場合は、ビデオ フィードの 1 秒あたりのフレーム数 (fps) を管理するため、`samplingOptions` フィールドを使用する必要があります。 

   * エッジ マシンの CPU やメモリの使用率が高い特定の状況では、特定の推論イベントが失われることがあります。 この問題に対処するには、`samplingOptions` フィールドで `maximumSamplesPerSecond` プロパティの値を低く設定します。 グラフの各インスタンスで 0.5 ("maximumSamplesPerSecond":"0.5") に設定してインスタンスを再実行し、ハブで推論イベントを確認できます。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>複数のダイレクトメソッドの並列 –タイムアウト エラー 

Live Video Analytics on IoT Edge では、複数のトポロジと複数のグラフ インスタンスを設定できるようにする、ダイレクト メソッド ベースのプログラミング モデルが用意されています。 トポロジとグラフのセットアップの一環として、IoT Edge モジュールで複数のダイレクト メソッド呼び出しを起動します。 これらの複数のメソッド呼び出し (特に、グラフを開始および停止する呼び出し) を並列で起動すると、次のようなタイムアウト エラーが発生する可能性があります。 

Assembly Initialization method Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync threw exception. Microsoft.Azure.Devices.Common.Exceptions.IotHubException:Microsoft.Azure.Devices.Common.Exceptions.IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

ダイレクト メソッドは、並列で "*呼び出さない*" ことをお勧めします。 順番に呼び出すようにします。つまり、前のダイレクト メソッド呼び出しが終了してから次を実行します。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>サポート チケットを送信するためのログを収集する

セルフガイドによるトラブルシューティング手順で問題が解決しない場合は、Azure portal にアクセスして、[サポート チケットを開いてください](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> ログには、ご使用の IP アドレスなどの、個人を特定できる情報 (PII) が含まれている可能性があります。 ログのすべてのローカル コピーは、Microsoft による調査が完了し、サポート チケットが閉じられるとすぐに削除されます。  

チケットに追加する必要のある関連するログを収集するには、以下の指示に順番に従い、サポート要求の **[詳細]** ウィンドウにログ ファイルをアップロードします。  
1. [詳細ログを収集するように Live Video Analytics モジュールを構成します](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [デバッグ ログを有効にします](#live-video-analytics-debug-logs)
1. 問題を再現します
1. ポータルの **[IoT Hub]** ページから仮想マシンに接続します
    1. *debugLogs* フォルダー内のすべてのファイルを zip 圧縮します。

       > [!NOTE]
       > これらのログ ファイルは、自己診断を目的としたものではありません。 これらは、Azure エンジニアリング チームがお客様の問題を分析するために使用するものです。

       * 次のコマンドで、 **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** の部分を、前に **手順 2** で設定したエッジ デバイス上のデバッグ ログの場所に忘れずに置き換えてください。  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. この *debugLogs.zip* ファイルをサポート チケットに添付します。
1. [サポート バンドル コマンド](#use-the-support-bundle-command)を実行し、ログを収集してサポート チケットにアタッチします。

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>詳細ログを収集するように Live Video Analytics モジュールを構成する
次のように `logLevel` と `logCategories` を設定して、詳細ログを収集するように Live Video Analytics モジュールを構成します。
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

これは次のいずれかで行えます。
* **Azure portal** で、Live Video Analytics モジュールのモジュール ID ツインのプロパティを更新します。[ ![モジュール ID ツインのプロパティ。](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* または、**配置マニフェスト** ファイルで、Live Video Analytics モジュールのプロパティ ノードにこれらのエントリを追加できます

### <a name="use-the-support-bundle-command"></a>support-bundle コマンドを使用する

IoT Edge デバイスからログを収集する必要がある場合、最も簡単な方法は `support-bundle` コマンドを使用することです。 このコマンドでは、次の内容を収集します。

- モジュール ログ
- IoT Edge Security Manager とコンテナー エンジンのログ
- IoT Edge check JSON 出力
- 有用なデバッグ情報

1. ログの収集時間の長さを指定するには、 *--since* フラグを指定して `support-bundle` コマンドを実行します。 たとえば、2h と指定すると、過去 2 時間のログを取得します。 このフラグの値を変更することで、別の期間のログを取得できます。

    ```
    sudo iotedge support-bundle --since 2h
    ```

   このコマンドを実行すると、コマンドを実行したディレクトリに、*support_bundle.zip* という名前のファイルが作成されます。 
   
1. この *support_bundle.zip* ファイルをサポート チケットに添付します。

### <a name="live-video-analytics-debug-logs"></a>Live Video Analytics のデバッグ ログ

Live Video Analytics on IoT Edge モジュールがデバッグ ログを生成するように構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、IoT ハブに移動します。
1. 左側のペインで、 **[IoT Edge]** を選択します。
1. デバイスの一覧で、ターゲット デバイスの ID を選択します。
1. ペインの上部にある **[モジュールの設定]** を選択します。

   ![Azure portal の [モジュールの設定] ボタンのスクリーンショット。](media/troubleshoot-how-to/set-modules.png)

1. **[IoT Edge モジュール]** セクションで、 **[lvaEdge]** を探して選択します。
1. **[コンテナーの作成オプション]** を選択します。
1. **[バインド]** セクションで、次のコマンドを追加します。

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > このコマンドにより、エッジ デバイスとコンテナーの間でログ フォルダーがバインドされます。 別の場所にあるログを収集する場合は、 **$LOG_LOCATION_ON_EDGE_DEVICE** の部分を使用したい場所に置き換えて、次のコマンドを使用します。`/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. **[Update]\(更新\)** を選択します。
1. **[確認および作成]** を選択します。 検証の正常終了を示すメッセージが緑色のバナーの下に表示されます。
1. **［作成］** を選択します
1. **[モジュール ID ツイン]** を更新して、DebugLogsDirectory パラメーターを指すようにします。これは、ログが収集されるディレクトリを指します。

    a. **[モジュール]** テーブルの下にある **[lvaEdge]** を選択します。  
    b. ペインの上部にある **[モジュール ID ツイン]** を選択します。 編集可能なペインが開きます。  
    c. **[desired key]\(必要なキー\)** の下に、次のキーと値のペアを追加します。  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > このコマンドにより、エッジ デバイスとコンテナーの間でログ フォルダーがバインドされます。 デバイス上の別の場所にあるログを収集するには、次のようにします。
    > 1. **[バインド]** セクションでデバッグ ログの場所のバインドを作成し、 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** と **$DEBUG _LOG_LOCATION** を目的の場所 `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION` に置き換えます。
    > 2. 次のコマンドを使用して、 **$DEBUG _LOG_LOCATION** を、前の手順で使用した場所に置き換えます。  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. **[保存]** を選択します。


1. **[モジュール ID ツイン]** の値を *null* に設定すると、ログ収集を停止できます。 **[モジュール ID ツイン]** ページに戻り、次の内容をパラメーターとして更新します。

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>ログ記録に関するベスト プラクティス

[監視とログ記録](monitoring-logging.md)は、分類を理解し、LVA での問題のデバッグに役立つログの生成方法を理解する場合に役立ちます。 

gRPC サーバーの実装は言語間で異なるので、サーバーには、内部でログ記録を追加する標準的な方法はありません。  

たとえば、.NET Core を使用して gRPC サーバーを構築する場合、gRPC サービスは **Grpc** カテゴリ下にログを追加します。 gRPC から詳細なログを有効にするには、次の項目をログ記録の LogLevel サブセクションに追加することで、appsettings.json ファイルのデバッグ レベルに Grpc プレフィックスを構成します。 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

ConfigureLogging を使用して、Startup.cs ファイルでこれを構成することもできます。 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[.NET の gRPC でのログ記録と診断](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1)には、gRPC サーバーから複数の診断ログを収集するためのいくつかのガイダンスが用意されています。 

### <a name="a-failed-grpc-connection"></a>失敗した gRPC 接続 

グラフがアクティブであり、カメラからストリーミングされている場合、接続は Live Video Analytics によって維持されます。 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>CPU および GPU リソースがボトルネックになっているときに、これらのリソースの負荷を監視し分散する

Live Video Analytics は監視していないか、ハードウェア リソース監視を用意していません。 開発者は、ハードウェア製造元の監視ソリューションを使用する必要があります。 ただし、Kubernetes コンテナーを使用する場合は、[Kubernetes ダッシュボード](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)を使用してデバイスを監視できます。 

また、.NET Core ドキュメントの gRPC は、「[パフォーマンスのベストプラクティス](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1)」および「[負荷分散](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing)」に関する重要な情報も共有します。  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>どのフレームも受信しない場合および "不明" プロトコル エラーを受信している場合の推論サーバーのトラブルシューティング 

問題に関する詳細情報を取得するために実行できるいくつかの操作があります。  

* Live Video Analytics モジュールの目的のプロパティに "**ediaPipeline**" ログ カテゴリを含め、ログ レベルが `Information` に設定されていることを確認します。  
* ネットワーク接続をテストするには、エッジ デバイスから次のコマンドを実行できます。 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   コマンドによって短い文字列の乱雑なテキストが出力された場合、Telnet は正常に推論サーバーへの接続を開き、バイナリ gRPC チャネルを開くことができました。 これが表示されない場合、Telnet はネットワーク エラーを報告します。 
* 推論サーバーでは、gRPC ライブラリで追加のログ記録を有効にすることができます。 これにより、gRPC チャネル自体に関する追加情報が提供されます。 この操作は言語によって異なりますが、ここでは [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) での手順を説明します。 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>最初のバッファーの結果を返さずに gRPC のバッファーからより多くのイメージを選択する

gRPC データ転送コントラクトの一部として、Live Video Analytics が gRPC 推論サーバーに送信するすべてのメッセージは確認される必要があります。 イメージ フレームの受信を確認しないと、データ コントラクトが中断され、望ましくない状況になる場合があります。  

Live Video Analytics で gRPC サーバーを使用する場合、最適なパフォーマンスを得るために共有メモリを使用できます。 これには、プログラミング言語/環境によって公開された Linux 共有メモリ機能を使用する必要があります。 

1. Linux 共有メモリ ハンドルを開きます。
1. フレームを受信したときに、共有メモリ内のアドレス オフセットにアクセスします。
1. Live Video Analytics でメモリを再利用できるように、フレーム処理の完了を確認します。

   > [!NOTE]
   > 長時間、Live Video Analytics へのフレームの受信の確認が遅れた場合、共有メモリがいっぱいになり、データのドロップが行われる結果になることがあります。
1. 各フレームは、推論サーバー上の任意のデータ構造 (リスト、配列など) に格納します。
1. この場合、必要な数のイメージ フレームがあれば、処理ロジックを実行できます。
1. 準備ができたら、Live Video Analytics に推論の結果を戻します。

## <a name="next-steps"></a>次のステップ

[チュートリアル:クラウドへのイベントベースのビデオ記録とクラウドからの再生](event-based-video-recording-tutorial.md)