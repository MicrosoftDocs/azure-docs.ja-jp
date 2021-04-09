---
title: Azure IoT Edge のモジュールを開発およびデバッグする | Microsoft Docs
description: Visual Studio Code を使用して、Azure IoT Edge のモジュールを C#、Python、Node.js、Java、C で開発、ビルド、デバッグする
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: eae6f1ec8cb6917d0d51deca8c9e88725c9b01a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200632"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge のモジュールを開発およびデバッグする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

ビジネス ロジックを Azure IoT Edge のモジュールにすることができます。 この記事では、主なツールとして Visual Studio Code を使用してモジュールを開発およびデバッグする方法を説明します。

Visual Studio Code で、C#、Node.js、または Java で記述されたモジュールをデバッグする方法は 2 つあります。モジュール コンテナー内にプロセスをアタッチするか、モジュール コードをデバッグ モードで起動することができます。 Python または C で記述されたモジュールをデバッグするには、Linux amd64 コンテナー内のプロセスにアタッチする方法だけです。

Visual Studio Code のデバッグ機能をよく知らない場合は、[デバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関するページを確認してください。

この記事では、複数のアーキテクチャ用に複数の言語でモジュールを開発およびデバッグする手順について説明します。 現時点では、Visual Studio Code は、C#、C、Python、Node.js、および Java で記述されたモジュールをサポートしています。 サポートされているデバイス アーキテクチャは X64 と ARM32 です。 サポートされているオペレーティング システム、言語、およびアーキテクチャの詳細については、「[Language and architecture support (言語とアーキテクチャのサポート)](module-development.md#language-and-architecture-support)」を参照してください。

>[!NOTE]
>Linux ARM64 デバイスの開発とデバッグのサポートは、[パブリック プレ表示](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。 詳細については、「[Visual Studio Code で ARM64 IoT Edge モジュールを開発してデバッグする (プレビュー)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)」を参照してください。

## <a name="prerequisites"></a>前提条件

Windows、macOS、または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用できます。 Windows コンピューターでは、Windows または Linux のいずれかのモジュールを開発できます。 Windows モジュールを開発するには、バージョン 1809/ビルド 17763 以降を実行している Windows コンピューターを使用します。 Linux モジュールを開発するには、[Docker Desktop の要件](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)を満たす Windows コンピューターを使用します。

まず [Visual Studio Code](https://code.visualstudio.com/) をインストールしてから、次の拡張機能を追加します。

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- 開発に使用している言語固有の Visual Studio 拡張機能
  - C# (Azure Functions を含む): [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java:[Visual Studio Code 向け Java 拡張機能パック](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++ 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

また、モジュールを開発するために、言語固有のツールをいくつか追加でインストールする必要もあります。

- C# (Azure Functions を含む): [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: Python パッケージをインストールするための [Python](https://www.python.org/downloads/) と [Pip](https://pip.pypa.io/en/stable/installing/#installation) (通常は、Python のインストールに含まれています)。

- Node.js:[Node.js](https://nodejs.org)。 また、[Yeoman](https://www.npmjs.com/package/yo) と [Azure IoT Edge Node.js Module Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module) もインストールする必要があります。

- Java:[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) と [Maven](https://maven.apache.org/)。 JDK インストールを指すように [`JAVA_HOME` 環境変数を設定する](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)必要があります。

モジュール イメージを作成してデプロイするには、モジュール イメージを作成する Docker とモジュール イメージを保持するコンテナー レジストリが必要です。

- お使いの開発用マシン上の [Docker Community Edition](https://docs.docker.com/install/)。

- [Azure Container Registry](../container-registry/index.yml) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。

C でモジュールを開発している場合を除き、IoT Edge ソリューションをデバッグ、実行、テストするようにローカルの開発環境を設定するには、Python ベースの [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) も必要になります。 まだ行っていない場合は、[Python (2.7、3.6、3.7) と Pip](https://www.python.org/) をインストールした後、ターミナルで次のコマンドを実行して **iotedgehubdev** をインストールしてください。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> 現在、iotedgehubdev では、Python 3.8 と互換性のない docker-py ライブラリが使用されています。
>
> 事前インストール済みの python 2.7 など、複数の Python をお持ちの場合 (たとえば、Ubuntu や macOS で)、正しい `pip` または `pip3` を使用して **iotedgehubdev** をインストールしています

デバイス上でモジュールをテストするには、1 つ以上の IoT Edge デバイスがあるアクティブな IoT ハブが必要です。 お使いのコンピューターを IoT Edge デバイスとして使用するには、[Linux](quickstart-linux.md) または [Windows](quickstart.md) 用のクイック スタートの手順に従ってください。 開発用マシン上で IoT Edge デーモンを実行している場合は、次の手順に進む前に EdgeHub と EdgeAgent を停止することが必要になる可能性があります。

## <a name="create-a-new-solution-template"></a>新しいソリューション テンプレートを作成する

次の手順では、Visual Studio Code と Azure IoT Tools を使用して好きな開発言語 (C# で記述されている Azure Functions を含む) で IoT Edge モジュールを作成する方法を説明します。 まずはソリューションを作成し、次にそのソリューション内に最初のモジュールを生成します。 各ソリューションには複数のモジュールを含めることができます。

1. **[ビュー]**  >  **[コマンド パレット]** を選択します。

1. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

1. 新しいソリューションを作成するフォルダーを参照し、 **[フォルダーの選択]** を選択します。

1. ソリューションの名前を入力します。

1. ソリューション内の最初のモジュールになるように、好きな開発言語用のモジュール テンプレートを選択します。

1. モジュールの名前を入力します。 コンテナー レジストリ内に一意の名前を選択します。

1. モジュールのイメージ リポジトリの名前を指定します。 Visual Studio Code により、モジュール名には自動的に **localhost:5000/<対象のモジュール名\>** が設定されます。 独自のレジストリ情報に置き換えます。 テスト用に Docker のローカル レジストリを使用する場合、**localhost** で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは **_\<registry name\>_ .azurecr.io** のようになります。 この文字列の **localhost:5000** 部分だけを置き換えて、最終的な結果が **\<*registry name*\>.azurecr.io/ _\<your module name\>_** になるようにします。

   ![Docker イメージ リポジトリを指定する](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code は、指定された情報を取得し、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。

ソリューション内には 4 つの項目があります。

- **.vscode** フォルダーにはデバッグ構成が含まれています。

- **modules** フォルダーにはモジュールごとのサブフォルダーが含まれています。  各モジュールのフォルダー内には **module.json** というファイルがあります。これは、モジュールのビルドとデプロイを制御します。  ローカル ホストからリモート レジストリにモジュール デプロイメント コンテナー レジストリを変更する目的で、このファイルを修正しなければならないことがあります。 この時点ではモジュールは 1 つだけです。  ただし、コマンド パレットでコマンド **Azure IoT Edge:Add IoT Edge Module** を実行することで追加できます。

- **.env** ファイルには環境変数の一覧が表示されます。 レジストリが Azure Container Registry の場合、Azure Container Registry のユーザー名とパスワードがあります。

  > [!NOTE]
  > 環境ファイルは、モジュールのイメージ リポジトリを指定した場合にのみ作成されます。 localhost の既定値を受け入れてローカルでテストおよびデバッグする場合は、環境変数を宣言する必要はありません。

- **deployment.template.json** ファイルには新しいモジュールと、テストに使用できるデータをシミュレートする **SimulatedTemperatureSensor** のサンプル モジュールの一覧が表示されます。 配置マニフェストがどのように機能するかについて詳しくは、「[配置マニフェストを使ってモジュールをデプロイしルートを確立する](module-composition.md)」をご覧ください。

シミュレートされた気温モジュールがどのように動作するかを確認するには、[SimulatedTemperatureSensor.csproj のソース コード](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)をご覧ください。

## <a name="add-additional-modules"></a>他のモジュールを追加する

ソリューションに他のモジュールを追加するには、コマンド **[Azure IoT Edge: Add IoT Edge Module]\(Azure IoT Edge: IoT Edge モジュールを追加する\)** をコマンド パレットから実行します。 また、Visual Studio Code の [エクスプローラー] ビューで **modules** フォルダーまたは `deployment.template.json` ファイルを右クリックし、 **[IoT Edge モジュールを追加する]** を選択することもできます。

## <a name="develop-your-module"></a>モジュールの開発

ソリューションに付属する既定のモジュール コードは次の場所にあります。

- Azure Functions (C#): **modules > *&lt;対象のモジュール名&gt;*  >  *&lt;対象のモジュール名&gt;* .cs**
- C#: **modules > *&lt;対象のモジュール名&gt;* > Program.cs**
- Python: **modules > *&lt;対象のモジュール名&gt;* > main.py**
- Node.js: **modules > *&lt;対象のモジュール名&gt;* > app.js**
- Java: **modules > *&lt;対象のモジュール名&gt;* > src > main > java > com > edgemodulemodules > App.java**
- C: **modules > *&lt;対象のモジュール名&gt;* > main.c**

モジュールと deployment.template.json ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスにデプロイすることで、コードを操作することなくテストを開始できます。 モジュールは単にソース (この場合は、データをシミュレートする SimulatedTemperatureSensor モジュール) から入力を取得して IoT Hub に送信するように作成されています。

テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>コンテナーを使用せずにモジュールをデバッグする (C#、Node.js、Java)

C#、Node.js、または Java で開発している場合、モジュールでは、メッセージを開始、実行、ルーティングできるように既定のモジュール コード内で **ModuleClient** オブジェクトを使用する必要があります。 また、モジュールがメッセージを受信したときに対処するために、既定の入力チャネル **input1** も使用します。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge ソリューション用に IoT Edge シミュレーターを設定する

開発用マシン上では、IoT Edge シミュレーターを起動できます。IoT Edge セキュリティ デーモンをインストールして、IoT Edge ソリューションを実行できるようにする必要はありません。

1. 左側のデバイス エクスプローラーで、IoT Edge デバイス ID を右クリックして **[Setup IoT Edge Simulator]\(IoT Edge シミュレーターの設定\)** を選択し、デバイスの接続文字列を使用してシミュレーターを起動します。
1. 統合ターミナルで進捗の詳細を確認すると、IoT Edge シミュレーターが正常に設定されたことがわかります。

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>シングル モジュール アプリ用に IoT Edge シミュレーターを設定する

シミュレーターを設定して起動するには、コマンド **[Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module]\(Azure IoT Edge: シングル モジュール用の IoT Edge Hub シミュレーターを起動\)** を Visual Studio Code のコマンド パレットから実行します。 メッセージが表示されたら、既定のモジュール コードの値 **input1** (または使用しているコードの同等の値) をアプリケーションの入力名として使用します。 このコマンドは **iotedgehubdev** CLI をトリガーしてから、IoT Edge シミュレーターとテスト用ユーティリティ モジュール コンテナーを起動します。 シミュレーターがシングル モジュール モードで正しく起動された場合は、統合ターミナルで以下の出力を確認できます。 メッセージの送信に役立つ `curl` コマンドも表示されます。 これは後で使用します。

   ![シングル モジュール アプリ用に IoT Edge シミュレーターを設定する](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Visual Studio Code の Docker Explorer ビューを使用して、モジュールの実行状態を確認できます。

   ![シミュレーター モジュールの状態](media/how-to-develop-csharp-module/simulator-status.png)

   **edgeHubDev** コンテナーはローカルの IoT Edge シミュレーターの中心です。 これは、IoT Edge セキュリティ デーモンを使用せずに開発用マシンで実行可能であり、ネイティブ モジュール アプリまたはモジュール コンテナー用の環境設定を提供します。 **入力** コンテナーは、モジュール上の対象となる入力チャネルへのメッセージのブリッジに役立つ REST API シリーズを公開します。

### <a name="debug-module-in-launch-mode"></a>起動モードでモジュールをデバッグする

1. お使いの開発言語の要件に従ってデバッグ用の環境を準備し、モジュール内にブレークポイントを設定して、使用するデバッグ構成を選択します。
   - **C#**
     - Visual Studio Code 統合ターミナルで、ディレクトリを ***&lt;対象のモジュール名&gt;*** フォルダーに変更し、次のコマンドを実行して .NET Core アプリケーションをビルドします。

       ```cmd
       dotnet build
       ```

     - `Program.cs` ファイルを開き、ブレークポイントを追加します。

     - **[ビュー] > [デバッグ]** の順に選択して、Visual Studio Code の [デバッグ] ビューに移動します。 ドロップダウンからデバッグ構成として **[ _&lt;your module name&gt;_ Local Debug (.NET Core)]\(<モジュール名> のローカル デバッグ (.NET Core)\)** を選択します。

        > [!NOTE]
        > .NET Core `TargetFramework` が `launch.json` 内のプログラム パスと一致しない場合は、Visual Studio Code がこのプログラムを正常に起動できるように .csproj ファイル内の `TargetFramework` に合わせて `launch.json` 内のプログラム パスを手動で更新する必要があります。

   - **Node.js**
     - Visual Studio Code 統合ターミナルで、ディレクトリを ***&lt;対象のモジュール名&gt;*** フォルダーに変更し、次のコマンドを実行して Node パッケージをインストールします。

       ```cmd
       npm install
       ```

     - `app.js` ファイルを開き、ブレークポイントを追加します。

     - **[ビュー] > [デバッグ]** の順に選択して、Visual Studio Code の [デバッグ] ビューに移動します。 ドロップダウンからデバッグ構成として **[ _&lt;your module name&gt;_ Local Debug (Node.js)]\(<モジュール名> のローカル デバッグ (Node.js)\)** を選択します。
   - **Java**
     - `App.java` ファイルを開き、ブレークポイントを追加します。

     - **[ビュー] > [デバッグ]** の順に選択して、Visual Studio Code の [デバッグ] ビューに移動します。 ドロップダウンからデバッグ構成として **[ _&lt;your module name&gt;_ Local Debug (Java)]\(<モジュール名> のローカル デバッグ (Java)\)** を選択します。

1. **[デバッグの開始]** をクリックするか **F5** キーを押して、デバッグ セッションを開始します。

1. Visual Studio Code 統合ターミナルで、次のコマンドを実行して **Hello World** メッセージをモジュールに送信します。 これは、前述の手順で IoT Edge シミュレーターを設定したときに表示されたコマンドです。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows を使用している場合は、Visual Studio Code 統合ターミナルのシェルが **Git Bash** または **WSL Bash** であることを確認してください。 `curl` コマンドを PowerShell またはコマンド プロンプトから実行することはできません。
   > [!TIP]
   > `curl` の代わりに [PostMan](https://www.getpostman.com/) またはその他の API ツールを使用してメッセージを送信することもできます。

1. Visual Studio Code の [デバッグ] ビューの左側のパネルに変数が表示されます。

1. デバッグ セッションを停止するには、停止ボタンを選択するか **Shift + F5** キーを押し、コマンド パレットの **[Azure IoT Edge: Stop IoT Edge Simulator]\(Azure IoT Edge: IoT Edge シミュレーターを停止\)** を実行して、シミュレーターを停止してクリーンアップします。

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>IoT Edge シミュレーターを使用してアタッチ モードでデバッグする (C#、Node.js、Java、Azure Functions)

既定のソリューションには 2 つのモジュールが含まれています。1 つはシミュレート済みの温度センサー モジュールで、もう 1 つはパイプ モジュールです。 シミュレート済みの温度センサーがパイプ モジュールにメッセージを送信すると、そのメッセージは IoT Hub にパイプされます。 作成したモジュール フォルダーには、コンテナー タイプが異なるいくつかの Docker ファイルが含まれています。 テスト用のモジュールを作成するには、 **.debug** 拡張子で終わるファイルのいずれかを使用します。

現時点では、次のように、アタッチ モードでのデバッグのみがサポートされています。

- C# モジュール (Azure Functions のモジュールを含む) では、Linux amd64 コンテナーでのデバッグをサポートしています
- Node.js モジュールでは、Linux amd64 コンテナーと Linux arm32v7 コンテナーのほか Windows amd64 コンテナーでのデバッグをサポートしています
- Java モジュールでは、Linux amd64 コンテナーと Linux arm32v7 コンテナーでのデバッグをサポートしています

> [!TIP]
> Visual Studio Code のステータス バーの項目をクリックすると、使用している IoT Edge ソリューションの既定のプラットフォームに合わせてオプションを切り替えることができます。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge ソリューション用に IoT Edge シミュレーターを設定する

開発用マシンでは、IoT Edge ソリューションを実行できるように IoT Edge セキュリティ デーモンをインストールする代わりに IoT Edge シミュレーターを起動できます。

1. 左側のデバイス エクスプローラーで、IoT Edge デバイス ID を右クリックして **[Setup IoT Edge Simulator]\(IoT Edge シミュレーターの設定\)** を選択し、デバイスの接続文字列を使用してシミュレーターを起動します。

1. 統合ターミナルで進捗の詳細を確認すると、IoT Edge シミュレーターが正常に設定されたことがわかります。

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>デバッグ用のコンテナーをビルドして実行し、アタッチ モードでデバッグする

1. モジュール ファイル (`Program.cs`、`app.js`、`App.java`、または `<your module name>.cs`) を開いてブレークポイントを追加します。

1. Visual Studio Code の [エクスプローラー] ビューで、対象のソリューションの `deployment.debug.template.json` ファイルを右クリックし、 **[Build and Run IoT Edge solution in Simulator]\(シミュレーターでの IoT Edge ソリューションのビルドおよび実行\)** を選択します。 モジュール コンテナーのすべてのログを同じウィンドウで確認できます。 また、[Docker] ビューに移動してコンテナーの状態を確認することもできます。

   ![watch 変数](media/how-to-vs-code-develop-module/view-log.png)

1. Visual Studio Code の [デバッグ] ビューに移動し、対象のモジュール用のデバッグ構成ファイルを選択します。 デバッグ オプション名は、***&lt;対象のモジュール名&gt;* Remote Debug** のようになります。

1. **[デバッグの開始]** を選択するか、**F5** キーを押します。 アタッチするプロセスを選択します。

1. Visual Studio Code の [デバッグ] ビューの左側のパネルに変数が表示されます。

1. デバッグ セッションを停止するには、最初に停止ボタンを選択するか **Shift + F5** キーを押してから、 **[Azure IoT Edge: Stop IoT Edge Simulator]\(Azure IoT Edge: IoT Edge シミュレーターを停止\)** をコマンド パレットから選択します。

> [!NOTE]
> 前の例は、コンテナー上の IoT Edge モジュールをデバッグする方法を示しています。 これにより、公開ポートがモジュールのコンテナーの `createOptions` 設定に追加されました。 モジュールのデバッグが終了したら、運用環境対応の IoT Edge モジュールではこれらの公開ポートを削除することをお勧めします。
>
> C# (Azure Functions を含む) で記述されたモジュールの場合、この例は、コンテナー イメージの作成中に .NET Core コマンドライン デバッガー (VSDBG) を含める、デバッグ バージョンの `Dockerfile.amd64.debug` に基づいています。 C# モジュールをデバッグした後、稼働準備済みの IoT Edge モジュールでは、VSDBG が含まれていない Dockerfile を直接使用することをお勧めします。

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>IoT Edge ランタイムを使用してモジュールをデバッグする

各モジュール フォルダーには、コンテナー タイプが異なるいくつかの Docker ファイルが含まれています。 テスト用のモジュールを作成するには、 **.debug** 拡張子で終わるファイルのいずれかを使用します。

この方法を使用してモジュールをデバッグする場合、モジュールは IoT Edge ランタイム上で実行されます。 IoT Edge デバイスと Visual Studio Code は同じマシン上に配置できますが、一般的に Visual Studio Code は開発用マシン上に配置され、IoT Edge ランタイムとモジュールは別の物理マシン上で実行されます。 Visual Studio Code からデバッグを行うには、次の作業を実行する必要があります。

- IoT Edge デバイスを設定し、 **.debug** Dockerfile を使用して IoT Edge モジュールをビルドして、IoT Edge デバイスにデプロイします。
- モジュールの IP とポートを公開し、デバッガーがアタッチできるようにします。
- `launch.json` を更新し、Visual Studio Code が、リモート マシン上のコンテナー内にあるプロセスにアタッチできるようにします。 このファイルは、ワークスペース内の `.vscode` フォルダー内にあり、デバッグをサポートする新しいモジュールを追加するたびに更新されます。

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>モジュールをビルドして IoT Edge デバイスにデプロイする

1. Visual Studio Code で、`deployment.debug.template.json` ファイルを開きます。これには、適切な `createOptions` 値が設定されているモジュール イメージのデバッグ バージョンが含まれています。

1. Python でモジュールを開発している場合は、続行する前に次の手順に従います。
   - `main.py` ファイルを開き、import セクションの後に次のコードを追加します。

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - デバッグするコールバックに次の 1 行のコードを追加します。

      ```python
      ptvsd.break_into_debugger()
      ```

     たとえば、`receive_message_listener` 関数をデバッグする場合は、以下に示すようなコード行を挿入します。

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. Visual Studio Code のコマンド パレットで:
   1. **Azure IoT Edge: Build IoT Edge solution** コマンドを入力して実行します。

   1. 対象のソリューションの `deployment.debug.template.json` ファイルを選択します。

1. Visual Studio Code の [エクスプローラー] ビューの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションで:
   1. IoT Edge デバイス ID を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

      > [!TIP]
      > 選択したデバイスが IoT Edge デバイスであることを確認するには、そのデバイスを選択してモジュールの一覧を展開し、 **[$edgeHub]** と **[$edgeAgent]** が存在することを確認します。 すべての IoT Edge デバイスには、これらの 2 つのモジュールが含まれています。

   1. ソリューションの **config** フォルダーに移動し、`deployment.debug.amd64.json` ファイルを選択して、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** を選択します。

統合ターミナルで、デプロイが正常に作成されていることをデプロイ ID によって確認できます。

端末で `docker ps` コマンドを実行して、コンテナーの状態を確認できます。 Visual Studio Code と IoT Edge ランタイムが同じマシン上で実行されている場合は、Visual Studio Code の Docker ビューでも状態を確認することができます。

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>モジュールの IP とポートをデバッガー用に公開する

モジュールを実行しているマシンが Visual Studio Code と同じ場合は、このセクションをスキップできます。これは、コンテナーへのアタッチに localhost が使用されており、 **.debug** Dockerfile、モジュールのコンテナーの `createOptions` 設定、および `launch.json` ファイル内で、ポートが既に正しく設定されているためです。 モジュールと Visual Studio Code を別々のマシン上で実行している場合は、お使いの開発言語用の手順に従ってください。

- **C# (Azure Functions を含む)**

  [開発用マシンと IoT Edge デバイス上で SSH チャネルを構成し](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes)、アタッチする `launch.json` ファイルを編集します。

- **Node.js**

  - デバッグ対象のマシン上のモジュールが実行されており、デバッガーがアタッチする準備が整えられていること、およびポート 9229 が外部からアクセス可能であることを確認します。 これを確認するには、デバッガー マシン上で `http://<target-machine-IP>:9229/json` を開きます。 この URL に、デバッグ対象の Node.js モジュールに関する情報が表示されます。
  
  - 開発用マシン上で Visual Studio Code を開き、`launch.json` を編集して、***&lt;モジュール名&gt;* Remote Debug (Node.js)** プロファイル (モジュールが Windows コンテナーとして実行されている場合は、**_&lt;モジュール名&gt;_ Remote Debug (Windows コンテナー内の Node.js)** プロファイル) のアドレス値が、デバッグ対象のマシンの IP になるようにします。

- **Java**

  - `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` を実行し、デバッグ対象マシンへの SSH トンネルを構築します。
  
  - 開発用マシン上で Visual Studio Code を開き、`launch.json` 内の ***&lt;モジュール名&gt;* Remote Debug (Java)** プロファイルを編集し、ターゲット マシンにアタッチできるようにします。 Visual Studio Code を使用して `launch.json` を編集したり、Java をデバッグしたりする方法の詳細については、[デバッガーの構成](https://code.visualstudio.com/docs/java/java-debugging#_configuration)の該当するセクションをご覧ください。

- **Python**

  - デバッグ対象マシン上のポート 5678 が開いており、アクセスできることを確認します。

  - 前に `main.py` 内に挿入したコード `ptvsd.enable_attach(('0.0.0.0', 5678))` 内で、**0.0.0.0** をデバッグ対象マシンの IP アドレスに変更します。 IoT Edge モジュールをもう一度ビルドし、プッシュしてデプロイします。

  - 開発用マシン上で Visual Studio Code を開き、`launch.json` を編集して、***&lt;モジュール名&gt;* Remote Debug (Python)** プロファイルの `host` 値に、`localhost` ではなく、ターゲット マシンの IP アドレスが使用されるようにします。

### <a name="debug-your-module"></a>モジュールをデバッグする

1. Visual Studio Code の [デバッグ] ビューで、対象のモジュール用のデバッグ構成ファイルを選択します。 デバッグ オプション名は、***&lt;対象のモジュール名&gt;* Remote Debug** のようになります。

1. お使いの開発言語のモジュール ファイルを開いて、ブレークポイントを追加します。

   - **Azure 関数 (C#)** :ファイル `<your module name>.cs` にブレークポイントを追加します。
   - **C#** : ファイル `Program.cs` にブレークポイントを追加します。
   - **Node.js**:ファイル `app.js` にブレークポイントを追加します。
   - **Java**: ファイル `App.java` にブレークポイントを追加します。
   - **Python**: `main.py` ファイルのコールバック メソッド内の `ptvsd.break_into_debugger()` 行を追加した場所にブレークポイントを追加します。
   - **C**: ファイル `main.c` にブレークポイントを追加します。

1. **[デバッグの開始]** を選択するか、**F5** を押します。 アタッチするプロセスを選択します。

1. Visual Studio Code の [デバッグ] ビューの左側のパネルに変数が表示されます。

> [!NOTE]
> 前の例は、コンテナー上の IoT Edge モジュールをデバッグする方法を示しています。 これにより、公開ポートがモジュールのコンテナーの `createOptions` 設定に追加されました。 モジュールのデバッグが終了したら、運用環境対応の IoT Edge モジュールではこれらの公開ポートを削除することをお勧めします。

## <a name="build-and-debug-a-module-remotely"></a>モジュールをリモートでビルドしてデバッグする

最近行われた Docker エンジンと Moby エンジンの両方での SSH 接続をサポートするための変更と、Visual Studio Code コマンド パレットと Azure IoT Edge ターミナルへの環境設定のインジェクションを可能にする Azure IoT Tools の新しい設定によって、リモート デバイス上でモジュールのビルドとデバッグを実行できるようになりました。

詳細情報と手順については、こちらの [IoT 開発者のブログ記事](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)を参照してください。

## <a name="next-steps"></a>次のステップ

モジュールをビルドしたら、[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)方法を確認してください。

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。