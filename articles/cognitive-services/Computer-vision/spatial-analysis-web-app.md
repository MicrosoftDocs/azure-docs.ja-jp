---
title: 空間分析 Web アプリをデプロイする
titleSuffix: Azure Cognitive Services
description: Web アプリで空間分析を使用する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: de011fb0f827ea90efe33e237bbf1c5100dc76a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183474"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>方法:人数カウント Web アプリをデプロイする

この記事では、人の動きを把握し、物理的な空間内の人数を監視する Web アプリに空間分析を統合する方法について説明します。 

このチュートリアルで学習する内容は次のとおりです。

* 空間分析コンテナーをデプロイする
* 操作とカメラを構成する
* Web アプリで IoT Hub 接続を構成する
* Web アプリをデプロイしてテストする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/cognitive-services/)
* Azure IoT Edge デプロイの構成と [Azure IoT Hub](../../iot-hub/index.yml) の基礎知識
* 構成済みの[ホスト コンピューター](spatial-analysis-container.md)

## <a name="deploy-the-spatial-analysis-container"></a>空間分析コンテナーをデプロイする

[要求申請](https://aka.ms/csgate)に入力して、コンテナーを実行するためのアクセス権を取得します。 

[ホスト コンピューターのセットアップ](./spatial-analysis-container.md)に関する記事に従って、ホスト コンピューターを構成し、IoT Edge デバイスを Azure IoT Hub に接続します。 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>サブスクリプションに Azure IoT Hub サービスをデプロイする

まず、Standard 価格レベル (S1) または Free レベル (S0) で Azure IoT Hub サービスのインスタンスを作成します。 次の手順に従って、Azure CLI を使用してこのインスタンスを作成します。

必要なパラメーターを入力します。
* サブスクリプション:Azure サブスクリプションの名前または ID。
* リソース グループ: リソース グループの名前を作成します。
* Iot Hub 名: IoT Hub の名前を作成します。
* IoTHub 名: 作成した IoT Hub の名前。 
* Edge Device Name\(エッジ デバイス名\): エッジ デバイスの名前を作成します。

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>ホスト コンピューター上の Azure IoT Edge にコンテナーをデプロイする

Azure CLI を使用して、空間分析コンテナーを IoT モジュールとしてホスト コンピューターにデプロイします。 デプロイ プロセスには、デプロイに必要なコンテナー、変数、構成の概要を示す配置マニフェスト ファイルが必要です。 GitHub には、サンプルの [Azure Stack Edge 固有の配置マニフェスト](https://go.microsoft.com/fwlink/?linkid=2142179)、[非 Azure Stack Edge 固有の配置マニフェスト](https://go.microsoft.com/fwlink/?linkid=2152189)、および [GPU 搭載 Azure VM 固有の配置マニフェスト](https://go.microsoft.com/fwlink/?linkid=2152189)が掲載されています。これらには、*spatial-analysis* コンテナーの基本的なデプロイ構成が含まれています。 

または、Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT Hub で操作を実行できます。 詳細については、「[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](../../iot-edge/how-to-deploy-modules-vscode.md)」を参照してください。

> [!NOTE] 
> *spatial-analysis-telegraf* および *spatial-analysis-diagnostics* コンテナーはオプションです。 これらは、*DeploymentManifest.json* ファイルから削除することもできます。 詳細については、[テレメトリとトラブルシューティング](./spatial-analysis-logging.md)に関する記事をご覧ください。 GitHub には、[Azure Stack Edge デバイス](https://go.microsoft.com/fwlink/?linkid=2142179)、[デスクトップ マシン](https://go.microsoft.com/fwlink/?linkid=2152189)、または [GPU 搭載 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) の 3 つのサンプル *DeploymentManifest.json* ファイルが掲載されています。

### <a name="set-environment-variables"></a>環境変数の設定

IoT Edge モジュールのほとんどの **環境変数** は、上記のリンク先のサンプル *DeploymentManifest.json* ファイルに既に設定されています。 このファイルで、次のように、`BILLING_ENDPOINT` および `API_KEY` 環境変数を検索します。 以前に作成したエンドポイント URI と API キーに値を置き換えます。 EULA 値が "accept" に設定されていることを確認します。 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>操作のパラメーターを構成する

*spatial-analysis* コンテナーの初期構成が完了しました。次に、操作のパラメーターを構成し、それらをデプロイに追加します。 

まず、上記のリンク先のサンプル配置マニフェストを更新し、次に示すように `cognitiveservices.vision.spatialanalysis-personcount` の operationId を構成します。


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

配置マニフェストが更新されたら、カメラの製造元の指示に従ってカメラをインストールし、カメラの URL、ユーザー名、パスワードを構成します。 

次に、`VIDEO_URL` をカメラの RTSP URL に設定し、カメラに接続するための資格情報を設定します。

エッジ デバイスに複数の GPU がある場合は、この操作を実行する GPU を選択します。 一度に 1 つの GPU で実行される操作が 8 つ以下の場合は、必ず操作の負荷を分散してください。  

次に、人数をカウントするゾーンを構成します。 ゾーンの多角形を構成するには、まず、製造元の指示に従ってカメラからフレームを取得します。 多角形の各頂点を決定するには、フレーム上のポイントを選択し、フレームの左上隅を基準としたポイントの X、Y ピクセル座標を取得して、対応するフレーム サイズで除算します。 結果を頂点の X、Y 座標として設定します。 ゾーンの多角形構成は、`SPACEANALYTICS_CONFIG` フィールドで設定できます。

これは、サイズが 1920/1080 のフレームの頂点座標がどのように計算されるのかを示すサンプル ビデオ フレームです。
![サンプル ビデオ フレーム](./media/spatial-analysis/sample-video-frame.jpg)

検出された人数がカウントされ、イベントが生成されるときの信頼度のしきい値を選択することもできます。 すべてのイベントを出力する場合は、しきい値を 0 に設定します。

### <a name="execute-the-deployment"></a>デプロイを実行する

配置マニフェストが完成したら、Azure CLI で次のコマンドを使用して、コンテナーを IoT Edge モジュールとしてホスト コンピューターにデプロイします。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

必要なパラメーターを入力します。

* IoT Hub 名: Azure IoT Hub の名前。
* DeploymentManifest.json:デプロイ ファイルの名前。
* IoT Edge デバイス名: ホスト コンピューターの IoT Edge デバイス名。
* サブスクリプション:サブスクリプションの ID または名前。

このコマンドによってデプロイが開始されます。Azure portal の Azure IoT Hub インスタンスでデプロイの状態を確認できます。 デバイスでコンテナー イメージのダウンロードが終了し、実行が開始されるまで、状態に "*417 - デバイスのデプロイ構造が設定されていません*" と表示されることがあります。

### <a name="validate-that-the-deployment-was-successful"></a>デプロイが成功したことを検証する

Azure portal の IoT Hub インスタンスで、spatial-analysis モジュールの [IoT Edge モジュールの設定] にある *[ランタイムの状態]* を見つけます。 **[ランタイムの状態]** の **[必要な値]** と *[報告された値]* に `Running` と表示されている必要があります。 これは、Azure portal で次のように表示されます。

![デプロイの検証の例](./media/spatial-analysis/deployment-verification.png)

この時点で、spatial-analysis コンテナーは操作を実行しています。 `cognitiveservices.vision.spatialanalysis-personcount` 操作の AI 分析情報が生成され、これらの分析情報がテレメトリとして Azure IoT Hub インスタンスにルーティングされます。 追加のカメラを構成するには、配置マニフェスト ファイルを更新し、デプロイをもう一度実行します。

## <a name="person-counting-web-application"></a>人数カウント Web アプリ

この人数カウント Web アプリを使用すると、サンプル Web アプリをすばやく構成し、Azure 環境でホストできます。

### <a name="get-the-person-counting-app-container"></a>人数カウント アプリ コンテナーを取得する

このアプリのコンテナー フォームは、Azure Container Registry で利用できます。 次の docker pull コマンドを使用してダウンロードします。 アクセス トークンについては、Microsoft (projectarchon@microsoft.com) にお問い合わせください。

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

コンテナーを Azure Container Registry (ACR) にプッシュします。

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

コンテナーをインストールするには、新しい Azure Web App for Containers を作成し、必要なパラメーターを入力します。 次に、 **[Docker]** タブに移動し、 **[単一コンテナー]** 、 **[Azure Container Registry]** の順に選択します。 上記でイメージをプッシュした Azure Container Registry のインスタンスを使用します。

![イメージの詳細を入力する](./media/spatial-analysis/solution-app-create-screen.png)

上記のパラメーターを入力したら、 **[レビュー + 作成]** をクリックしてアプリを作成します。

### <a name="configure-the-app"></a>Configure the app 

セットアップが完了するまで待ち、Azure portal でリソースに移動します。 **構成** セクションに移動し、次の 2 つの **アプリケーション設定** を追加します。

* `EventHubConsumerGroup` - Azure IoT Hub のコンシューマー グループの文字列名。IoT Hub に新しいコンシューマー グループを作成することも、既定のグループを使用することもできます。 
* `IotHubConnectionString` - Azure IoT Hub への接続文字列。これは、Azure IoT Hub リソースの ![[パラメーターの構成]](./media/spatial-analysis/solution-app-config-page.png) のキー セクションから取得できます。

この 2 つの設定を追加したら、 **[保存]** をクリックします。 次に、左側のナビゲーション メニューで **[認証/承認]** をクリックし、目的の認証レベルで更新します。 Azure Active Directory (Azure AD) 簡易をお勧めします。 

### <a name="test-the-app"></a>アプリのテスト

Azure Web アプリに移動し、デプロイが成功したことと、Web アプリが実行されていることを確認します。 構成済みの URL (`<yourapp>.azurewebsites.net`) に移動して、実行中のアプリを表示します。

![展開をテスト](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>PersonCount ソース コードを取得する
このアプリケーションのソース コードを表示または変更する場合は、[GitHub](https://github.com/Azure-Samples/cognitive-services-spatial-analysis) を参照してください。

## <a name="next-steps"></a>次のステップ

* [空間分析操作の構成](./spatial-analysis-operations.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)
