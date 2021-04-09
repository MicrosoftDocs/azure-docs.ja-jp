---
title: IoT Edge の透過的なゲートウェイを Azure IoT Central アプリケーションに接続する
description: IoT Edge の透過的なゲートウェイを経由してデバイスを IoT Central アプリケーションに接続する方法
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bdfb5f65106f3f8843b4aa52b752f5e563ab03f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102619954"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>IoT Edge の透過的なゲートウェイを経由してデバイスを接続する方法

IoT Edge デバイスは、ローカル ネットワーク上の他のデバイスと IoT Central アプリケーションの間の接続を提供するゲートウェイとして機能できます。 ゲートウェイは、デバイスから IoT Central アプリケーションに直接アクセスできない場合に使用します。

IoT Edge では、[*透過的* と *変換* のゲートウェイ パターン](../../iot-edge/iot-edge-as-gateway.md)がサポートされています。 この記事では、透過的ゲートウェイ パターンを実装する方法の概要を示します。 このパターンでは、ゲートウェイは、ダウンストリーム デバイスから IoT Central アプリケーションの IoT Hub エンドポイントにメッセージを渡します。

この記事では、仮想マシンを使用してダウンストリーム デバイスとゲートウェイをホストします。 実際のシナリオでは、ダウンストリーム デバイスとゲートウェイはローカル ネットワーク上の物理デバイス上で実行されます。

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

**[カスタム アプリ] > [カスタム アプリケーション]** テンプレートを使用して IoT Central アプリケーションを作成するには、「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」クイックスタートを完了します。

この記事の手順を実行するには、次のファイルをコンピューターにダウンロードします。

- [Thermostat デバイス モデル](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [透過的なゲートウェイのマニフェスト](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>デバイス テンプレートの追加

ダウンストリーム デバイスとゲートウェイ デバイスの両方に、IoT Central のデバイス テンプレートが必要です。 IoT Central を使用して、ダウンストリーム デバイスとゲートウェイ間の関係をモデル化し、それらが接続された後にそれらを表示および管理することができます。

ダウンストリーム デバイスのデバイス テンプレートを作成するには、デバイスの機能をモデル化する標準のデバイス テンプレートを作成します。 この記事で示している例では、サーモスタット デバイス モデルを使用します。

ダウンストリーム デバイスのデバイス テンプレートを作成するには、次のようにします。

1. デバイス テンプレートを作成し、テンプレートの種類として **[IoT デバイス]** を選択します。

1. ウィザードの **[カスタマイズ]** ページで、デバイス テンプレートの名前 (*Thermostat* など) を入力します。

1. デバイス テンプレートを作成したら、 **[Import a model]\(モデルのインポート\)** を選択します。 以前にダウンロードした *thermostat-1.json* ファイルなどのモデルを選択します。

1. サーモスタット用の既定のビューを生成するには、ビューを選択してから **[既定のビューを生成する]** を選択します。

1. デバイス テンプレートを発行する。

透過的な IoT Edge ゲートウェイ用のデバイス テンプレートを作成するには、次のようにします。

1. デバイス テンプレートを作成し、テンプレートの種類として **[Azure IoT Edge]** を選択します。

1. ウィザードの **[カスタマイズ]** ページで、デバイス テンプレートの名前 (*Edge Gateway* など) を入力します。

1. ウィザードの **[カスタマイズ]** ページで、 **[Gateway device with downstream devices]\(ダウンストリーム デバイスがあるゲートウェイ デバイス\)** チェック ボックスをオンにします。

1. ウィザードの **[カスタマイズ]** ページで、 **[参照]** を選択します。 以前にダウンロードした *EdgeTransparentGatewayManifest.json* ファイルをアップロードします。

1. **[Relationships]\(関係\)** のエントリをダウンストリーム デバイス テンプレートに追加します。

次のスクリーンショットは、**Thermostat** デバイス テンプレートを使用するダウンストリーム デバイスがある IoT Edge ゲートウェイ デバイスの **[Relationships]\(関係\)** ページを示しています。

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="IoT Edge ゲートウェイ デバイス テンプレートと、サーモスタット ダウンストリーム デバイス テンプレートの関係を示すスクリーンショット。":::

前のスクリーンショットは、モジュールが定義されていない IoT Edge ゲートウェイ デバイス テンプレートを示しています。 IoT Edge ランタイムによって、ダウンストリーム デバイスから IoT Central にメッセージが転送されるため、透過的なゲートウェイにはモジュールは必要ありません。 テレメトリの送信、プロパティの同期、またはコマンドの処理をゲートウェイ自体で行う必要がある場合、既定のコンポーネントで、またはモジュールでこれらの機能を定義できます。

クラウドのプロパティとビューで必要なものがあれば、ゲートウェイとダウンストリーム デバイスのテンプレートを発行する前に追加します。

## <a name="add-the-devices"></a>デバイスの追加

デバイスを IoT Central アプリケーションに追加するときに、ダウンストリーム デバイスと透過的なゲートウェイの間の関係を定義できます。

デバイスを追加するには、次の手順を実行します。

1. IoT Central アプリケーションのデバイス ページにアクセスします。

1. 透過的なゲートウェイの IoT Edge デバイスのインスタンスを追加します。 この記事では、ゲートウェイ デバイス ID は `edgegateway` です。

1. ダウンストリーム デバイスの 1 つ以上のインスタンスを追加します。 この記事では、ダウンストリーム デバイスは ID が `thermostat1` と `thermostat2` であるサーモスタットです。

1. デバイスの一覧で、それぞれのダウンストリーム デバイスを選択して **[ゲートウェイに接続]** を選択します。

次のスクリーンショットは、ゲートウェイに接続されているデバイスの一覧を **[ダウンストリーム デバイス]** ページで表示できることを示しています。

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="透過的なゲートウェイに接続されているダウンストリーム デバイスの一覧を示すスクリーンショット。":::

透過的なゲートウェイでは、ダウンストリーム デバイスはゲートウェイ自体に接続し、ゲートウェイによってホストされるカスタム モジュールには接続しません。

デバイスをデプロイする前に、次のものが必要です。

- IoT Central アプリケーションの **ID スコープ**。
- ゲートウェイとダウンストリーム デバイスの **デバイス ID** の値。
- ゲートウェイとダウンストリーム デバイスの **主キー** の値。

これらの値を確認するには、デバイスの一覧で各デバイスにアクセスして **[接続]** を選択します。 続行する前に、これらの値をメモしておきます。

## <a name="deploy-the-gateway-and-devices"></a>ゲートウェイとデバイスのデプロイ

このシナリオを試すことができるよう、次の手順では、ゲートウェイとダウンストリーム デバイスを Azure 仮想マシンにデプロイする方法を示しています。 実際のシナリオでは、ダウンストリーム デバイスとゲートウェイはローカル ネットワーク上の物理デバイス上で実行されます。

透過的なゲートウェイのシナリオを試すには、次のボタンを選択して 2 つの Linux 仮想マシンをデプロイします。 一方の仮想マシンは透過的な IoT Edge ゲートウェイであり、もう一方はサーモスタットをシミュレートするダウンストリーム デバイスです。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

2 つの仮想マシンがデプロイされて実行状態になったら、IoT Edge ゲートウェイ デバイスが `edgegateway` 仮想マシンで実行されていることを確認します。

1. IoT Central アプリケーションの **[デバイス]** ページにアクセスします。 IoT Edge ゲートウェイ デバイスが IoT Central に接続されている場合、そのステータスは "**プロビジョニング済み**" です。

1. IoT Edge ゲートウェイ デバイスを開き、 **[モジュール]** ページでモジュールのステータスを確認します。 IoT Edge ランタイムが正常に開始された場合、 **$edgeAgent** および **$edgeHub** モジュールのステータスは "**実行中**" です。

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="IoT Edge ゲートウェイで実行中の $edgeAgent および $edgeHub モジュールを示すスクリーンショット。":::

> [!TIP]
> 仮想マシンが起動してデバイスが IoT Central アプリケーションでプロビジョニングされる間、数分待つことが必要な場合があります。

## <a name="configure-the-gateway"></a>ゲートウェイを構成する

IoT Edge デバイスが透過的なゲートウェイとして機能するためには、あらゆるダウンストリーム デバイスに対してその ID を証明するための証明書がいくつか必要です。 この記事ではデモ証明書を使用します。 運用環境では、利用している証明機関からの証明書を使用してください。

デモ証明書を生成してゲートウェイ デバイスにインストールするには、次のようにします。

1. SSH を使用して、ゲートウェイ デバイスの仮想マシンに接続してサインインします。

1. 次のコマンドを実行して、IoT Edge リポジトリをクローンし、デモ証明書を生成します。

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    上記のコマンドを実行すると、後続の手順で下記のファイルを使用する準備が整います。

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* - IoT Edge シナリオをテストするための、その他すべてのデモ用証明書を作成するために使用されるルート CA 証明書。
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* - *config.yaml* ファイルから参照されるデバイス CA 証明書。 ゲートウェイのシナリオでは、IoT Edge デバイスはこの CA 証明書によって、ダウンストリーム デバイスに対して自らの ID を検証します。
    - *~/certs/private/iot-edge-device-mycacert.key.pem* - デバイス CA 証明書に関連付けられた秘密キー。

    これらのデモ証明書の詳細については、「[IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](../../iot-edge/how-to-create-test-certificates.md)」を参照してください。

1. テキスト エディターで *config.yaml* ファイルを開きます。 次に例を示します。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. `Certificate settings` 設定を探します。 次のように、証明書の設定をコメント解除して変更します。

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    上記の例では、**AzureUser** としてサインイン済みであり、"mycacert" という名前のデバイス CA 証明書が作成済みであると想定しています。

1. 変更を保存して IoT Edge ランタイムを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

変更後に IoT Edge ランタイムが正常に起動すると、IoT Central で、対象のゲートウェイ デバイスの **[モジュール]** ページにおいて **$edgeAgent** および **$edgeHub** モジュールのステータスが "**実行中**" に変わります。

ランタイムが起動しない場合は、*config.yaml* で行った変更を確認し、「[IoT Edge デバイスのトラブルシューティング](../../iot-edge/troubleshoot.md)」を参照してください。

透過的なゲートウェイが構成され、ダウンストリーム デバイスからテレメトリの転送を開始する準備ができました。

## <a name="provision-a-downstream-device"></a>ダウンストリーム デバイスのプロビジョニング

現時点では、IoT Edge でダウンストリーム デバイスを IoT Central アプリケーションに自動的にプロビジョニングすることはできません。 次の手順は、`thermostat1` デバイスをプロビジョニングする方法を示しています。 これらの手順を完了するには、Python 3.5 (またはそれ以降) がインストールされ、インターネットに接続されている環境が必要です。 [Azure Cloud Shell](https://shell.azure.com/) には Python 3.5 がプレインストールされています。

1. 次のコマンドを実行して、`azure.iot.device` モジュールをインストールします。

    ```bash
    pip install azure.iot.device
    ```

1. 次のコマンドを実行して、プロビジョニングを実行する Python スクリプトをダウンロードします。

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. IoT Central アプリケーションで `thermostat1` ダウンストリーム デバイスをプロビジョニングするには、次のコマンドを実行します。`{your application id scope}` および `{your device primary key}` は適宜置き換えてください。

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

IoT Central アプリケーションで、thermostat1 デバイスの **[デバイスの状態]** が "**プロビジョニング済み**" になっていることを確認します。 

## <a name="configure-a-downstream-device"></a>ダウンストリームのデバイスを構成する

前のセクションでは、デモ証明書を使用して `edgegateway` 仮想マシンを構成し、ゲートウェイとして実行できるようにしました。 `leafdevice` 仮想マシンは、ゲートウェイを使用して IoT Central に接続するサーモスタット シミュレーターをインストールする準備ができています。

`leafdevice` 仮想マシンには、`edgegateway` 仮想マシン上で作成したルート CA 証明書のコピーが必要です。 `edgegateway` 仮想マシンの */home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem* ファイルを、`leafdevice` 仮想マシンの home ディレクトリにコピーします。 **scp** コマンドを使用して、Linux 仮想マシンとの間でファイルをコピーできます。

ダウンストリーム デバイスからゲートウェイへの接続を確認する方法については、「[ゲートウェイ接続をテストする](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection)」を参照してください。

`leafdevice` 仮想マシン上でサーモスタット シミュレーターを実行するには、次のようにします。

1. Python サンプルをホーム ディレクトリにダウンロードします。

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Azure IoT デバイスの Python モジュールをインストールします。

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. サンプルを構成するための環境変数を設定します。 `{your device shared key}` は、以前にメモしておいた `thermostat1` の主キーに置き換えてください。 これらの変数では、ゲートウェイ仮想マシンの名前が `edgegateway`、サーモスタット デバイスの ID が `thermostat1` であると想定しています。

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    接続文字列で、IoT ハブの名前ではなくゲートウェイ デバイスの名前を使用していることに注目してください。

1. コードを実行するには、次のコマンドを使用します。

    ```bash
    python3 simple_thermostat.py
    ```

    このコマンドの出力は次のようになります。

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. IoT Central でテレメトリを確認するには、**thermostat1** デバイスの **[概要]** ページにアクセスします。

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="ダウンストリーム デバイスからのテレメトリを示すスクリーンショット。":::

    **[About]\(詳細情報\)** ページでは、ダウンストリーム デバイスから送信されたプロパティ値を表示できます。 **[コマンド]** ページでは、ダウンストリーム デバイスに対してコマンドを呼び出すことができます。

## <a name="next-steps"></a>次のステップ

IoT Central を使用して透過的なゲートウェイを構成する方法を理解したら、次のステップとして、[IoT Edge](../../iot-edge/about-iot-edge.md) についての理解を深めることをお勧めします。
