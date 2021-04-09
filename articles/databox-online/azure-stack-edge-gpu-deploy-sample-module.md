---
title: Azure Stack Edge Pro GPU デバイスに GPU モジュールをデプロイする | Microsoft Docs
description: ローカル UI からコンピューティングを有効化し、Azure Stack Edge Pro デバイスでコンピューティングの準備をする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4142542c9f17ae464a996df310c50d73d8711d8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438201"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>GPU 対応 IoT モジュール Azure Stack Edge Pro GPU デバイスにデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスに GPU 対応 IoT Edge モジュールをデプロイする方法について説明します。 

この記事では、次のことについて説明します。
  - GPU モジュールを実行するために Azure Stack Edge Pro を準備します。
  - Git リポジトリからサンプ ルコードをダウンロードしてインストールします。
  - ソリューションをビルドし、配置マニフェストを生成します。
  - ソリューションを Azure Stack Edge Pro デバイスにデプロイします。
  - モジュールの出力を監視します。


## <a name="about-sample-module"></a>サンプル モジュールについて

この記事の GPU サンプル モジュールには、CPU と GPU を比較する PyTorch および TensorFlow のベンチマーク サンプル コードが含まれています。

## <a name="prerequisites"></a>前提条件

開始する前に、次のものがあることを確認します。

- GPU 対応の 1 ノード Azure Stack Edge Pro デバイスにアクセスできること。 このデバイスと Azure のリソースがアクティブになっていること。 [デバイスをアクティブにする](azure-stack-edge-gpu-deploy-activate.md)に関する記事を参照してください。
- このデバイスでコンピューティングを構成済みであること。 「[チュートリアル: Azure Stack Edge Pro デバイスでコンピューティングを構成する](azure-stack-edge-gpu-deploy-configure-compute.md)」の手順に従います。
- Azure Container Registry (ACR)。 **[アクセス キー]** ブレードに移動し、ACR ログイン サーバー、ユーザー名、パスワードをメモしておきます。 詳細については、「[クイック スタート: Azure portal を使用したプライベート コンテナー レジストリの作成](../container-registry/container-registry-get-started-portal.md#create-a-container-registry)」を参照してください。
- Windows クライアント上の次の開発リソース:
    - [Azure CLI 2.0 以降](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows)。 ソフトウェアをダウンロードしてインストールするには、アカウントの作成が必要になる場合があります。
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)    
    - [Visual Studio Code 用の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - Python パッケージをインストールするための Pip (通常は、Python のインストールに含まれています)

## <a name="get-the-sample-code"></a>サンプル コードの入手

1. [[Azure サンプルの Azure インテリジェント エッジ パターン]](https://github.com/azure-samples/azure-intelligent-edge-patterns) に移動します。 コードの ZIP ファイルを複製またはダウンロードします。 

    ![ZIP 形式でダウンロード](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    ZIP ファイルからファイルを解凍します。 サンプルを複製することもできます。

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>モジュールをビルドおよびデプロイする

1. Visual Studio Code で **GpuReferenceModules** フォルダーを開きます。

    ![VS Code で GPUReferenceModules を開く](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. *deployment.template.json* を開き、コンテナー レジストリの参照先パラメーターを特定します。 次のファイルでは、CONTAINER_REGISTRY_USERNAME、CONTAINER_REGISTRY_PASSWORD、CONTAINER_REGISTRY_NAME が使用されています。

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. 新しいファイルを作成します。 次のように、コンテナー レジストリ パラメーターの値を入力します (前の手順で指定した値を使用してください)。 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    サンプルの *.env* ファイルを次に示します。
    
    ![.env ファイルを作成して保存する](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. **SampleSolution** フォルダーにファイルを *.env* として保存します。

5. Docker にサインインするには、Visual Studio Code 統合ターミナルで次のコマンドを入力します。 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Azure portal でコンテナー レジストリの **[アクセス キー]** セクションに移動します。 レジストリ名、パスワード、ログイン サーバーをコピーして使用します。

    ![コンテナー レジストリのアクセス キー](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    資格情報を指定すると、サインインに成功します。

    ![成功したサインイン](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Azure コンテナー レジストリにイメージをプッシュします。 VS Code Explorer で、**deployment.template.json** ファイルを選択して右クリックし、 **[IoT Edge ソリューションのビルドとプッシュ]** を選択します。 

    ![IoT Edge ソリューションをビルドしてプッシュする](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Python と Python の拡張機能がインストールされていない場合は、ソリューションをビルドしてプッシュするとインストールされます。 ただし、ビルド時間は長くなります。 

    この手順が完了すると、コンテナー レジストリにモジュールが表示されます。

    ![コンテナー レジストのモジュール](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. 配置マニフェストを作成するには、**deployment.template.json** ファイルを右クリックし、 **[IoT Edge 配置マニフェストの生成]** を選択します。 

    ![IoT Edge 配置マニフェストの生成](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    配置マニフェストが生成されたパスが通知されます。 マニフェストは、**config** フォルダーに生成された `deployment.amd64.json` ファイルです。 

8. **config** フォルダーで **deployment.amd64.json** ファイルを選択し、 **[単一デバイスのデプロイの作成]** をクリックします。 **deployment.template.json** ファイルは使用しないでください。 

    ![単一デバイスのデプロイを作成する](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    **[出力]** ウィンドウに、デプロイに成功したことを示すメッセージが表示されます。

    ![出力のデプロイメント成功](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>モジュールを監視する  

1. VS Code コマンド パレットで、**[Azure IoT Hub: Select IoT Hub]\(Azure IoT Hub: IoT ハブの選択\)** を実行します。

2. 構成する IoT Edge デバイスが含まれているサブスクリプションと IoT ハブを選択します。 この場合、Azure Stack Edge Pro デバイスのデプロイに使用するサブスクリプションを選択し、Azure Stack Edge Pro デバイス用に作成された IoT Edge デバイスを選択します。 これは、前の手順で Azure portal からコンピューティングを構成した場合に発生します。

3. VS Code エクスプローラーで、[Azure IoT Hub] セクションを展開します。 **[デバイス]** に、Azure Stack Edge Pro デバイスに対応する IoT Edge デバイスが表示されます。 

    1. そのデバイスを選択して右クリックし、 **[組み込みイベント エンドポイントの監視を開始する]** を選択します。
  
        ![監視の開始](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **[デバイス]、[モジュール]** の順にアクセスして、**GPU モジュール** が実行中であることを確認します。

        ![IoT Hub のモジュール](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. VS Code ターミナルでも Azure Stack Edge Pro デバイスの監視出力に IoT Hub イベントが含まれていることを確認します。

        ![監視出力](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        CPU よりも GPU の方が、同じ操作セット (5000 回の図形変換) の実行にかかった時間がずっと短いことがわかります。

## <a name="next-steps"></a>次の手順

- [モジュールを使用するために GPU を構成する](azure-stack-edge-j-series-configure-gpu-modules.md)方法を確認します。
