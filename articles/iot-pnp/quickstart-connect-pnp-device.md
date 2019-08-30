---
title: IoT プラグ アンド プレイ プレビューのサンプル デバイス コードを IoT Hub に接続する | Microsoft Docs
description: IoT ハブと接続する IoT プラグ アンド プレイ プレビューのサンプル デバイス コードをビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b233fcecfe80d1ce4464d2d02fdddb188f9265a7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881581"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>クイック スタート:IoT プラグ アンド プレイ プレビューのデバイス アプリケーションのサンプルを IoT Hub に接続する

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、デバイスからハブに送信される情報を Azure IoT Explorer ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは C で記述され、C 対応の Azure IoT device SDK に含められています。ソリューション開発者は、Azure IoT Explorer ツールを使用すれば、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、ご利用のローカル コンピューター上に次のソフトウェアをインストールする必要があります。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/): Visual Studio のインストール時に、**NuGet パッケージ マネージャー** コンポーネントと **C++ によるデスクトップ開発**ワークロードを必ず含めるようにしてください。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer をインストールする

[最新のリリース](https://github.com/Azure/azure-iot-explorer/releases) ページから Azure IoT Explorer ツールをダウンロードしてインストールします。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT ハブを準備する

また、このクイックスタートを完了するには、ご利用の Azure サブスクリプション内に Azure IoT ハブが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

Azure CLI 用の Microsoft Azure IoT 拡張機能を追加します。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

次のコマンドを実行して、ご利用の IoT ハブに新しいデバイス ID を作成します。 **YourIoTHubName** および **YourDevice** プレースホルダーを実際の名前に置き換えます。 IoT Hub をお持ちではない場合は、[次の手順に従って作成します](../iot-hub/iot-hub-create-using-cli.md)。

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

次のコマンドを実行して、登録したばかりのデバイス用の_デバイス接続文字列_を取得します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

次のコマンドを実行して、ご利用の IoT ハブに対する _IoT ハブ接続文字列_を取得します。

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>開発環境の準備

### <a name="get-azure-iot-device-sdk-for-c"></a>C 対応の Azure IoT device SDK を取得する

このクイックスタートでは、Azure IoT C device SDK を複製してビルドするのに使用できる開発環境を準備します。

コマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

この操作は、完了するまでに数分かかります。

## <a name="build-the-code"></a>コードのビルド

ビルドしたアプリケーションでは、IoT ハブに接続するデバイスのシミュレーションが行われます。 アプリケーションによりテレメトリとプロパティが送信され、コマンドが受け取られます。

1. デバイス SDK ルート フォルダーに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、デバイス SDK と生成されたコード スタブをビルドします。

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > ご利用の C++ コンパイラが cmake で見つからない場合は、前のコマンドを実行したときにビルド エラーが発生します。 それが発生した場合は、[Visual Studio コマンド プロンプト](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)で次のコマンドを実行してみてください。

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

IoT ハブ デバイス接続文字列をパラメーターとして渡して、アプリケーションを実行します。

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

そのデバイス アプリケーションによって IoT Hub へのデータの送信が開始されます。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT Explorer を使用してコードを検証する

1. Azure IoT Explorer を開くと、 **[アプリの構成]** ページが表示されます。

1. ご利用の IoT Hub 接続文字列を入力し、 **[接続]** をクリックします。

1. 接続すると、デバイスの概要ページが表示されます。

1. 会社リポジトリを追加するには、 **[設定]** 、 **[+ 新規]** 、 **[On the connected device]\(接続されているデバイスに\)** の順に選択します。

1. デバイスの概要ページで、前に作成したデバイス ID を見つけ、それを選択して詳細を表示します。

1. **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** という ID を持つインターフェイスを展開して、IoT プラグ アンド プレイのプリミティブ (プロパティ、コマンド、テレメトリ) を表示します。

1. **[テレメトリ]** ページを選択して、デバイスから送信されているテレメトリ データを表示します。

1. **[プロパティ (書き込み不可)]** ページを選択して、デバイスから報告された書き込みができないプロパティを表示します。

1. **[プロパティ (書き込み可能)]** ページを選択して、更新を行うことができる書き込み可能なプロパティを表示します。

1. プロパティ**名** を展開し、新しい名前で更新して、 **[書き込み可能なプロパティの更新]** を選択します。 

1. 新しい名前が **[報告されるプロパティ]** 列に表示されるのを確認するには、ページの上部にある **[更新]** ボタンをクリックします。

1. **[コマンド]** ページを選択して、デバイスでサポートされているコマンドをすべて表示します。

1. **点滅**コマンドを展開し、新しい点滅時間間隔を設定します。 **[コマンドの送信]** を選択して、デバイス上でコマンドを呼び出します。

1. シミュレートするデバイスにアクセスして、コマンドが想定どおりに実行されたことを確認します。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: IoT プラグ アンド プレイ プレビュー デバイスに接続してやりとりする](howto-develop-solution.md)
