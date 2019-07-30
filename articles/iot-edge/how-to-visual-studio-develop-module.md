---
title: Visual Studio でモジュールを開発してデバッグする - Azure IoT Edge | Microsoft Docs
description: Visual Studio 2019 を使用して Azure IoT Edge 用のモジュールを開発してデバッグする
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/01/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 953a549a44d224f9d196c6d6842bdcae04cca975
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485912"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio 2019 を使用して Azure IoT Edge 用のモジュールを開発してデバッグする

ビジネス ロジックを Azure IoT Edge のモジュールにすることができます。 この記事では、主なツールとして Visual Studio 2019 を使用してモジュールを開発してデバッグする方法を示します。

Azure IoT Edge Tools for Visual Studio の利点は次のとおりです。

- ローカル開発用コンピューターで Azure IoT Edge ソリューションとモジュールの作成、編集、ビルド、実行、デバッグを行うことができます。
- Azure IoT Hub を使用して Azure IoT Edge ソリューションを Azure IoT Edge デバイスにデプロイできます。
- Visual Studio での開発のすべての利点を利用しながら、C または C# で Azure IoT モジュールをコーディングできます。
- Azure IoT Edge デバイスとモジュールを UI で管理できます。

この記事では、Azure IoT Edge Tools for Visual Studio 2019 を使用して、IoT Edge モジュールを開発する方法を示します。 Azure IoT Edge デバイスにプロジェクトをデプロイする方法についても説明します。

> [!TIP]
> Visual Studio によって作成された IoT Edge プロジェクト構造は、Visual Studio Code と同じではありません。
  
## <a name="prerequisites"></a>前提条件

この記事では、Windows を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 IoT Edge デバイスとして、別の物理デバイスを指定できます。

この記事ではメインの開発ツールとして Visual Studio 2019 を使うため、Visual Studio をインストールします。 Visual Studio 2019 のインストールには、必ず **Azure の開発**と **C++ によるデスクトップ開発**のワークロードを含めてください。 [Visual Studio 2019 を変更](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019)して、必須のワークロードを追加することができます。

Visual Studio 2019 の準備ができたら、次のツールとコンポーネントも必要になります。

- Visual Studio 2019 で IoT Edge プロジェクトを作成するために、Visual Studio Marketplace から [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) をダウンロードしてインストールします。

> [!TIP]
> Visual Studio 2017 を使用している場合は、Visual Studio Marketplace から VS 2017 用の [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) をダウンロードしてインストールしてください。

- モジュール イメージを作成して実行するために、開発用マシンに [Docker Community Edition](https://docs.docker.com/install/) をダウンロードしてインストールします。 Linux コンテナー モードまたは Windows コンテナー モードで実行するように Docker CE を設定する必要があります。

- [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) をインストールして、IoT Edge ソリューションをデバッグ、実行、テストするようにローカルの開発環境を設定します。 [Python (2.7/3.6) と Pip](https://www.python.org/) をインストールした後、ターミナルで次のコマンドを実行して **iotedgehubdev** パッケージをインストールします。 Azure IoT EdgeHub Dev Tool のバージョンが 0.3.0 より大きいことを確認します。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- リポジトリを複製して Vcpkg ライブラリ マネージャをインストールしてから、Windows 用の **azure-iot-sdk-c パッケージ**をインストールします。

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。

  > [!TIP]
  > プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。

- デバイス上でモジュールをテストするには、1 つ以上の IoT Edge デバイスがあるアクティブな IoT ハブが必要です。 お使いのコンピューターを IoT Edge デバイスとして使用するには、[Linux](quickstart-linux.md) または [Windows](quickstart.md) 用のクイック スタートの手順に従ってください。 開発用マシン上で IoT Edge デーモンを実行している場合は、Visual Studio で開発を始める前に、EdgeHub と EdgeAgent を停止することが必要になる可能性があります。

### <a name="check-your-tools-version"></a>ツールのバージョンを確認する

1. **[ツール]** メニューの **[拡張機能と更新プログラム]** を選択します。 **[インストール済み] > [ツール]** の順に展開すると、 **[Azure IoT Edge Tools]** と **[Cloud Explorer for Visual Studio]** が見つかります。

1. インストールされているバージョンを確認します。 このバージョンと、Visual Studio Marketplace での最新バージョンを比較できます ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. インストールされているバージョンが Visual Studio Marketplace で入手できるものより古い場合は、次のセクションの説明に従って Visual Studio でツールを更新します。

### <a name="update-your-tools"></a>ツールを更新する

1. **[拡張機能と更新プログラム]** ダイアログで、 **[更新プログラム] > [Visual Studio Marketplace]** の順に展開し、 **[Azure IoT Edge Tools]** または **[Cloud Explorer for Visual Studio]** を選択して、 **[更新]** を選択します。

1. ツールの更新プログラムをダウンロードしたら、Visual Studio を終了し、VSIX インストーラーを使用してツールの更新プログラムを起動します。

1. インストーラーで **[OK]** を選択して開始し、 **[変更]** を選択してツールを更新します。

1. 更新が完了したら、 **[閉じる]** を選択して Visual Studio を再起動します。

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge プロジェクトを作成する

Visual Studio の Azure IoT Edge プロジェクト テンプレートでは、Azure IoT Hub の Azure IoT Edge デバイスにデプロイできるプロジェクトが作成されます。 最初に Azure IoT Edge ソリューションを作成した後、そのソリューションで最初のモジュールを生成します。 各 IoT Edge ソリューションには、複数のモジュールを含めることができます。

1. Visual Studio の新しいプロジェクト ダイアログで、 **[Azure IoT Edge]** プロジェクトを検索して選択し、 **[次へ]** をクリックします。 プロジェクトの構成ウィンドウで、プロジェクトの名前を入力し、場所を指定して、 **[作成]** を選択します。 既定のプロジェクト名は **AzureIoTEdgeApp1** です。

   ![新しいプロジェクトの作成](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. **[IoT Edge アプリケーションとモジュールの追加]\(Add IoT Edge Application and Module\)** ウィンドウで、 **[C# Module]\(C# モジュール\)** または **[C Module]\(C モジュール\)** を選択し、モジュール名とモジュール イメージ リポジトリを指定します。 Visual Studio により、モジュール名には自動的に **localhost:5000/<対象のモジュール名\>** が設定されます。 独自のレジストリ情報に置き換えます。 テスト用に Docker のローカル レジストリを使用する場合、**localhost** で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは * **\<レジストリ名\>*.azurecr.io** のようになります。 この文字列の **localhost:5000** 部分だけを置き換えて、最終的な結果が * *\<* レジストリ名 *\>.azurecr.io/\<* 対象のモジュール名*\>** になるようにします。 既定のモジュール名は **IoTEdgeModule1** です。

   ![アプリケーションとモジュールの追加](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. **[OK]** を選択して、C# または C を使用したモジュールを含む Azure IoT Edge ソリューションを作成します。

これで、**AzureIoTEdgeApp1.Linux.Amd64** プロジェクトまたは **AzureIoTEdgeApp1.Windows.Amd64** プロジェクト、および **IoTEdgeModule1** プロジェクトもソリューション内に作成されました。 各 **AzureIoTEdgeApp1** プロジェクトには `deployment.template.json` ファイルが存在します。このファイルでは、IoT Edge ソリューション用にビルドしてデプロイするモジュールのほか、モジュール間のルートも定義されています。 既定のソリューションには、**tempSensor** モジュールと **IoTEdgeModule1** モジュールが含まれます。 **tempSensor** モジュールでは、**IoTEdgeModule1** モジュールに対するシミュレートされたデータが生成されるのに対し、**IoTEdgeModule1** モジュールの既定のコードでは、受信したメッセージが Azure IoT Hub に直接パイプされます。

C# モジュールの場合、**IoTEdgeModule1** プロジェクトは .NET Core 2.1 のコンソール アプリケーションです。 IoT Edge デバイスが Windows コンテナーまたは Linux コンテナーで実行されるために必要な Docker ファイルが含まれています。 `module.json` ファイルでは、モジュールのメタデータが記述されています。 依存関係として Azure IoT Device SDK を取得する実際のモジュール コードは、`Program.cs` または `main.c` ファイル内に含まれています。

## <a name="develop-your-module"></a>モジュールの開発

ソリューションに付属する既定のモジュール コードは、**IoTEdgeModule1** > **Program.cs** (C# の場合) または **main.c** (C の場合) にあります。 モジュールと `deployment.template.json` ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスにデプロイすることで、コードを操作することなくテストを開始できます。 モジュールはソース (この場合は、データをシミュレートする **tempSensor** モジュール) から入力を取得して IoT Hub に送信するように作成されています。

モジュール テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>IoT Edge デバイス接続文字列を使用して iotedgehubdev を初期化する

1. 任意の IoT Edge デバイスの接続文字列を Visual Studio Cloud Explorer の **[プライマリ接続文字列]** からコピーします。 Edge 以外のデバイスの接続文字列はコピーしないようにしてください。IoT Edge デバイスのアイコンは Edge 以外のデバイスのアイコンとは異なります。

   ![Edge デバイスの接続文字列をコピーする](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. **[ツール]**  >  **[Azure IoT Edge Tools]**  >  **[Setup IoT Edge Simulator]\(IoT Edge シミュレーターのセットアップ\)** の順に移動し、接続文字列を貼り付けて **[OK]** をクリックします。

   ![[Set Edge Connection String]\(Edge の接続文字列の設定\) 画面を開く](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 最初の手順の接続文字列を入力し、 **[OK]** を選択します。

> [!NOTE]
> 結果は後続の Azure IoT Edge ソリューションすべてに自動的に適用されるため、お使いの開発用コンピューターではこの手順に一度だけ従う必要があります。 この手順にもう一度従うことができるのは、別の接続文字列への変更が必要になった場合のみです。

## <a name="build-and-debug-single-module"></a>1 つのモジュールをビルドしてデバッグする

通常は、複数のモジュールを含むソリューション全体の中で実行する前に、各モジュールをテストしてデバッグします。

1. **IoTEdgeModule1** を右クリックし、コンテキスト メニューから **[スタートアップ プロジェクトに設定]** を選択します。

   ![スタートアップ プロジェクトを設定する](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. **F5** キーを押すか下のボタンをクリックして、モジュールを実行します。初めて実行する場合は、10 秒から 20 秒かかることがあります。

   ![モジュールを実行する](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. モジュールが正常に初期化された場合は .Net Core コンソール アプリが起動したことが示されます。

   ![実行されているモジュール](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. C# で開発している場合、**Program.cs** 内で `PipeMessage()` 関数にブレークポイントを設定します。C を使用している場合、**main.c** 内で `InputQueue1Callback()` 関数にブレークポイントを設定します。 その後、**Git Bash** または **WSL Bash** シェルで次のコマンドを実行してメッセージを送信することで、それをテストします。 (`curl` コマンドを PowerShell またはコマンド プロンプトから実行することはできません)。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![1 つのモジュールをデバッグする](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    ブレークポイントがトリガーされるはずです。 Visual Studio の **[ローカル]** ウィンドウで変数を確認できます。

   > [!TIP]
   > `curl` の代わりに [PostMan](https://www.getpostman.com/) またはその他の API ツールを使用してメッセージを送信することもできます。

1. デバッグを停止するには、**Ctrl + F5** キーを押すか、停止ボタンをクリックします。

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>複数のモジュールで IoT Edge ソリューションをビルドしてデバッグする

1 つのモジュールの開発が完了したら、複数のモジュールを含むソリューション全体を実行してデバッグします。

1. **AzureIoTEdgeApp1** を右クリックし、 **[追加]**  >  **[New IoT Edge Module]\(新しい IoT Edge モジュール\)** の順に選択して、ソリューションに 2 つ目のモジュールを追加します。 2 つ目のモジュールの既定の名前は **IoTEdgeModule2** で、別のパイプ モジュールとして機能します。

1. `deployment.template.json` ファイルを開くと、**IoTEdgeModule2** が **modules** セクションに追加されていることがわかります。 **routes** セクションを次の内容に置き換えます。 モジュール名をカスタマイズしている場合は、これらの名前を一致するように必ず更新してください。

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. **AzureIoTEdgeApp1** を右クリックし、コンテキスト メニューから **[スタートアップ プロジェクトに設定]** を選択します。

1. ブレークポイントを作成した後、**F5** キーを押して、複数のモジュールを同時に実行してデバッグします。 複数の .Net Core コンソール アプリ ウィンドウが表示されます。各ウィンドウは異なるモジュールを表します。

   ![複数のモジュールをデバッグする](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. **Ctrl + F5** キーを押すか停止ボタンをクリックして、デバッグを停止します。

## <a name="build-and-push-images"></a>イメージをビルドしてプッシュする

1. **AzureIoTEdgeApp1** がスタートアップ プロジェクトであることを確認します。 モジュール イメージに対して作成する構成として **[デバッグ]** または **[リリース]** を選択します。

    > [!NOTE]
    > **[デバッグ]** を選択すると、Visual Studio では、Docker イメージのビルドに `Dockerfile.(amd64|windows-amd64).debug` が使用されます。 これにより、コンテナー イメージのビルド中に .NET Core コマンド ライン デバッガー VSDBG が組み込まれます。 運用環境対応の IoT Edge モジュールの場合は、 **[リリース]** 構成を使用することをお勧めします。これには、VSDBG が含まれていない `Dockerfile.(amd64|windows-amd64)` が使用されます。

1. Azure Container Registry のようなプライベート レジストリを使用している場合は、次の Docker コマンドを使用してサインインします。 ローカル レジストリを使用している場合は、[ローカル レジストリを実行する](https://docs.docker.com/registry/deploying/#run-a-local-registry)ことができます。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Azure Container Registry のようなプライベート レジストリを使用している場合は、`deployment.template.json` ファイルにあるランタイム設定にレジストリのログイン情報を追加する必要があります。 プレースホルダーを実際の ACR 管理者ユーザー名、パスワード、レジストリ名に置き換えてください。

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. **AzureIoTEdgeApp1** を右クリックし、 **[Build and Push Edge Solution]\(Edge ソリューションのビルドとプッシュ\)** を選択して、各モジュールの Docker イメージをビルドしてプッシュします。

   ![イメージをビルドしてプッシュする](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

IoT Edge デバイスの設定に使用したクイック スタートの記事では、Azure portal を使用してモジュールをデプロイしました。 Visual Studio の Cloud Explorer を使用してモジュールをデプロイすることもできます。 実際のシナリオに合わせて既に配置マニフェスト (`deployment.json` ファイル) の準備が完了しているため、デプロイを受け取るデバイスを選択するだけで済みます。

1. **[表示]**  >  **[Cloud Explorer]** をクリックして、**Cloud Explorer** を開きます。 Visual Studio 2019 にログインしていることを確認します。

1. **Cloud Explorer** でサブスクリプションを展開し、Azure IoT Hub とデプロイする Azure IoT Edge デバイスを探します。

1. IoT Edge デバイスを右クリックしてデプロイを作成します。`$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` にある配置マニフェスト ファイルを選択する必要があります。

   > [!NOTE]
   > `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json` は選択しないでください

1. 更新ボタンをクリックすると、新しいモジュールが、**TempSensor** モジュールのほか **$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。

## <a name="view-generated-data"></a>生成されたデータを表示する

1. 特定のデバイスの D2C のメッセージを監視するには、一覧でそのデバイスを選択し、 **[アクション]** ウィンドウで **[Start Monitoring Built-in Event Endpoint]\(組み込みイベントのエンドポイントの監視を開始\)** をクリックします。

1. データの監視を停止するには、一覧でデバイスを選択し、 **[アクション]** ウィンドウで **[Stop Monitoring Built-in Event Endpoint]\(組み込みイベントのエンドポイントの監視を停止\)** を選択します。

## <a name="next-steps"></a>次の手順

お使いの IoT Edge デバイス用にカスタム モジュールを開発するには、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」を参照してください。
