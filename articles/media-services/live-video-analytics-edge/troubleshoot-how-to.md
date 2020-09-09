---
title: Live Video Analytics on IoT Edge のトラブルシューティング - Azure
description: この記事では、Live Video Analytics on IoT Edge のトラブルシューティング手順について説明します。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: bbd3cb88b017209adff58a646e274caf31ab425f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486444"
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

エッジ インフラストラクチャが正常な場合は、配置マニフェスト ファイルの問題を調べることができます。 Live Video Analytics on IoT Edge モジュールを他の IoT モジュールと共に IoT Edge デバイスにデプロイするには、IoT Edge ハブ、IoT Edge エージェント、その他のモジュールとそれらのプロパティを含む配置マニフェストを使用します。 JSON コードの形式が正しくない場合、次のエラーが表示されることがあります。 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Failed to parse JSON from file: '<deployment manifest.json>' for argument 'content' with exception:"Extra data: line 101 column 1 (char 5325)"

このエラーが発生した場合は、かっこの不足やファイルの構造に関するその他の問題がないか、JSON を確認することをお勧めします。 ファイル構造を検証するには、[JSON Viewer プラグイン搭載の Notepad++](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) などのクライアントや、[JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/) などのオンライン ツールを使用できます。

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>デプロイ中:メディア グラフのダイレクト メソッドを使用した診断 

Live Video Analytics on IoT Edge モジュールが IoT Edge デバイスに正しくデプロイされると、[ダイレクト メソッド](direct-methods.md)を呼び出すことによって、メディア グラフを作成して実行できるようになります。 Azure portal を使用して、ダイレクト メソッド経由でメディア グラフの診断を実行できます。

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

1. 状態 501 のコードが表示された場合は、ダイレクト メソッド名が正確であることを確認してください。 メソッド名と要求ペイロードが正確である場合は、成功コード = 200 と共に結果が返されます。 要求ペイロードが不正確な場合は、状態 = 400 と、ダイレクト メソッド呼び出しに関する問題の診断に役立つエラー コードとメッセージを示す応答ペイロードが表示されます。
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

### <a name="edge-setup-script-issues"></a>エッジのセットアップ スクリプトの問題

Microsoft のドキュメントの一部として、エッジおよびクラウド リソースをデプロイして Live Video Analytics Edge を開始するための[セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)が用意されています。 このセクションでは、発生する可能性があるスクリプト エラーのいくつかと、それらをデバッグするための解決策について説明します。

問題点:スクリプトを実行すると、いくつかのリソースが部分的に作成されるものの、次のメッセージが表示されて失敗します。

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
この問題を解決するには、次の手順に従います。

1. 次のコマンドを実行します。

    ```
    az --version
    ```
1. 次の拡張機能がインストールされていることを確認します。 この記事の公開時点では、拡張機能とそのバージョンは次のとおりです。

    | 拡張機能 | Version |
    |---|---|
    |azure-cli   |      2.5.1*|
    |command-modules-nspkg         |   2.0.3|
    |core  |    2.5.1*|
    |nspkg    | 3.0.4|
    |telemetry| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. インストールされている拡張機能の中に、そのバージョンがこちらに記載されているリリース番号より前のものがある場合は、次のコマンドを使用してその拡張機能を更新します。

    ```
    az extension update --name <Extension name>
    ```

    たとえば、`az extension update --name azure-iot` のように実行します。

### <a name="sample-app-issues"></a>サンプル アプリの問題

Microsoft では、リリースの一部として、開発者コミュニティが自分で作業を開始するのに役立つ .NET サンプル コードを用意しています。 このセクションでは、そのサンプル コードを実行するときに発生する可能性があるエラーのいくつかと、それらをデバッグするための解決策について説明します。

問題点:Program.cs は、ダイレクト メソッドの呼び出しで次のエラーが発生して失敗します。

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Visual Studio Code 環境に [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) がインストールされていること、および IoT ハブ セットアップへの接続が設定済みであることを確認します。 これを行うには、Ctrl + Shift + P キーを同時に押し、 **[Select IoT Hub method]\(IoT Hub メソッドの選択\)** を選択します。

1. Visual Studio Code を使用してその IoT Edge モジュールでダイレクト メソッドを呼び出せるかどうかを確認します。 たとえば、次のペイロードを指定して、GraphTopologyList を呼び出します。{&nbsp;"@apiVersion":"1.0"}。 次の応答が返されるはずです。 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code の応答のスクリーンショット。](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 上記のソリューションで解決しない場合は、次を試してください。

    a. IoT Edge デバイスでコマンド プロンプトに移動し、次のコマンドを実行します。
    
      ```
      sudo systemctl restart iotedge
      ```

      このコマンドを実行すると、IoT Edge デバイスとすべてのモジュールが再起動します。 数分待ってから、ダイレクト メソッドをもう一度使用してみる前に、次のコマンドを実行してモジュールが実行されていることを確認します。

      ```
      sudo iotedge list
      ```

    b. 上記の方法でも解決しない場合は、仮想マシンやコンピューターを再起動してみてください。

    c. すべてのアプローチを試しても問題が解決しない場合は、次のコマンドを実行して、すべての[関連ログ](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)を含む ZIP ファイルを取得し、そのファイルを[サポート チケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)に添付してください。

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. エラー応答 *400* コードが表示された場合は、メソッド呼び出しペイロードが「[ダイレクト メソッド](direct-methods.md)」ガイドに従った適切な形式になっていることをご確認ください。
1. 状態 *200* コードが表示された場合は、ハブが正常に機能しており、モジュールのデプロイが適切で応答していることを示しています。 

1. アプリの構成が正確であるかどうかを確認します。 アプリの構成は、*appsettings.json* ファイルの次のフィールドで構成されています。 deviceId と moduleId が正確であることをもう一度確認してください。 Visual Studio Code の Azure IoT Hub 拡張機能セクションを見ると、簡単に確認できます。 *appsettings.json* ファイルの値と、この IoT Hub セクションの値は一致しているはずです。
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. *appsettings.json* ファイルで、IoT Hub デバイス接続文字列*ではなく*、IoT Hub 接続文字列が指定されていることを確認します。これは、両者の[接続文字列の形式が異なる](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)ためです。

### <a name="live-video-analytics-working-with-external-modules"></a>Live Video Analytics による外部モジュールの操作

HTTP 拡張プロセッサ経由の Live Video Analytics では、メディア グラフを拡張し、他の IoT Edge モジュールに対して REST を使用して HTTP 経由でデータを送受信することができます。 [具体的な例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)として、メディア グラフでは、Yolo v3 などの外部推論モジュールへビデオ フレームをイメージとして送信し、JSON ベースの分析結果を受け取ることができます。 このようなトポロジでは、イベントの送信先は主に IoT ハブです。 ハブで推論イベントが表示されない場合は、次の点を確認します。

* メディア グラフの公開先となるハブと、調査中のハブが同じであるかどうかを確認します。 複数のデプロイを作成すると、ハブが複数作成され、イベントに対して間違ったハブを誤って確認していることがあります。
* Visual Studio Code で、外部モジュールがデプロイされて実行されているかどうかを確認します。 この例の図では、rtspsim と cv は、lvaEdge モジュールの外部で実行されている IoT Edge モジュールです。

    ![Azure IoT Hub のモジュールの実行状態を表示するスクリーンショット。](./media/troubleshoot-how-to/iot-hub.png)

* 正しい URL エンドポイントにイベントを送信しているかどうかを確認します。 外部 AI コンテナーは、URL と、POST 要求からデータを受信して返すポートを公開します。 この URL は、HTTP 拡張プロセッサの `endpoint: url` プロパティとして指定されます。 [トポロジ URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) に示されているように、エンドポイントは推論の URL パラメーターに設定されています。 このパラメーターの既定値、または渡された値が正確であることを確認します。 クライアント URL (cURL) を使用することにより、それが機能しているかどうかをテストできます。  

    例として、ローカル コンピューター上で実行されている Yolo v3 コンテナー (IP アドレス: 172.17.0.3) があるとします。 IP アドレスを検出するには、Docker inspect を使用します。

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    返される結果:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* HTTP 拡張プロセッサを利用するグラフのインスタンスを 1 つ以上実行している場合は、ビデオ フィードの 1 秒あたりのフレーム数 (fps) を管理するため、各 HTTP 拡張プロセッサの前にフレーム レート フィルターが必要です。 

   エッジ マシンの CPU やメモリの使用率が高い特定の状況では、特定の推論イベントが失われることがあります。 この問題に対処するには、フレーム レート フィルターの maximumFps プロパティの値を低く設定します。 グラフの各インスタンスで 0.5 ("maximumFps":0.5) に設定してインスタンスを再実行し、ハブで推論イベントを確認できます。

   また、より強力な CPU とより多くのメモリを搭載するパワフルなエッジ マシンを取得する方法もあります。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>複数のダイレクトメソッドの並列 –タイムアウト エラー 

Live Video Analytics on IoT Edge では、複数のトポロジと複数のグラフ インスタンスを設定できるようにする、ダイレクト メソッド ベースのプログラミング モデルが用意されています。 トポロジとグラフのセットアップの一環として、IoT Edge モジュールで複数のダイレクト メソッド呼び出しを起動します。 これらの複数のメソッド呼び出し (特に、グラフを開始および停止する呼び出し) を並列で起動すると、次のようなタイムアウト エラーが発生する可能性があります。 

Assembly Initialization method Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync threw exception. Microsoft.Azure.Devices.Common.Exceptions.IotHubException:Microsoft.Azure.Devices.Common.Exceptions.IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

ダイレクト メソッドは、並列で "*呼び出さない*" ことをお勧めします。 順番に呼び出すようにします。つまり、前のダイレクト メソッド呼び出しが終了してから次を実行します。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>サポート チケットを送信するためのログを収集する

セルフガイドによるトラブルシューティング手順で問題が解決しない場合は、Azure portal にアクセスして、[サポート チケットを開いてください](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> ログには、ご使用の IP アドレスなどの、個人を特定できる情報 (PII) が含まれている可能性があります。 ログのすべてのローカル コピーは、Microsoft による調査が完了し、サポート チケットが閉じられるとすぐに削除されます。  

チケットに追加する必要のある関連ログを収集するには、次のセクションの手順に従います。 ログ ファイルは、サポート リクエストの **[詳細]** ペインでアップロードできます。

### <a name="use-the-support-bundle-command"></a>support-bundle コマンドを使用する

IoT Edge デバイスからログを収集する必要がある場合、最も簡単な方法は `support-bundle` コマンドを使用することです。 このコマンドでは、次の内容を収集します。

- モジュール ログ
- IoT Edge Security Manager とコンテナー エンジンのログ
- Iotedge check JSON 出力
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
    > このコマンドにより、エッジ デバイスとコンテナーの間でログ フォルダーがバインドされます。 別の場所にあるログを収集する場合は、 **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** の部分を使用したい場所に置き換えて、次のコマンドを使用します。  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE"`  

    d. **[保存]** を選択します。

1. 問題を再現します。
1. ポータルの **[IoT Hub]** ページから仮想マシンに接続します。
1. *debugLogs* フォルダー内のすべてのファイルを zip 圧縮します。

   > [!NOTE]
   > これらのログ ファイルは、自己診断を目的としたものではありません。 これらは、Azure エンジニアリング チームがお客様の問題を分析するために使用するものです。

   a. 次のコマンドで、 **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** の部分を、前に設定したエッジ デバイス上のデバッグ ログの場所に忘れずに置き換えてください。  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. この *debugLogs.zip* ファイルをサポート チケットに添付します。

1. **[モジュール ID ツイン]** の値を *null* に設定すると、ログ収集を停止できます。 **[モジュール ID ツイン]** ページに戻り、次の内容をパラメーターとして更新します。

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>次のステップ

[チュートリアル:クラウドへのイベントベースのビデオ記録とクラウドからの再生](event-based-video-recording-tutorial.md)
