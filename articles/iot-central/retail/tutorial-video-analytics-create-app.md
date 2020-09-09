---
title: チュートリアル - Azure IoT Central でビデオ分析 (物体とモーションの検出) アプリケーションを作成する
description: このチュートリアルでは、IoT Central でビデオ分析アプリケーションを作成する方法について説明します。 それを作成してカスタマイズし、他の Azure サービスに接続します。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: b98406984f2c9f2adfca030369a6ea3d47a786f3
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762779"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>チュートリアル:Azure IoT Central でビデオ分析 (物体とモーションの検出) アプリケーションを作成する

ソリューション ビルダーとして、IoT Central の "*ビデオ分析 (物体とモーションの検出)* " アプリケーション テンプレート、Azure IoT Edge デバイス、Azure Media Services を使用してビデオ分析アプリケーションを作成する方法について学習します。 このソリューションでは、小売店を例に、セキュリティ カメラを監視するにあたっての一般的なビジネス ニーズを満たす方法について説明します。 このソリューションでは、ビデオ フィードの自動物体検出を使用して、関心のあるイベントをすばやく識別して見つけ出します。

サンプル アプリケーションには、シミュレートされたデバイスが 2 つと、IoT Edge ゲートウェイが 1 つ含まれています。 ゲートウェイの機能を試して理解するために、以降のチュートリアルでは次の 2 つのアプローチを紹介しています。

* IoT Edge ゲートウェイを Azure VM に作成して、シミュレートされたカメラを接続する。
* 実デバイス (Intel NUC など) に IoT Edge ゲートウェイを作成し、実際のカメラを接続する。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure IoT Central のビデオ分析アプリケーション テンプレートを使用して小売店アプリケーションを作成する
> * アプリケーションの設定をカスタマイズする
> * IoT Edge ゲートウェイ デバイス用のデバイス テンプレートを作成する
> * IoT Central アプリケーションにゲートウェイ デバイスを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアル シリーズを完了するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。
* 実際のカメラを使用している場合、IoT Edge デバイスとカメラとの間に接続が必要です。また、**リアル タイム ストリーミング プロトコル** チャンネルも必要となります。

## <a name="initial-setup"></a>初期セットアップ

これらのチュートリアルでは、いくつかの構成ファイルを更新して使用します。 これらのファイルの初期バージョンは、[LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub リポジトリで入手できます。 このリポジトリには、scratchpad というテキスト ファイルも用意されています。デプロイするサービスの構成値を記録する際に、ダウンロードしてご利用ください。

これらのファイルのコピーを保存するための *lva-configuration* というフォルダーをローカル コンピューターに作成します。 次の各リンクを右クリックし、 **[名前を付けて保存]** を選択して、*lva-configuration* フォルダーにファイルを保存します。

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> IoT Edge モジュールである **LvaEdgeGatewayModule** と **lvaYolov3** のソース コードも GitHub リポジトリにあります。 ソース コードを使った作業について詳しくは、[LVA ゲートウェイ モジュールの作成](tutorial-video-analytics-build-module.md)に関するページを参照してください。

## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services をデプロイして構成する

このソリューションでは、IoT Edge ゲートウェイ デバイスによって作成された物体検出ビデオ クリップの保存先として、Azure Media Services アカウントを使用します。

Media Services アカウントを作成する際:

- アカウント名、Azure サブスクリプション、場所、リソース グループ、ストレージ アカウントを指定する必要があります。 Media Services アカウントを作成するときに、既定の設定を使用して新しいストレージ アカウントを作成してください。

- 場所には**米国東部**リージョンを選択します。

- Media Services とストレージ アカウント用に *lva-rg* という新しいリソース グループを**米国東部**リージョンに作成します。 チュートリアルを終えたら、*lva-rg* リソース グループを削除することで、すべてのリソースを簡単に削除できます。

[Azure portal で Media Services アカウント](https://portal.azure.com/?r=1#create/Microsoft.MediaService)を作成します。

> [!TIP]
> これらのチュートリアルでは、すべての例で**米国東部**リージョンを使用しています。 必要に応じて最寄りのリージョンを使用してかまいません。

*scratchpad.txt* ファイルに **Media Services** アカウントの名前を書き留めます。

デプロイが完了したら、**Media Services** アカウントの **[プロパティ]** ページに移動します。 **リソース ID** を *scratchpad.txt* ファイルに書き留めます。この値は、後で IoT Edge モジュールを構成する際に使用します。

次に、Media Services リソースに使用する Azure Active Directory サービス プリンシパルを構成します。 **[API アクセス]** を選択し、 **[サービス プリンシパルの認証]** を選択します。 Media Services リソースと同じ名前で新しい Azure Active Directory アプリを作成し、"*IoT Edge アクセス*" という説明でシークレットを作成します。

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="AMS 用の AAD アプリを構成する":::

シークレットが生成されたら、下へスクロールして、 **[サービス プリンシパル アプリケーションを接続するための資格情報のコピー]** セクションに移動します。 次に、 **[JSON]** を選択します。 すべての資格情報は、ここからまとめてコピーできます。 その情報を *scratchpad.txt* ファイルに書き留めておきます。これは後で IoT Edge デバイスを構成する際に使用します。

> [!WARNING]
> これがシークレットを表示して保存する唯一の機会となります。 なくしてしまった場合は、別のキーを生成する必要があります。

## <a name="create-the-azure-iot-central-application"></a>Azure IoT Central アプリケーションを作成する

このセクションでは、テンプレートから新しい Azure IoT Central アプリケーションを作成します。 チュートリアル シリーズ全体でこのアプリケーションを使用して、完全なソリューションを構築します。

新しい Azure IoT Central アプリケーションを作成するには:

1. [Azure IoT Central のアプリケーション マネージャー](https://aka.ms/iotcentral) Web サイトに移動します。

1. Azure サブスクリプションへのアクセスに使用する資格情報でサインインします。

1. 新しい Azure IoT Central アプリケーションの作成を開始するには、 **[ビルド]** ページの **[新しいアプリケーション]** を選択します。

1. **[小売]** を選択します。 小売ページに、いくつかの小売アプリケーション テンプレートが表示されます。

新しいビデオ分析アプリケーションを作成するには:

1. **ビデオ分析 (物体とモーションの検出)** アプリケーション テンプレートを選択します。 このテンプレートには、チュートリアルで使用するデバイス用のデバイス テンプレートが含まれています。 このテンプレートには、カメラの監視や管理などのタスクを実行するためにオペレーターが使用できるサンプル ダッシュボードが含まれています。

1. 必要に応じて、わかりやすい**アプリケーション名**を選びます。 このアプリケーションは、Northwind Traders という架空の小売店に基づいています。 このチュートリアルでは、*Northwind Traders video analytics* という**アプリケーション名**を使用します。

    > [!NOTE]
    > わかりやすい**アプリケーション名**を使用する場合でも、アプリケーションの **URL** には一意の値を使用する必要があります。

1. Azure サブスクリプションをお持ちの場合は、 **[ディレクトリ]** と **[Azure サブスクリプション]** を選択し、 **[場所]** として **[米国]** を選択します。 サブスクリプションをお持ちでない場合は、**7 日間の無料試用版**を有効にし、必須の連絡先情報を入力できます。 このチュートリアルでは、カメラ 2 台と IoT Edge デバイス 1 台の計 3 台のデバイスを使用します。そのため、無料試用版を使用していない場合、使用量に応じて課金されます。

    ディレクトリ、サブスクリプション、場所の詳細については、[アプリケーションの作成のクイックスタート](../core/quick-deploy-iot-central.md)に関するページを参照してください。

1. **［作成］** を選択します

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Azure IoT Central の [Create Application]\(アプリケーションの作成\) ページ":::

### <a name="retrieve-the-configuration-data"></a>構成データを取得する

このチュートリアルの中で、後から IoT Edge ゲートウェイを構成します。その際、IoT Central アプリケーションの構成データがいくつか必要となります。 IoT Edge デバイスがアプリケーションへの登録と接続を行うためには、この情報が必要です。

**[管理]** セクションで **[お客様のアプリケーション]** を選択し、 **[アプリケーション URL]** と **[アプリケーション ID]** を *scratchpad.txt* ファイルに書き留めます。

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="管理":::

**[API トークン]** を選択し、**オペレーター** ロール用に **LVAEdgeToken** という新しいトークンを生成します。

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="トークンの生成":::

このトークンを後で使用できるよう、*scratchpad.txt* ファイルに書き留めます。 ダイアログを閉じた後に、再びトークンを確認することはできません。

**[管理]** セクションで **[デバイス接続]** を選択し、 **[SAS-IoT-Devices]** を選択します。

*scratchpad.txt* ファイルにデバイスの**プライマリ キー**を書き留めます。 この "*プライマリ グループ Shared Access Signature トークン*" は、後で IoT Edge デバイスを構成する際に使用します。

## <a name="edit-the-deployment-manifest"></a>配置マニフェストを編集する

IoT Edge モジュールは、配置マニフェストを使用してデプロイします。 IoT Central で、このマニフェストをデバイス テンプレートとしてインポートすれば、モジュールのデプロイを IoT Central に管理させることができます。

配置マニフェストを準備するには:

1. テキスト エディターを使用して、*lva-configuration* フォルダーに保存した *deployment.amd64.json* ファイルを開きます。

1. `LvaEdgeGatewayModule` 設定を見つけて、次のスニペットに示すようにイメージ名を変更します。

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. `LvaEdgeGatewayModule` セクションの `env` ノードに、Media Services アカウントの名前を追加します。 このアカウントの名前は、*scratchpad.txt* ファイルに書き留めました。

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. これらのプロパティが、テンプレートによって IoT Central に公開されることはありません。Media Services の構成値を自分で配置マニフェストに追加する必要があります。 `lvaEdge` モジュールを探し、プレースホルダーを、Media Services アカウントの作成時に *scratchpad.txt* ファイルに書き留めた値に置き換えます。

    `azureMediaServicesArmId` は、Media Services アカウントの作成時に *scratchpad.txt* ファイルに書き留めた**リソース ID** です。

    `aadTenantId`、`aadServicePrincipalAppId`、`aadServicePrincipalSecret` は、Media Services アカウントのサービス プリンシパルを作成する際に *scratchpad.txt* ファイルに書き留めました。

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. 変更を保存します。

Yolov3 モジュールは、ハードウェアによって最適化された Intel の [OpenVINO&trade; Model Server – Edge AI Extension Module](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) に必要に応じて置き換えることができます。 サンプルの配置マニフェスト [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json) をダウンロードできます。 このマニフェストでは、`lvaYolov3` モジュールの構成設定が次の構成に置き換えられます。

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Azure IoT Edge ゲートウェイ デバイスを作成する

ビデオ分析 (物体とモーションの検出) アプリケーションには、**LVA Edge Object Detector** デバイス テンプレートと **LVA Edge Motion Detection** デバイス テンプレートが含まれています。 このセクションでは、配置マニフェストを使用してゲートウェイ デバイス テンプレートを作成し、そのゲートウェイ デバイスを IoT Central アプリケーションに追加します。

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>LVA Edge Gateway のデバイス テンプレートを作成する

配置マニフェストをインポートして **LVA Edge Gateway** デバイス テンプレートを作成するには:

1. IoT Central アプリケーションで、 **[デバイス テンプレート]** に移動し、 **[+ 新規]** を選択します。

1. **[Select template type]\(テンプレートの種類の選択\)** ページで **[Azure IoT Edge]** タイルを選択します。 次に、 **[次のステップ: カスタマイズ]** を選択します。

1. **[Upload an Azure IoT Edge deployment manifest]\(Azure IoT Edge 配置マニフェストをアップロードする\)** ページで、テンプレート名として「*LVA Edge Gateway*」を入力し、 **[Gateway device with downstream devices]\(ダウンストリーム デバイスがあるゲートウェイ デバイス\)** チェック ボックスをオンにします。

    まだ配置マニフェストを参照しないでください。 そのようにした場合、デプロイ ウィザードによって各モジュールのインターフェイスが要求されますが、インターフェイスを公開する必要があるのは **LvaEdgeGatewayModule** だけです。 マニフェストは、後続の手順でアップロードします。

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="配置マニフェストをアップロードしない":::

    **確認\)** をクリックします。

1. **[Review]\(レビュー\)** ページで、 **[Create]\(作成\)** を選択します。

### <a name="import-the-device-capability-model"></a>デバイス機能モデルをインポートする

デバイス テンプレートには、デバイス機能モデルが含まれている必要があります。 **[LVA Edge Gateway]** ページの **[機能モデルのインポート]** タイルを選択します。 先ほど作成した *lva-configuration* フォルダーに移動し、*LvaEdgeGatewayDcm.json* ファイルを選択します。

**LVA Edge Gateway** デバイス テンプレートに、 **[LVA Edge Gateway Module]\(LVA Edge Gateway モジュール\)** が、 **[Device information]\(デバイス情報\)** 、 **[LVA Edge Gateway Settings]\(LVA Edge Gateway 設定\)** 、 **[LVA Edge Gateway Interface]\(LVA Edge Gateway インターフェイス\)** の 3 つのインターフェイスと共に含まれています。

### <a name="replace-the-manifest"></a>マニフェストを置き換える

**[LVA Edge Gateway]** ページで、 **[+ Replace manifest]\(+ マニフェストを置き換える\)** を選択します。

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="マニフェストを置き換える":::

*lva-configuration* フォルダーに移動し、先ほど編集した *deployment.amd64.json* マニフェスト ファイルを選択します。 **[アップロード]** を選択します。 検証が完了したら、 **[Replace]\(置き換え\)** を選択します。

### <a name="add-relationships"></a>リレーションシップの追加

**[LVA Edge Gateway]** デバイス テンプレートの **[Modules/LVA Edge Gateway Module]\(モジュール/LVA Edge Gateway モジュール\)** で **[Relationships]\(リレーションシップ\)** を選択します。 **[+ リレーションシップの追加]** を選択し、次の 2 つのリレーションシップを追加します。

|表示名               |名前          |移行先 |
|-------------------------- |------------- |------ |
|LVA Edge Motion Detector   |既定値を使用します。   |LVA Edge Motion Detector Device |
|LVA Edge Object Detector   |既定値を使用します。   |LVA Edge Object Detector Device |

次に、 **[保存]** を選択します。

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="リレーションシップの追加":::

### <a name="add-views"></a>ビューを追加する

**LVA Edge Gateway** デバイス テンプレートには、ビューの定義が含まれていません。

デバイス テンプレートにビューを追加するには:

1. **LVA Edge Gateway** デバイス テンプレートで、 **[Views]\(ビュー\)** に移動し、 **[デバイスの視覚化]** タイルを選択します。

1. ビューの名前として「*LVA Edge Gateway device*」と入力します。

1. 次のタイルをビューに追加します。

    * **[デバイス情報]** のプロパティ (**デバイス モデル**、**製造元**、**オペレーティング システム**、**プロセッサ アーキテクチャ**、**ソフトウェア バージョン**、**メモリ合計**、**ストレージの合計**) を含むタイル。
    * **[Free Memory]\(空きメモリ\)** と **[System Heartbeat]\(システム ハートビート\)** のテレメトリ値を含む折れ線グラフのタイル。
    * **[Create Camera]\(カメラの作成\)** 、 **[Delete Camera]\(カメラの削除\)** 、 **[Module Restart]\(モジュールの再起動\)** 、 **[Module Started]\(モジュール開始\)** 、 **[Module Stopped]\(モジュール停止\)** の各イベントを含むイベント履歴タイル。
    * **[IoT Central Client State]\(IoT Central クライアントの状態\)** のテレメトリを表示する最後の既知の値タイル (2x1)。
    * **[Module State]\(モジュールの状態\)** のテレメトリを表示する最後の既知の値タイル (2x1)。
    * **[System Heartbeat]\(システム ハートビート\)** のテレメトリを表示する最後の既知の値タイル (1x1)。
    * **[Connected Cameras]\(接続されているカメラ\)** のテレメトリを表示する最後の既知の値タイル (1x1)。

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="ダッシュボード":::

1. **[保存]** を選択します。

### <a name="publish-the-device-template"></a>デバイス テンプレートを発行する

デバイスをアプリケーションに追加する前に、デバイス テンプレートを発行する必要があります。

1. **LVA Edge Gateway** デバイス テンプレートで **[発行]** を選択します。

1. **[このデバイス テンプレートのアプリケーションへの発行]** ページで、 **[発行]** を選択します。

アプリケーションの **[デバイス]** ページに、使用するデバイスの種類として **LVA Edge Gateway** が表示されるようになります。

## <a name="add-a-gateway-device"></a>ゲートウェイ デバイスを追加する

**LVA Edge Gateway** デバイスをアプリケーションに追加するには:

1. **[デバイス]** ページに移動し、 **[LVA Edge Gateway]** デバイス テンプレートを選択します。

1. **[+新規]** を選択します。

1. **[新しいデバイスを作成します]** ダイアログで、デバイス名を *LVA Gateway 001* に変更し、デバイス ID を *lva-gateway-001* に変更します。

    > [!NOTE]
    > デバイス ID は、アプリケーション内で一意である必要があります。

1. **［作成］** を選択します

デバイスの状態が **[登録済み]** になります。

### <a name="get-the-device-credentials"></a>デバイスの資格情報を取得する

お使いの IoT Central アプリケーションへの接続をデバイスに許可する資格情報が必要になります。 デバイスの資格情報を取得するには、次の手順を実行します。

1. **[デバイス]** ページで、先ほど作成した **lva-gateway-001** デバイスを選択します。

1. **[接続]** を選択します。

1. **[デバイス接続]** ページで、**ID スコープ**、**デバイス ID**、デバイスの**プライマリ キー**を *scratchpad.txt* ファイルに書き留めます。 これらの値は後で使用します。

1. 接続方法が **Shared Access Signature** に設定されていることを確認します。

1. **[閉じる]** を選択します。

## <a name="next-steps"></a>次のステップ

**ビデオ分析 (物体とモーションの検出)** アプリケーション テンプレートを使用して IoT Central アプリケーションを作成し、ゲートウェイ デバイス用のデバイス テンプレートを作成して、ゲートウェイ デバイスをアプリケーションに追加しました。

クラウド VM を実行する IoT Edge モジュールとシミュレートされたビデオ ストリームを使用して、ビデオ分析 (物体とモーションの検出) アプリケーションを試したい場合:

> [!div class="nextstepaction"]
> [ビデオ分析用の IoT Edge インスタンスを作成する (Linux VM)](./tutorial-video-analytics-iot-edge-vm.md)

実デバイスを実行する IoT Edge モジュールと実際の **ONVIF** カメラを使用して、ビデオ分析 (物体とモーションの検出) アプリケーションを試したい場合:

> [!div class="nextstepaction"]
> [ビデオ分析用の IoT Edge インスタンスを作成する (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
