---
title: IoT プラグ アンド プレイ プレビュー デバイスを作成する | Microsoft Docs
description: デバイス機能モデルを使用してデバイス コードを作成します。 次に、デバイス コードを実行して、デバイスがご利用の IoT Hub に接続されることを確認します。
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6e5e08df444f66f2c5500d968c805552d20901c5
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861196"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>クイック スタート:デバイス機能モデルを使用して IoT プラグ アンド プレイ デバイスを作成する

"_デバイス機能モデル_" (DCM) には、IoT プラグ アンド プレイ デバイスの機能が記述されています。 DCM は多くの場合、製品 SKU に関連付けられています。 DCM で定義されている機能は、再利用可能なインターフェイスにまとめられています。 DCM からスケルトン デバイス コードを生成できます。 このクイックスタートでは、VS Code において DCM を使用して IoT プラグ アンド プレイ デバイスを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、ご利用のローカル コンピューター上に次のソフトウェアをインストールする必要があります。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/): Visual Studio のインストール時に、**NuGet パッケージ マネージャー** コンポーネントと **C++ によるデスクトップ開発**ワークロードを必ず含めるようにしてください。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。
* [Visual Studio Code](https://code.visualstudio.com/)。

### <a name="install-azure-iot-tools"></a>Azure IoT Tools のインストール

[VS Code 用の Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 拡張機能パックをインストールするには、次の手順に従います。

1. VS Code で、 **[拡張機能]** タブを選択します。
1. **Azure IoT Tools** を検索します。
1. **[インストール]** を選択します。

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer をインストールする

[最新のリリース](https://github.com/Azure/azure-iot-explorer/releases) ページから Azure IoT Explorer ツールをダウンロードしてインストールします。

### <a name="get-the-connection-string-for-your-company-model-repository"></a>ご利用の会社モデル リポジトリの接続文字列を取得する

Microsoft の職場または学校アカウントを使用するか、Microsoft パートナー ID (所持している場合) を使用してサインインすると、[Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) ポータルに、ご利用の_会社モデル リポジトリ接続文字列_が表示されます。 サインインしたら、 **[Company repository]\(会社リポジトリ\)** 、 **[接続文字列]** の順に選択します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT ハブを準備する

また、このクイックスタートを完了するには、ご利用の Azure サブスクリプション内に Azure IoT ハブが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> パブリック プレビュー中、IoT プラグ アンド プレイ機能は、**米国中部**、**北ヨーロッパ**、および**東日本**の各リージョンで作成された IoT ハブでのみご利用いただけます。

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

1. コマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    この操作は、完了するまでに数分かかります。

1. リポジトリのローカル複製のルートに `pnp_app` サブディレクトリを作成します。 デバイス モデル ファイルとデバイス コード スタブには、このフォルダーを使用します。

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>モデルを作成する

このクイックスタートでは、既存のサンプル デバイス機能モデルおよび関連するインターフェイスを使用します。

1. [デバイス機能モデル](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json)と[インターフェイスのサンプル](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json)をダウンロードし、ファイルを `pnp_app` フォルダーに保存します。

    > [!TIP]
    > GitHub からファイルをダウンロードするには、該当するファイルに移動して **[Raw]** を右クリックし、次に **[名前を付けてリンク先を保存]** を選択します。

1. VS Code を使用して `pnp_app` フォルダーを開きます。 IntelliSense を使用してファイルを表示できます。

    ![デバイス機能モデル](media/quickstart-create-pnp-device/dcm.png)

1. ダウンロードしたファイル内で、`@id` フィールド `schema` とフィールド内の `<YOUR_COMPANY_NAME_HERE>` を一意の値に置き換えます。 英数字 (a-z、A-Z、0-9) とアンダースコアのみを使用してください。 詳細については、[デジタル ツイン識別子の形式](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)に関するページを参照してください。

## <a name="generate-the-c-code-stub"></a>C コード スタブを生成する

DCM とそれに関連するインターフェイスの用意ができたので、モデルを実装するデバイス コードを生成できます。 VS Code で C コード スタブを生成するには:

1. DCM ファイルが開いているフォルダーで、**Ctrl + Shift + P** キーを押してコマンド パレットを開き、「**IoT プラグ アンド プレイ**」と入力して、 **[Generate Device Code Stub]\(デバイス コード スタブを生成する\)** を選択します。

    > [!NOTE]
    > IoT プラグ アンド プレイ コード ジェネレーター ユーティリティを初めて使用するときは、ダウンロードに数秒かかります。

1. デバイス コード スタブの生成に使用する DCM ファイルを選択します。

1. プロジェクト名として「**sample_device**」と入力します。これは、デバイス アプリケーションの名前になります。

1. 使用する言語として、 **[ANSI C]** を選択します。

1. 使用するプロジェクトの種類として **[CMake プロジェクト]** を選択します。

1. 接続方法として **[Via IoT Hub device connection string]\(IoT Hub デバイス接続文字列による\)** を選択します。

1. VS Code では新しいウィンドウが開き、生成されたデバイス コード スタブ ファイルが表示されます。
    ![デバイス コード](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>コードのビルド

デバイス SDK を使用して、生成されたデバイス コード スタブをビルドします。 ビルドしたアプリケーションでは、IoT ハブに接続するデバイスのシミュレーションが行われます。 アプリケーションによりテレメトリとプロパティが送信され、コマンドが受け取られます。

1. VS Code で、デバイス SDK ルート フォルダーにある `CMakeLists.txt` を開きます。

1. コンパイル時にデバイス コード スタブ フォルダーが取り込まれるように、`CMakeLists.txt` ファイルの末尾に次の行を追加します。

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. デバイス SDK ルート フォルダーに cmake サブディレクトリを作成し、そのフォルダーに移動します。

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、デバイス SDK と生成されたコード スタブをビルドします。

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > ご利用の C++ コンパイラが cmake で見つからない場合は、前のコマンドを実行したときにビルド エラーが発生します。 それが発生した場合は、[Visual Studio コマンド プロンプト](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)で次のコマンドを実行してみてください。

1. ビルドが正常に完了したら、IoT ハブ デバイス接続文字列をパラメーターとして渡すアプリケーションを実行します。

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. そのデバイス アプリケーションによって IoT Hub へのデータの送信が開始されます。

    ![実行中のデバイス アプリ](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>コードを検証する

### <a name="publish-device-model-files-to-model-repository"></a>デバイス モデル ファイルをモデル リポジトリに発行する

**Azure IoT Explorer** を使用してデバイス コードを検証するには、モデル リポジトリにファイルを発行する必要があります。

1. DCM ファイルが開いているフォルダーで、**Ctrl + Shift + P** キーを使用してコマンド パレットを開き、「**IoT プラグ アンド プレイ: モデル リポジトリにファイルを送信する**」と入力して選択します。

1. `SampleDevice.capabilitymodel.json` と `EnvironmentalSensor.interface.json` ファイルを選択します。

1. ご利用の会社モデル リポジトリ接続文字列を入力します。

    > [!NOTE]
    > 接続文字列は、初めてリポジトリに接続するときにのみ必要です。

1. VS Code の出力ウィンドウと通知では、ファイルが正常に発行されたことを確認できます。

    > [!NOTE]
    > デバイス モデル ファイルの発行に関するエラーが発生した場合は、コマンド **[IoT プラグ アンド プレイ: モデル リポジトリからサインアウトする]** を使用してサインアウトし、再度手順を行ってみてください。

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT Explorer を使用してコードを検証する

1. Azure IoT Explorer を開くと、 **[アプリの構成]** ページが表示されます。

1. ご利用の IoT Hub 接続文字列を入力し、 **[接続]** をクリックします。

1. 接続すると、デバイスの概要ページが表示されます。

1. 会社リポジトリを追加するには、 **[設定]** 、 **[+ 新規]** 、 **[会社リポジトリ]** の順に選択します。

1. ご利用の会社モデルリ ポジトリ接続文字列を追加します。 **[接続]** を選択します。

1. デバイスの概要ページで、前に作成したデバイス ID を見つけ、それを選択して詳細を表示します。

1. **urn:azureiot:EnvironmentalSensor:1** という ID を持つインターフェイスを展開して、IoT プラグ アンド プレイのプリミティブ (プロパティ、コマンド、テレメトリ) を表示します。

1. **[テレメトリ]** ページを選択して、デバイスから送信されているテレメトリ データを表示します。

1. **[プロパティ (書き込み不可)]** ページを選択して、デバイスから報告された書き込みができないプロパティを表示します。

1. **[プロパティ (書き込み可能)]** ページを選択して、更新を行うことができる書き込み可能なプロパティを表示します。

1. プロパティ**名** を展開し、新しい名前で更新して、 **[書き込み可能なプロパティの更新]** を選択します。 
2. 新しい名前が **[報告されるプロパティ]** 列に表示されるのを確認するには、ページの上部にある **[更新]** ボタンをクリックします。

1. **[コマンド]** ページを選択して、デバイスでサポートされているコマンドをすべて表示します。

1. **点滅**コマンドを展開し、新しい点滅時間間隔を設定します。 **[コマンドの送信]** を選択して、デバイス上でコマンドを呼び出します。

1. シミュレートするデバイスにアクセスして、コマンドが想定どおりに実行されたことを確認します。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、DCM を使用して IoT プラグ アンド プレイ デバイスを作成する方法について学習しました。

IoT プラグ アンド プレイの詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用してデバイス機能モデルを作成しテストする](tutorial-pnp-visual-studio-code.md)
