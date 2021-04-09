---
title: IoT プラグ アンド プレイ ブリッジをビルドおよびデプロイする方法 | Microsoft Docs
description: IoT プラグ アンド プレイ ブリッジのコンポーネントを識別します。 それを IoT デバイスまたはゲートウェイで実行したり、IoT Edge モジュールとして実行したりする方法について説明します。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c89427f83600d3b8091d5293b1757fa6f1a15ef1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202486"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジをビルドおよびデプロイする

[IoT プラグ アンド プレイ ブリッジ](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture)を使用すると、ゲートウェイに接続されている既存のデバイスを IoT ハブに接続できます。 ブリッジを使用して、接続されているデバイスに IoT プラグ アンド プレイ インターフェイスをマップします。 IoT プラグ アンド プレイ インターフェイスでは、デバイスによって送信されるテレメトリ、デバイスとクラウド間で同期されるプロパティ、およびデバイスによって応答されるコマンドが定義されます。 オープンソースのブリッジ アプリケーションを Windows または Linux のゲートウェイにインストールして構成することができます。 さらに、ブリッジを Azure IoT Edge ランタイム モジュールとして実行できます。

この記事では、以下の方法について説明します。

- ブリッジを構成する。
- さまざまな環境でブリッジをビルドして実行する方法。

ブリッジの使用方法を示す簡単な例については、「[Linux または Windows 上で実行されている IoT プラグ アンド プレイ ブリッジのサンプルを IoT Hub に接続する方法](howto-use-iot-pnp-bridge.md)」を参照してください。

この記事のガイダンスとサンプルでは、[Azure Digital Twins](../digital-twins/overview.md) と [IoT プラグ アンド プレイ](overview-iot-plug-and-play.md)に関する基本的な知識があることを前提としています。

## <a name="general-prerequisites"></a>一般的な前提条件

### <a name="supported-os-platforms"></a>サポートされる OS プラットフォーム

次の OS プラットフォームおよびバージョンがサポートされています。

|プラットフォーム  |サポートされているバージョン  |
|---------|---------|
|Windows 10 |     すべての Windows SKU がサポートされています。 次に例を示します。IoT Enterprise、Server、Desktop、IoT Core。 "*カメラの正常性の監視機能については、20H1 以降のビルドをお勧めします。その他のすべての機能は、すべての Windows 10 ビルドで使用できます。* "  |
|Linux     |Ubuntu 18.04 でテストおよびサポートされています。他のディストリビューションで機能するかどうかはテストされていません。         |
||

### <a name="hardware"></a>ハードウェア

- 上記の OS SKU およびバージョンをサポートする任意のハードウェア プラットフォーム。
- シリアル、USB、Bluetooth、およびカメラの周辺機器およびセンサーは、ネイティブでサポートされています。 IoT プラグ アンド プレイ ブリッジは、任意のカスタム周辺機器またはセンサーをサポートするように拡張できます。

### <a name="azure-iot-products-and-tools"></a>Azure IoT の製品とツール

- **Azure IoT Hub** - Azure サブスクリプションには、デバイスを接続する [Azure IoT Hub](../iot-hub/index.yml) が必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 IoT ハブがない場合は、[こちらの手順に従って作成します](../iot-hub/iot-hub-create-using-cli.md)。 IoT プラグ アンド プレイ サポートは、Basic レベルの IoT Hub には含まれていません。
- **Azure IoT エクスプローラー** - IoT プラグ アンド プレイ デバイスを操作するには、Azure IoT エクスプローラー ツールを使用できます。 ご利用のオペレーティング システム用の [Azure IoT エクスプローラーの最新リリースをダウンロードしてインストール](./howto-use-iot-explorer.md)します。 IoT Hub に接続するように Azure IoT エクスプローラー ツールを構成し、クローニングした **iot-plug-and-play-bridge** の *pnpbridge\docs\schemas* フォルダーでモデル定義を見つけます。

## <a name="configure-a-bridge"></a>ブリッジを構成する

ブリッジを構成するには、次の 2 つの方法があります。

- ブリッジが IoT デバイスまたはゲートウェイでネイティブに実行される場合は、構成ファイルを使用します。 このシナリオでは、Linux または Windows のマシンを使用できます。
- ブリッジが IoT Edge ランタイムで IoT Edge モジュールとして実行される場合は、モジュール ツインの必要なプロパティを使用します。 このシナリオでは、IoT Edge ランタイムが Linux 環境でホストされていることを前提としています。

### <a name="configuration-file"></a>構成ファイル

プラグ アンド プレイ ブリッジが IoT デバイスまたはゲートウェイでネイティブに実行される場合は、構成ファイルを使用して次のことが行われます。

- IoT Central または IoT Hub の接続情報が取得されます。
- IoT プラグ アンド プレイ インターフェイスを公開するデバイスが構成されます。

次のいずれかのオプションを使用して、ブリッジに構成ファイルを提供します。

- 構成ファイルへのパスをコマンドライン パラメーターとしてブリッジの実行可能ファイルに渡します。
- *pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console* フォルダー内の既存の *config.json* ファイルを使用します。

[構成ファイルのスキーマ](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json)は、GitHub リポジトリで入手できます。

> [!TIP]
> VS Code でブリッジの構成ファイルを編集する場合は、このスキーマ ファイルを使用してファイルを検証できます。

### <a name="iot-edge-module-configuration"></a>IoT Edge モジュールの構成

ブリッジが IoT Edge ランタイムで IoT Edge モジュールとして実行される場合、構成ファイルは必要な `PnpBridgeConfig` プロパティへの更新としてクラウドから送信されます。 ブリッジは、アダプターとコンポーネントを構成する前に、このプロパティの更新を待機します。

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>IoT デバイスまたはゲートウェイにブリッジをビルドして実行する

| プラットフォーム | サポートされています |
| :-----------: | :-----------: |
| Windows |  はい |
| Linux | はい |

### <a name="prerequisites"></a>前提条件

このセクションを完了するには、ご利用のローカル コンピューターに次のソフトウェアをインストールする必要があります。

- C++ のコンパイルをサポートする開発環境 ([Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/) など) - Visual Studio のインストール時に、NuGet パッケージ マネージャー コンポーネントと **C++ によるデスクトップ開発** ワークロードを必ず含めるようにしてください。
- [CMake](https://cmake.org/download/) - CMake をインストールするときに、 **[CMake をシステム パスに追加する]** オプションを選択します。
- Windows でビルドする場合は、[Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk) もダウンロードする必要があります。

### <a name="source-code"></a>ソース コード

[IoT プラグアンドプレイ ブリッジ](https://github.com/Azure/iot-plug-and-play-bridge) リポジトリをローカル コンピューターにクローニングします。

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

前のコマンドの実行には数分かかることが予想されます。

> [!NOTE]
> Windows で `git submodule update` が失敗する問題が発生した場合は、`git config --system core.longpaths true` というコマンドを実行してその問題を解決してください。

### <a name="build-the-bridge-on-windows"></a>Windows でブリッジをビルドする

**VS 2019 の開発者コマンド プロンプト** を開き、クローニングしたリポジトリが格納されているフォルダーに移動し、次のコマンドを実行します。

```console
cd pnpbridge\scripts\windows

build.cmd
```

前のコマンドの実行には数分かかることが予想されます。

必要に応じて、生成された *pnpbridge\cmake\pnpbridge_x86\azure_iot_pnp_bridge.sln* ソリューション ファイルを Visual Studio で開いて、コードを編集、リビルド、デバッグすることができます。

> [!TIP]
> このプロジェクトでは、CMAKE を使用してプロジェクト ファイルを生成します。 適切な CMAKE ファイルが更新されていない場合、Visual Studio でプロジェクトに加えた変更がすべて失われる可能性があります。

### <a name="build-the-bridge-on-linux"></a>Linux でブリッジをビルドする

Bash シェルを使用して、クローニングしたリポジトリが格納されているフォルダーに移動し、次のコマンドを実行します。

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>汎用センサーを構成する

Windows の *iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console* フォルダー、または Windows の *iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_linux\src/pnpbridge/samples/console* フォルダーの *config.json* ファイルの `pnp_bridge_connection_parameters` ノードの下にある次のパラメーターを変更します。

接続文字列を使用して IoT Hub に接続している場合は、次のようにします。

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> `symmetric_key` 値は、接続文字列の SAS キーと一致している必要があります。

DPS を使用して IoT Hub または IoT Central アプリケーションに接続している場合は、次のようにします。

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

構成ファイルの残りの部分を確認して、このサンプルで構成されているインターフェイス コンポーネントとグローバル パラメーターを確認します。

### <a name="start-the-bridge-in-windows"></a>Windows でブリッジを開始する

コマンド プロンプトでブリッジを実行して開始します。

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> 別の場所にある構成ファイルを使用するには、ブリッジを実行するときに、そのパスをコマンドライン パラメーターとして渡します。
  
> [!TIP]
> 内蔵カメラが搭載されているか、USB カメラが PC に接続されている場合は、カメラを使用するアプリケーション (組み込みの **カメラ** アプリなど) を起動できます。 **カメラ** アプリが実行されている場合は、ブリッジ コンソールの出力に監視の統計情報が表示され、フレーム レートがデジタル ツイン インターフェイスを通じて IoT Hub に報告されます。

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>IoT Edge モジュールとしてブリッジをビルドして実行する

| プラットフォーム | サポートされています |
| :-----------: | :-----------: |
| Windows |  いいえ |
| Linux | はい |

### <a name="prerequisites"></a>前提条件

このセクションを完了するには、無料または Standard レベルの Azure IoT Hub が必要です。 IoT Hub を作成する方法については、[IoT hub の作成](../iot-hub/iot-hub-create-through-portal.md)に関する記事を参照してください。

このセクションの手順では、Windows 10 コンピューターに次の開発環境があることを前提としています。 これらのツールを使用すると、IoT Edge モジュールをビルドして IoT Edge デバイスにデプロイできます。

- Ubuntu 18.04 LTS が実行されている Windows Subsystem for Linux (WSL) 2。 詳細については、「[Windows 10 用 Windows Subsystem for Linux のインストール ガイド](/windows/wsl/install-win10)」を参照してください。
- WSL 2 を使用するように構成された Docker Desktop for Windows。 詳細については、「[Docker Desktop WSL 2 バックエンド](https://docs.docker.com/docker-for-windows/wsl/)」を参照してください。
- [Windows 環境にインストールされた Visual Studio Code](https://code.visualstudio.com/docs/setup/windows) (次の 3 つの拡張機能がインストールされていること)。

  - [Remote Development extension pack](https://aka.ms/vscode-remote/download/extension) - この拡張機能を使用すると、WSL 2 でコードをビルドして実行できます。
  - [Docker for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) - VS Code の **WSL:Ubuntu-18.04** 環境で、この拡張機能を有効にする必要があります。
  - [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) - VS Code の **WSL:Ubuntu-18.04** 環境で、この拡張機能を有効にする必要があります。

- WSL 2 環境で次のコマンドを実行して、必要なビルド ツールをインストールします。

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- Azure リソースを管理するために、WSL 2 環境に [Azure CLI](/cli/azure/install-azure-cli-apt) がインストールされている。

  > [!TIP]
  > 必要に応じて、CLI が事前にインストールされている [Azure Cloud Shell](https://shell.azure.com/) で `az` コマンドを実行できます。

### <a name="create-an-iot-edge-device"></a>IoT Edge デバイスを作成する

このセクションのコマンドを実行すると、Azure 仮想マシンで実行される IoT Edge デバイスが作成されます。 実際のデバイスにアクセスできない場合は、仮想マシンで IoT Edge デバイスを実行すると、デプロイのテストを行うことができます。

IoT Hub に IoT Edge デバイスの登録を作成するには、WSL 2 環境で次のコマンドを実行します。 `az login` コマンドを使用して、Azure サブスクリプションにサインインします。

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

IoT Edge ランタイムがインストールされた Azure 仮想マシンを作成するには、次のコマンドを実行します。 プレースホルダーを適切な値で更新します。

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

これで、IoT Edge ランタイムが仮想マシンで実行されるようになりました。 次のコマンドを使用すると、デバイスで **$edgeAgent** と **$edgeHub** が実行されていることを確認できます。

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> この仮想マシンが実行されている間は料金が発生します。 使用していないときはシャットダウンし、不要になった場合は削除するようにしてください。

### <a name="download-the-source-code"></a>ソース コードをダウンロードする

以降の手順では、WSL 2 環境で Docker イメージをビルドします。 **iot-plug-and-play-bridge** リポジトリをクローニングするには、適切なフォルダーで WSL 2 Bash シェルを使用して次のコマンドを実行します。

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

前のコマンドの実行には数分かかることが予想されます。

### <a name="update-the-dockerfile"></a>*Dockerfile* を更新する

VS Code を起動して、コマンド パレットを開き、「*Remote WSL:Open folder in WSL*」と入力して、クローニングされたリポジトリが含まれている *iot-plug-and-play-bridge* フォルダーを開きます。

*pnpbridge\Dockerfile.amd64* ファイルを開きます。 環境変数の定義を次のように編集します。

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> 次のコマンドを使用すると、デバイスの接続文字列を取得できます: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

他のアーキテクチャ用の *Dockerfiles* のサンプルがあります。

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>IoT プラグ アンド プレイ ブリッジ モジュールのイメージをビルドする

VS Code で、コマンド パレットを開き、「*Docker Registries:Log In to Docker CLI*」と入力して、Azure サブスクリプションとコンテナー レジストリを選択します。 このコマンドを実行すると、Docker でコンテナー レジストリに接続し、モジュール イメージをアップロードできるようになります。

*pnpbridge/module.json* ファイルを開きます。 コンテナー イメージ リポジトリとして `{your container registry name}.azurecr.io/iotpnpbridge` を追加し、バージョンとして `v1` を指定します。

VS Code の **[エクスプローラー]** ビューで *pnpbridge/module.json* ファイルを右クリックして、 **[IoT Edge モジュール イメージをビルドしてプッシュ]** を選択し、プラットフォームとして **[amd64]** を選択します。

VS Code の **[Docker]** ビューで、**iotpnpbridge:V1-amd64** モジュール イメージが格納されたコンテナー レジストリの内容を参照できます。

### <a name="create-a-container-registry"></a>コンテナー レジストリを作成する

IoT Edge デバイスによって、コンテナー レジストリからモジュール イメージがダウンロードされます。 この例では、Azure Container Registry を使用します。

**bridge-edge-resources** リソース グループに Azure Container Registry を作成します。 次に、コンテナー レジストリへの管理者アクセスを有効にして、IoT Edge デバイスでモジュール イメージをダウンロードするために必要な資格情報を取得します。

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>配置マニフェストを作成する

IoT Edge 配置マニフェストには、IoT Edge デバイスにデプロイするモジュールと構成値を指定します。

VS Code で、*pnpbridge/deployment.template.json* ファイルを開きます。

- 前のコマンドの値を使用して `registryCredentials` を更新します。 `address` 値は `{your container registry name}.azurecr.io` のようになります。
- `ModulePnpBridge` の `image` 値を更新します。 モジュール イメージは、次のようになります: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64`。
- `PnPBridgeConfig` を次の JSON に置き換えて、CO2 検出アダプターを構成します。

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  変更を保存します。

VS Code の **[エクスプローラー]** ビューで *pnpbridge/deployment.template.json* ファイルを右クリックし、 **[IoT Edge 配置マニフェストの生成]** を選択します。 このコマンドによって、*pnpbridge/config/deployment.amd64.json* 配置マニフェストが生成されます。

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>IoT Edge デバイスにブリッジをデプロイする

VS Code で、コマンド パレットを開き、「*Azure IoT Hub:Select IoT Hub*」と入力して、サブスクリプションと IoT Hub を選択します。

VS Code の **[エクスプローラー]** ビューで *pnpbridge/config/deployment.amd64.json* ファイルを右クリックし、 **[1 つのデバイスのデプロイを作成]** を選択して、 **[bridge-edge-device]** を選択します。

デバイス上のモジュールの状態を表示するには、次のコマンドを実行します。

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

実行中のモジュールの一覧に、CO2 検出アダプターを使用するように構成されている **ModulePnpBridge** モジュールが含まれるようになりました。

### <a name="tidy-up"></a>片付ける

仮想マシンとコンテナー レジストリを Azure サブスクリプションから削除するには、次のコマンドを実行します。

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>フォルダー構造

*pnpbridge\deps\azure-iot-sdk-c-pnp*:Azure IoT Device SDK for C SDK を含む Git サブモジュール。

*pnpbridge\scripts*:ビルド スクリプト。

*pnpbridge\src*:IoT プラグ アンド プレイ ブリッジ コアのソース コード。

*pnpbridge\src\adapters*:さまざまな IoT プラグ アンド プレイ ブリッジ アダプターのソース コード。

## <a name="next-steps"></a>次のステップ

IoT プラグ アンド プレイ ブリッジの詳細については、[IoT プラグ アンド プレイ ブリッジ](https://github.com/Azure/iot-plug-and-play-bridge)の GitHub リポジトリを参照してください。