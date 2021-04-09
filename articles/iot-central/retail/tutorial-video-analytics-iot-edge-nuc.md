---
title: チュートリアル - Azure IoT Central でビデオ分析用 IoT Edge インスタンスを作成する (Intel NUC)
description: このチュートリアルでは、ビデオ分析 (物体とモーションの検出) アプリケーション テンプレートを使用して、ビデオ分析用 IoT Edge インスタンスを作成する方法について説明します。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831928"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>チュートリアル:ビデオ分析用の IoT Edge インスタンスを作成する (Intel NUC)

Azure IoT Edge は、以下をデプロイして実行することによってクラウド インテリジェンスをローカルで実現するフル マネージド サービスです。

* カスタム ロジック
* Azure サービス
* 人工知能

IoT Edge では、これらのサービスがクロスプラットフォームの IoT デバイス上で直接実行されるため、クラウドであれオフラインであれ、IoT ソリューションを安全かつ大規模に実行することができます。

このチュートリアルでは、IoT Edge ランタイムを Intel NUC デバイスにインストールして構成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * IoT Edge を更新して構成する
> * IoT Edge ゲートウェイを設定する
> * ONVIF に適合したローカルのカメラを Intel NUC デバイスに接続する

## <a name="prerequisites"></a>前提条件

* 開始する前に、これよりも前の [Azure IoT Central でのライブ ビデオ分析アプリケーションの作成 (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) に関するチュートリアルか、[Azure IoT Central でのビデオ分析の作成 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) に関するチュートリアルを完了しておく必要があります。
* Linux を実行するデバイス (Intel NUC など)。Docker コンテナーを実行でき、なおかつビデオ分析を実行できるだけの処理能力を備えている必要があります。
* デバイスに [IoT Edge ランタイムがインストール](../../iot-edge/how-to-install-iot-edge.md)され、実行されていること。
* Windows マシンから IoT Edge デバイスに接続できること。[PuTTY SSH クライアント](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)または同等のユーティリティが必要です。
* さらに、Azure サブスクリプションが必要となります。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で無料で作成できます。

## <a name="configure-the-iot-edge-device"></a>IoT Edge デバイスを構成する

Intel NUC マシンに IoT Edge ランタイムがインストールされていない場合は、「[Debian ベースの Linux システムに Azure IoT Edge ランタイムをインストールする](../../iot-edge/how-to-install-iot-edge.md)」の手順を参照してください。

IoT Edge ランタイムを更新するには:

1. PuTTY ユーティリティを使用して IoT Edge デバイスに接続します。

1. 次のコマンドを実行して、IoT Edge ランタイムのバージョンを更新してチェックします。 この記事の執筆時点では、バージョンは 1.0.9 です。

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. 次のコマンドを使用して、デプロイで使用するフォルダーを作成し、必要なアクセス許可を設定します。

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

IoT Edge デバイスの */data/storage* フォルダーに *state.json* 構成ファイルを追加するには:

1. テキスト エディターを使用して、ローカル コンピューターの *lva-configuration* フォルダーにある *state.json* ファイルを開きます。

1. `system` と `iotCentral > properties` のプレースホルダーを、実際のゲートウェイ デバイスを表す文字列値に更新します。 これらの値は、後で IoT Central アプリケーション ダッシュボードで確認できます。

1. `iotCentral > appKeys` のプレースホルダーを、前のチュートリアルで *scratchpad.txt* ファイルに書き留めた値に更新します。 変更を保存します。

1. コマンド プロンプトで PuTTY の `scp` ユーティリティを使用して、先ほど編集した *state.json* ファイルを、IoT Edge デバイス上の */data/storage* フォルダーにコピーします。 ここでは、IP アドレスの例として `192.168.0.144` を使用していますが、実際の IoT Edge デバイスの IP アドレスに置き換えてください。

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

IoT Central アプリケーションに登録、接続するように IoT Edge を構成するには:

1. PuTTY ユーティリティを使用して IoT Edge デバイスに接続します。

1. `nano` などのテキスト エディターを使用して、IoT Edge config.yaml ファイルを開きます。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > YAML ファイルでは、インデントにタブを使用できないので、代わりに 2 つのスペースを使用してください。 最上位の項目の先頭に空白を入れることはできません。

1. `# Manual provisioning configuration` が表示されるまで下にスクロールします。 次のスニペットに表示されているように、その次の 3 行をコメント アウトします。

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. `# DPS symmetric key provisioning configuration` が表示されるまで下にスクロールします。 次のスニペットに表示されているように、その次の 8 行をコメント解除します。

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. `{scope_id}` を、前のチュートリアルで *scratchpad.txt* ファイルに書き留めた **ID Scope** に置き換えます。

1. `{registration_id}` を *gateway-001* (前のチュートリアルで作成したデバイス) に置き換えます。

1. `{symmetric_key}` を、前のチュートリアルで *scratchpad.txt* ファイルに書き留めた **gateway-001** デバイスの **主キー** に置き換えます。

1. 次のコマンドを実行して、IoT Edge デーモンを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge モジュールの状態を確認するには、次のコマンドを実行します。

    ```bash
    iotedge list
    ```

    前のコマンドからの出力には、実行されている 5 つのモジュールが表示されます。 IoT Central アプリケーションで実行されているモジュールの状態を表示することもできます。

    > [!TIP]
    > このコマンドを再実行すると、状態をチェックすることができます。 すべてのモジュールの実行が開始されるまでお待ちください。

IoT Edge モジュールが正しく起動しない場合は、「[IoT Edge デバイスのトラブルシューティング](../../iot-edge/troubleshoot.md)」を参照してください。

## <a name="collect-the-rtsp-stream-from-your-camera"></a>カメラから RTSP ストリームを収集する

IoT Edge デバイスに接続されたカメラの RTSP ストリームの URL を識別します。その例を次に示します。

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> IoT Edge コンピューターで、VLC などのメディア プレーヤーを使用してカメラ ストリームを表示してみましょう。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを使い終えたら、次の手順で、作成したリソースをすべて削除することができます。

1. IoT Central アプリケーションの **[管理]** セクションの **[お客様のアプリケーション]** ページに移動します。 次に、 **[削除]** を選択します。
1. Azure portal で、**lva-rg** リソース グループを削除します。
1. ローカル コンピューターで、**amp-viewer** Docker コンテナーを停止します。

## <a name="next-steps"></a>次のステップ

Intel NUC ゲートウェイ デバイスに IoT Edge ランタイムと LVA モジュールをデプロイしました。

カメラを管理する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [ビデオ分析 (物体とモーションの検出) アプリケーションを監視、管理する](./tutorial-video-analytics-manage.md)