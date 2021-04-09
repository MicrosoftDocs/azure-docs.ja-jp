---
title: チュートリアル - Azure IoT Edge を使用して Windows デバイス用のモジュールを開発する
description: このチュートリアルでは、Windows コンテナーを使用して Windows デバイス用の IoT Edge モジュールを開発するための、開発マシンとクラウド リソースの設定について説明します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b5ff515f5a6d25285009f6579570aa3afa5a711f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463359"
---
# <a name="tutorial-develop-iot-edge-modules-using-windows-containers"></a>チュートリアル: Windows コンテナーを使用して IoT Edge モジュールを開発する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Visual Studio を使用して、コードを開発し、IoT Edge を実行している Windows デバイスにデプロイします。

>[!NOTE]
>IoT Edge 1.1 LTS は、Windows コンテナーをサポートする最後のリリース チャネルです。 バージョン 1.2 以降では、Windows コンテナーはサポートされません。 Windows デバイスで IoT Edge を実行するには、[IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用またはこちらへの移行を検討してください。

このチュートリアルでは、独自のコードを開発して IoT Edge デバイスにデプロイするために必要なことを順を追って説明します。 このチュートリアルは、特定のプログラミング言語や Azure サービスをより詳細に説明する他のチュートリアルにとって有用な前提条件です。

このチュートリアルでは、**C# モジュールの Windows デバイスへの** デプロイ例を使用します。 この例が選択された理由は、最も一般的な開発シナリオだからです。 異なる言語での開発に関心がある場合や、Azure サービスをモジュールとしてデプロイする予定の場合でも、このチュートリアルは開発ツールの学習に役立ちます。 開発の概念を理解したら、使用する言語や Azure サービスを選択して、詳細に進むことができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 開発マシンを設定する。
> * Visual Studio 用の IoT Edge Tools を使用して、新しいプロジェクトを作成する。
> * プロジェクトをコンテナーとしてビルドして、Azure Container Registry に格納する。
> * コードを IoT Edge デバイスにデプロイする。

## <a name="prerequisites"></a>前提条件

開発マシン:

* 1809 以降の更新プログラムが適用された Windows 10。
* 開発設定に応じて、独自のコンピューターまたは仮想マシンを使用できます。
  * 開発用マシンで、入れ子になった仮想化がサポートされていることを確認します。 この機能は、次のセクションでインストールするコンテナー エンジンを実行するために必要です。
* [Git](https://git-scm.com/) のインストール。

Window 上の Azure IoT Edge デバイス:

* [Windows コンテナーを使用して Azure IoT Edge をインストールし、管理します](how-to-install-iot-edge-windows-on-windows.md)。
* 可能であれば、開発マシンでは IoT Edge を実行せず、代わりに別のデバイスを使用することをお勧めします。 開発マシンと IoT Edge デバイスのこの区別は、実際のデプロイ シナリオをより正確に反映し、異なる概念を区別するのに役立ちます。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>主要な概念

このチュートリアルでは、IoT Edge モジュールの開発について順を追って説明します。 *IoT Edge モジュール* (略して、単に *モジュール* と呼ばれることもある) は、実行可能コードを含むコンテナーです。 IoT Edge デバイスには 1 つ以上のモジュールをデプロイできます。 モジュールは、センサーからのデータの取り込み、データ分析操作やデータ クリーニング操作の実行、または IoT ハブへのメッセージの送信のような特定のタスクを実行します。 詳細については、「[Azure IoT Edge モジュールについて](iot-edge-modules.md)」を参照してください。

IoT Edge モジュールを開発する場合は、開発マシンと、モジュールが最終的にデプロイされるターゲット IoT Edge デバイスの違いを理解することが重要です。 モジュール コードを保持するためにビルドするコンテナーは、*ターゲット デバイス* のオペレーティング システム (OS) と一致している必要があります。 Windows コンテナーの開発では、Windows コンテナーは Windows オペレーティング システムでのみ実行されるため、この概念はよりシンプルです。 ただし、たとえば Windows 開発マシンを使って、Linux IoT Edge デバイス用のモジュールをビルドできます。 そのシナリオでは、お使いの開発マシンが Linux コンテナーを実行していることを確認する必要があります。 このチュートリアルを進めていくときには、*開発マシンの OS* と *コンテナーの OS* の違いに留意してください。

このチュートリアルでは、IoT Edge を実行している Windows デバイスをターゲットとしています。 Windows IoT Edge デバイスは Windows コンテナーを使用します。 Windows デバイス用の開発には Visual Studio を使用することをお勧めします。そのため、このチュートリアルでもそれを使用します。 Visual Studio Code も使用できますが、この 2 つのツールにはサポートに違いがあります。

次の表に、Visual Studio Code と Visual Studio で **Windows コンテナー** に対してサポートされる開発シナリオを示します。

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure サービス** | Azure Functions <br> Azure Stream Analytics |   |
| **Languages** | C# (デバッグはサポートされていません) | C <br> C# |
| **詳細情報** | [Visual Studio Code 用の Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Visual Studio 2017 用の Azure IoT Edge ツール](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Visual Studio 2019 用の Azure IoT Edge ツール](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="install-container-engine"></a>コンテナー エンジンをインストールする

IoT Edge モジュールはコンテナーとしてパッケージされるので、それらのコンテナーをビルドおよび管理するためには、開発マシン上にコンテナー エンジンが必要です。 Docker Desktop には多くの機能があり、コンテナー エンジンとして人気があるため、開発にはこれを使用することをお勧めします。 Windows コンピューターで Docker Desktop を使用すると、Linux コンテナーと Windows コンテナーを切り替えて、さまざまな種類の IoT Edge デバイス用のモジュールを容易に開発することができます。

次の Docker のドキュメントを使用して、ご使用の開発マシンにインストールします。

* [Install Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * Docker Desktop for Windows をインストールするときに、Linux コンテナーを使用するか Windows コンテナーを使用するかを尋ねられます。 このチュートリアルでは **Windows コンテナー** を使用します。 詳細については、「[Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)」を参照してください。

## <a name="set-up-visual-studio-and-tools"></a>Visual Studio とツールを設定する

Visual Studio Code の IoT 拡張機能は IoT Edge モジュールの開発に役立ちます。 これらの拡張機能は、プロジェクト テンプレートを提供し、配置マニフェストの作成を自動化し、IoT Edge デバイスの監視および管理を可能にします。 このセクションでは、Visual Studio と IoT Edge 拡張機能をインストールし、次に Visual Studio 内から IoT Hub のリソースを管理する Azure アカウントを設定します。

このチュートリアルでは、Visual Studio 2019 の開発手順を説明します。 Visual Studio 2017 (バージョン 15.7 以降) を使用している場合、手順は似ています。 Visual Studio Code を使用する場合は、「[Visual Studio Code を使用して Azure IoT Edge のモジュールを開発およびデバッグする](how-to-vs-code-develop-module.md)」に記載されている手順を参照してください。

1. 開発用マシンで Visual Studio 2019 を準備します。

   * お使いの開発マシンに Visual Studio がまだインストールされていない場合は、次のワークロードとともに [Visual Studio 2019 のインストール](/visualstudio/install/install-visual-studio)を行ってください。

      * Azure の開発
      * C++ によるデスクトップ開発
      * .NET Core クロスプラットフォームの開発

   * 開発用マシンに既に Visual Studio 2019 がインストールされている場合は、[Visual Studio の変更](/visualstudio/install/modify-visual-studio)の手順に従って必要なワークロードを追加します。

2. Visual Studio 2019 用の [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 拡張機能をダウンロードしてインストールします。

   Visual Studio 2017 (バージョン 15.7 以降) を使用している場合は、[Visual Studio 2017 用の Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) をダウンロードしてインストールします。

3. インストールが完了したら、Visual Studio 2019 を開き、 **[コードなしで続行]** を選択します。

4. **[表示]**  >  **[Cloud Explorer]** を選択します。

5. まだサインインしていない場合は、Cloud Explorer でプロフィール アイコンを選択し、Azure アカウントにサインインします。

6. サインインすると、Azure サブスクリプションが一覧表示されます。 IoT ハブが含まれているサブスクリプションを展開します。

7. サブスクリプションの **[IoT Hub]** 、お使いの IoT ハブの順に展開します。 IoT デバイスの一覧が表示されます。このエクスプローラーを使用してそれらを管理できます。

   ![Cloud Explorer で IoT Hub のリソースにアクセスする](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>新しいモジュール プロジェクトを作成する

Azure IoT Edge Tools の拡張機能により、Visual Studio でサポートされているすべての IoT Edge モジュール言語のプロジェクト テンプレートが提供されます。 これらのテンプレートは、作業モジュールをデプロイして IoT Edge をテストするために必要なすべてのファイルとコードを含んでいます。または、独自のビジネス ロジックを使用してテンプレートをカスタマイズするための開始点を提供します。

1. **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択します。

2. 新しいプロジェクトのウィンドウで、 **[IoT Edge]** を検索し、 **[Azure IoT Edge (Windows amd64)]** プロジェクトを選択します。 **[次へ]** をクリックします。

   ![新しい Azure IoT Edge プロジェクトを作成する](./media/tutorial-develop-for-windows/new-project.png)

3. [新しいプロジェクトの構成] ウィンドウで、プロジェクトとソリューションを、**CSharpTutorialApp** のようなわかりやすい名前に変更します。 **[作成]** をクリックしてプロジェクトを作成します。

   ![新しい Azure IoT Edge プロジェクトを構成する](./media/tutorial-develop-for-windows/configure-project.png)

4. [モジュールの追加] ウィンドウで、以下の値を使用してプロジェクトを構成します。

   | フィールド | Value |
   | ----- | ----- |
   | Visual Studio テンプレート | **[C# モジュール]** を選択します。 |
   | モジュール名 | 既定の **IotEdgeModule1** をそのまま使用します。 |
   | リポジトリの URL | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、モジュール プロジェクト名の値から事前に入力されています。 **localhost:5000** を、Azure Container Registry の **ログイン サーバー** の値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーの値を取得できます。 <br><br> 最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/iotedgemodule1 のようになります。 |

      ![ターゲット デバイス、モジュールの種類、コンテナー レジストリ用にプロジェクトを構成する](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. **[追加]** を選択し、モジュールを作成します。

新しいプロジェクトが Visual Studio ウィンドウに読み込まれたら、少し時間を取って、作成されたファイルをよく確認してください。

* **CSharpTutorialApp** という IoT Edge プロジェクト。
  * **Modules** フォルダーには、プロジェクトに含まれるモジュールへのポインターが含まれています。 この例では、IotEdgeModule1 だけです。
  * 非表示の **.env** ファイルは、コンテナー レジストリへの資格情報を保持しています。 これらの資格情報は IoT Edge デバイスと共有されており、コンテナー イメージをプルするためにアクセスすることができます。
  * **deployment.template.json** ファイルは、配置マニフェストの作成に役立つテンプレートです。 *配置マニフェスト* は、どのモジュールをデバイスにデプロイするか、それらをどのように構成するか、そしてそれらが互いに、およびクラウドとどのように通信するかを正確に定義するファイルです。
    > [!TIP]
    > レジストリ資格情報セクションで、アドレスは、ソリューションを作成したときに指定した情報から自動的に入力されています。 ただし、ユーザー名とパスワードは、.env ファイルに格納されている変数を参照します。 これはセキュリティのためです .env ファイルは GIT Ignore ですが、デプロイ テンプレートはそうではないからです。
* **IotEdgeModule1** という IoT Edge モジュール プロジェクト。
  * **program.cs** ファイルには、プロジェクト テンプレートに付属する既定の C# モジュール コードが含まれています。 既定のモジュールは、ソースから入力を受け取り、それを IoT Hub に渡します。
  * **module.json** ファイルには、完全なイメージ リポジトリ、イメージ バージョン、サポートされているプラットフォームごとに使用する Dockerfile など、モジュールに関する詳細情報が含まれています。

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>レジストリの資格情報を IoT Edge エージェントに提供する

IoT Edge ランタイムでは、コンテナー イメージを IoT Edge デバイスにプルするためにレジストリ資格情報が必要です。 IoT Edge 拡張機能は、Azure からコンテナー レジストリの情報をプルし、それをデプロイ テンプレートに取り込もうと試みます。

1. モジュール ソリューション内の **deployment.template.json** ファイルを開きます。

1. $edgeAgent の必要なプロパティで、**registryCredentials** プロパティを見つけます。 プロジェクトの作成時に指定した情報からレジストリ アドレスが自動的に設定され、ユーザー名とパスワードのフィールドには変数名が含まれているはずです。 次に例を示します。

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. モジュール ソリューション内の **.env** ファイルを開きます。 (既定では、このファイルがソリューション エクスプローラーで非表示になっているため、表示するためには、 **[Show All Files]** ボタンを選択しなければならない場合があります。)

1. Azure コンテナー レジストリからコピーした **Username** と **Password** の値を追加します。

1. 変更内容を .env ファイルに保存します。

### <a name="review-the-sample-code"></a>サンプル コードを確認する

作成したソリューション テンプレートには、IoT Edge モジュールのサンプル コードが含まれています。 このサンプル モジュールは、単にメッセージを受け取って渡すだけです。 パイプライン機能は、モジュールがどのようにして相互に通信を行うかという、IoT Edge での重要な概念を示します。

各モジュールは、コードで宣言された複数の *入力* キューと *出力* キューを持つことができます。 デバイスで実行されている IoT Edge ハブは、1 つのモジュールの出力から、1 つ以上のモジュールの入力にメッセージをルーティングします。 入力と出力を宣言するための特定のコードは、言語によって異なりますが、その概念はすべてのモジュールで同じです。 モジュール間のルーティングの詳細については、[ルートの宣言](module-composition.md#declare-routes)に関する記事を参照してください。

プロジェクト テンプレートに含まれるサンプル C# コードには、.NET 用 IoT Hub SDK の [ModuleClient クラス](/dotnet/api/microsoft.azure.devices.client.moduleclient)が使用されています。

1. **program.cs** ファイルで、**SetInputMessageHandlerAsync** メソッドを見つけます。

2. [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) メソッドでは、受信メッセージを受け取る入力キューが設定されます。 このメソッドを確認し、**input1** という入力キューがどのように初期化されるかを確かめます。

   ![SetInputMessageHandlserAsync コンストラクターで入力名を見つける](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. 次に、**SendEventAsync** メソッドを見つけます。

4. [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) メソッドでは、受け取ったメッセージが処理され、それらを渡すための出力キューが設定されます。 このメソッドを確認し、**output1** という出力キューが初期化されることを確かめます。

   ![SendEventAsync コンストラクターで出力名を見つける](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. **deployment.template.json** ファイルを開きます。

6. $edgeAgent の必要なプロパティの **modules** プロパティを見つけます。

   ここには 2 つのモジュールがリストされているはずです。 1 つは **SimulatedTemperatureSensor** で、これは既定ですべてのテンプレートに含まれており、モジュールをテストするために使用できるシミュレートされた温度データを提供します。 もう 1 つは、このプロジェクトの一部として作成した **IotEdgeModule1** モジュールです。

   このモジュールのプロパティでは、デバイスへのデプロイにどのモジュールを含めるかを宣言します。

7. $edgeHub の必要なプロパティから **routes** プロパティを見つけます。

   IoT Edge ハブ モジュールの機能の 1 つは、デプロイ内のすべてのモジュール間でメッセージをルーティングすることです。 routes プロパティの値を確認します。 1 つのルートである **IotEdgeModule1ToIoTHub** では、IotEdgeModule1 モジュールの出力キューから送信されるすべてのメッセージを対象とするために、ワイルドカード文字 (* *\** _) を使用します。 これらのメッセージは、IoT Hub を示す予約名である _$upstream* に入ります。 もう 1 つのルートである **sensorToIotEdgeModule1** では、SimulatedTemperatureSensor モジュールから送信されたメッセージを受け取り、それらを IotEdgeModule1 モジュールの *input1* 入力キューにルーティングします。

   ![deployment.template.json でルートを確認する](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>ソリューションをビルドしてプッシュする

モジュール コードとデプロイ テンプレートを確認して、重要なデプロイの概念を理解しました。 これで、IotEdgeModule1 コンテナー イメージをビルドして、コンテナー レジストリにプッシュする準備ができました。 Visual Studio の IoT ツール拡張機能を使用して、この手順では、テンプレート ファイルの情報とソリューション ファイルのモジュール情報に基づいて配置マニフェストも生成します。

### <a name="sign-in-to-docker"></a>Docker にサインインする

コンテナー イメージをレジストリにプッシュして格納できるように、開発マシン上の Docker にコンテナー レジストリの資格情報を入力します。

1. PowerShell またはコマンド プロンプトを開きます。

2. レジストリを作成した後に保存した Azure コンテナー レジステリの資格情報を使用して Docker にサインインします。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスをご覧ください。

### <a name="build-and-push"></a>ビルドとプッシュ

これで開発マシンはコンテナー レジストリにアクセスできるようになり、ご使用の IoT Edge デバイスもアクセスできるようになりました。 次に、プロジェクト コードをコンテナー イメージに変換します。

1. **CSharpTutorialApp** プロジェクト フォルダーを右クリックし、 **[IoT Edge モジュールをビルドしてプッシュする]** を選択します。

   ![IoT Edge モジュールをビルドしてプッシュする](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。

   このプロセスは、初回は数分間かかる可能性がありますが、次回これらのコマンドを実行するときは、それより速くなります。

2. 新しく作成した config フォルダーで **deployment.windows-amd64.json** ファイルを開きます。 (config フォルダーが Visual Studio のソリューション エクスプローラーに表示されない場合があります。 その場合は、ソリューション エクスプローラーのタスクバーで **[すべてのファイルを表示]** アイコンを選択します。

3. IotEdgeModule1 セクションの **image** パラメーターを見つけます。 image には、module.json ファイルからの名前、バージョン、およびアーキテクチャのタグを含む完全なイメージ レポジトリが含まれていることに留意してください。

4. IotEdgeModule1 フォルダー内の **module.json** ファイルを開きます。

5. モジュール イメージのバージョン番号を変更します。 (version です。$schema-version ではありません。)たとえば、モジュール コードで軽微な修正を行ったかのように、修正プログラムのバージョン番号を **0.0.2** に増分します。

   >[!TIP]
   >モジュール バージョンではバージョン管理が有効であり、実稼働環境に更新プログラムをデプロイする前に、少数のデバイスで変更をテストすることができます。 ビルドとプッシュの前にモジュール バージョンを増分しないと、コンテナー レジストリ内のリポジトリが上書きされます。

6. 変更内容を module.json ファイルに保存します。

7. **CSharpTutorialApp** プロジェクト フォルダーをもう一度右クリックし、 **[IoT Edge モジュールをビルドしてプッシュする]** をもう一度選択します。

8. **deployment.windows-amd64.json** ファイルを再び開きます。 ビルドおよびプッシュ コマンドをもう一度実行したときに新しいファイルが作成されなかったことに注意してください。 代わりに、同じファイルが変更を反映するように更新されました。 IotEdgeModule1 イメージは現在、コンテナーの 0.0.2 バージョンを指しています。 配置マニフェストのこの変更は、IoT Edge デバイスに対して、プルする必要があるモジュールの新しいバージョンがあることを伝える方法です。

9. ビルドおよびプッシュ コマンドで何が行われたかをさらに確認するには、[Azure portal](https://portal.azure.com) にアクセスして、コンテナー レジストリに移動します。

10. コンテナー レジストリで、 **[リポジトリ]** を選択し、次に **iotedgemodule1** を選択します。 イメージの両方のバージョンがレジストリにプッシュされていたことを確認します。

    ![コンテナー レジストリ内の両方のイメージのバージョンを表示する](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>トラブルシューティング

モジュール イメージをビルドおよびプッシュしているときにエラーが発生する場合は、開発マシン上の Docker 構成に関連していることがよくあります。 次のチェックを使用して構成を確認してください。

* コンテナー レジストリからコピーした資格情報を使用して `docker login` コマンドを実行したか。 これらの資格情報は、Azure にサインインする際に使用するものとは異なります。
* コンテナー リポジトリは正しいか。 正しいコンテナー レジストリ名と正しいモジュール名が含まれているか。 チェックする IotEdgeModule1 フォルダー内の **module.json** ファイルを開きます。 リポジトリ値は、 **\<registry name\>.azurecr.io/iotedgemodule1** のようになっているはずです。
* **IotEdgeModule1** とは異なる名前を自分のモジュールに使用した場合、その名前はソリューション全体で一貫しているか。
* 対象のマシンは、ビルドしているのと同じ種類のコンテナーを実行しているか。 このチュートリアルは Windows IoT Edge デバイスを対象としているため、Visual Studio ファイルには **windows-amd64** 拡張子があり、Docker Desktop では Windows コンテナーが実行されている必要があります。

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

ビルドしたコンテナー イメージがコンテナー レジストリに格納されているのを確認したので、次にそれらをデバイスにデプロイします。 お使いの IoT Edge デバイスが稼働していることを確認します。

1. Visual Studio で Cloud Explorer を開き、お使いの IoT ハブの詳細を展開します。

2. デプロイ先のデバイスの名前を入力します **[アクション]** の一覧で、 **[デプロイの作成]** を選択します。

   ![単一デバイスのデプロイを作成する](./media/tutorial-develop-for-windows/create-deployment.png)

3. ファイル エクスプローラーで、自分のプロジェクトの config フォルダーに移動して、**deployment.windows-amd64.json** ファイルを選択します。 通常、このファイルは `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json` にあります。

   deployment.template.json ファイルは使用しないでください。これには、完全なモジュール イメージの値が含まれていません。

4. Cloud Explorer で IoT Edge デバイスの詳細を展開すると、お使いのデバイス上のモジュールが表示されます。

5. **[最新の情報に更新]** ボタンを使用してデバイス状態を更新し、SimulatedTemperatureSensor および IotEdgeModule1 モジュールがお使いのデバイスにデプロイされていることを確認します。

   ![IoT Edge デバイスで実行されているモジュールを表示する](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>デバイスからのメッセージを表示する

IotEdgeModule1 コードは、入力キューを介してメッセージを受け取り、出力キューを介してそれらを渡します。 配置マニフェストは、メッセージを SimulatedTemperatureSensor から IotEdgeModule1 に渡し、次に IotEdgeModule1 から IoT Hub にメッセージを転送したルートを宣言しました。 Visual Studio 用の Azure IoT Edge ツールを使用すると、個々のデバイスから IoT Hub に到着したメッセージを表示できます。

1. Visual Studio Cloud Explorer で、デプロイ先の IoT Edge デバイスの名前を選択します。

2. **[アクション]** メニューの **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

3. Visual Studio の **[出力]** セクションを監視して、IoT ハブに到着するメッセージを確認してください。

   両方のモジュールが開始するまでに数分かかる場合があります。 IoT Edge ランタイムは、新しい配置マニフェストを受け取り、コンテナー ランタイムからモジュール イメージを取得して、それぞれの新しいモジュールを開始する必要があります。

   ![デバイスからクラウドへの着信メッセージを表示する](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>デバイスでの変更を表示する

デバイス自体で何が起こっているかを確認する場合は、このセクションのコマンドを使用して、ご使用のデバイスで実行されている IoT Edge ランタイムとモジュールを検査します。

このセクションのコマンドは、開発マシンではなく、IoT Edge デバイスを対象としています。 IoT Edge デバイスに仮想マシンを使用している場合は、すぐに接続してください。 Azure で、仮想マシンの概要ページに移動し、 **[接続]** を選択してリモート デスクトップ接続にアクセスします。 デバイス上で、コマンドまたは PowerShell ウィンドウを開き、`iotedge` コマンドを実行します。

* お使いのデバイスにデプロイされているすべてのモジュールを表示し、それらの状態をチェックします。

   ```cmd
   iotedge list
   ```

   4 つのモジュール (2 つの IoT Edge ランタイム モジュール、SimulatedTemperatureSensor、および IotEdgeModule1) が表示されるはずです。 4 つすべてが実行中として一覧に表示されるはずです。

* 次のように、特定のモジュールのログを検査します。

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge モジュールでは大文字と小文字の区別があります。

   SimulatedTemperatureSensor と IotEdgeModule1 のログには、処理しているメッセージが表示されるはずです。 edgeAgent モジュールには、他のモジュールを開始する責任があります。そのため、そのログには、配置マニフェストの実装に関する情報が含まれます。 いずれかのモジュールが一覧に表示されていない、または実行されていない場合は、おそらく edgeAgent のログにエラーが書き込まれます。 edgeHub モジュールは、モジュールと IoT Hub 間の通信を担当します。 モジュールは稼働しているが、メッセージが IoT ハブに到着していない場合は、おそらく edgeHub のログにエラーが書き込まれます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、開発マシンに Visual Studio 2019 を設定し、そこから最初の IoT Edge モジュールをデプロイしました。 これで基本的な概念が理解できたので、モジュールを通過するデータを分析できるように、モジュールに機能を追加してみます。 使用したい言語を選択。

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)