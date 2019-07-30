---
title: Linux デバイスのモジュールを開発する - Azure IoT Edge | Microsoft Docs
description: このチュートリアルでは、Linux コンテナーを使用して Linux デバイス用の IoT Edge モジュールを開発するための、開発マシンとクラウド リソースの設定について説明します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: e5499afebf29df2942e74148b33797844fa9c880
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051936"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>チュートリアル:Linux のデバイス用の IoT Edge モジュールを開発する

Visual Studio Code を使用して、コードを開発して、IoT Edge を実行している Linux デバイスにデプロイします。 

クイックスタートの記事では、Linux 仮想マシンを使用して IoT Edge デバイスを作成し、Azure Marketplace から事前構成済みのモジュールをデプロイしました。 このチュートリアルでは、独自のコードを開発して IoT Edge デバイスにデプロイする方法について説明します。 このチュートリアルは、特定のプログラミング言語や Azure サービスをより詳細に説明する、他のすべてのチュートリアルにとって有用な前提条件です。 

このチュートリアルでは、**C# モジュールの Linux デバイスへの**展開例を使用します。 この例が選択された理由は、IoT Edge ソリューションで最も一般的な開発者シナリオだからです。 別の言語の使用や Azure サービスのデプロイを予定している場合でも、このチュートリアルは開発ツールと概念の学習に役立ちます。 この開発プロセスの概要を完了したら、使用する言語や Azure サービスを選択して、詳細に進むことができます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 開発マシンを設定する。
> * Visual Studio Code 用の IoT Edge ツールを使用して、新しいプロジェクトを作成する。
> * プロジェクトをコンテナーとしてビルドして、Azure Container Registry に格納する。
> * コードを IoT Edge デバイスにデプロイする。 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>主要な概念

このチュートリアルでは、IoT Edge モジュールの開発について順を追って説明します。 *IoT Edge モジュール* (略して、単に*モジュール*と呼ばれることもある) は、実行可能コードを含むコンテナーです。 IoT Edge デバイスには 1 つ以上のモジュールをデプロイできます。 モジュールは、センサーからのデータの取り込み、データ分析操作やデータ クリーニング操作の実行、または IoT ハブへのメッセージの送信のような特定のタスクを実行します。 詳細については、「[Azure IoT Edge モジュールについて](iot-edge-modules.md)」を参照してください。

IoT Edge モジュールを開発する場合は、開発マシンと、モジュールが最終的にデプロイされるターゲット IoT Edge デバイスの違いを理解することが重要です。 モジュール コードを保持するためにビルドするコンテナーは、*ターゲット デバイス*のオペレーティング システム (OS) と一致している必要があります。 たとえば、最も一般的なシナリオは、ある人が Windows コンピューターでモジュールを開発しているが、そのターゲットとして、IoT Edge を実行している Linux デバイスを予定している場合です。 その場合、コンテナーのオペレーティング システムは Linux になります。 このチュートリアルを進めていくときには、*開発マシンの OS* と*コンテナーの OS* の違いに留意してください。

このチュートリアルでは、IoT Edge を実行している Linux デバイスをターゲットとしています。 ご使用の開発マシンが Linux コンテナーを実行できる限り、自分の好きな開発マシンのオペレーティング システムを使用することができます。 Linux デバイス用の開発には Visual Studio Code を使用することをお勧めします。そのため、このチュートリアルでもそれを使用します。 Visual Studio も使用できますが、この 2 つのツールの間にはサポートに違いがあります。

次の表に、**Linux コンテナー**に関して Visual Studio Code と Visual Studio でサポートされる開発シナリオを示します。

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux のデバイスのアーキテクチャ** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure サービス** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Languages** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **詳細情報** | [Visual Studio Code 用の Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Visual Studio 2017 用の Azure IoT Edge ツール](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Visual Studio 2019 用の Azure IoT Edge ツール](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

このチュートリアルでは、Visual Studio Code の開発手順を説明します。 Visual Studio を使用する場合は、「[Visual Studio 2019 を使用して Azure IoT Edge 用のモジュールを開発してデバッグする](how-to-visual-studio-develop-module.md)」に記載されている手順を参照してください。

## <a name="prerequisites"></a>前提条件

開発マシン:

* 開発設定に応じて、独自のコンピューターまたは仮想マシンを使用できます。
* コンテナー エンジンを実行できるほとんどのオペレーティング システムを使用して、Linux デバイス用の IoT Edge モジュールを開発することができます。 このチュートリアルでは、Windows コンピューターを使用しますが、MacOS または Linux での既知の相違点を指摘します。 
* このチュートリアルの後半でモジュール テンプレート パッケージをプルするために、[Git](https://git-scm.com/) をインストールします。  
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

Linux 上の Azure IoT Edge デバイス:

* 開発マシンでは IoT Edge を実行せず、代わりに別個のデバイスを使用することをお勧めします。 開発マシンと IoT Edge デバイスのこの区別は、実際のデプロイ シナリオをより正確に反映し、異なる概念を区別するのに役立ちます。
* 2 台目のデバイスが使用可能でない場合は、クイックスタートの記事を使用して、[Linux 仮想マシン](quickstart-linux.md)によって Azure で IoT Edge デバイスを作成してください。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

## <a name="install-container-engine"></a>コンテナー エンジンをインストールする

IoT Edge モジュールはコンテナーとしてパッケージされるので、それらのコンテナーをビルドおよび管理するためには、開発マシン上にコンテナー エンジンが必要です。 Docker Desktop には多くの機能があり、コンテナー エンジンとして人気があるため、開発にはこれを使用することをお勧めします。 Windows デバイスで Docker Desktop を使用すると、Linux コンテナーと Windows コンテナーを切り替えて、さまざまな種類の IoT Edge デバイス用のモジュールを容易に開発することができます。 

次の Docker のドキュメントを使用して、ご使用の開発マシンにインストールします。 

* [Install Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * Docker Desktop for Windows をインストールするときに、Linux コンテナーを使用するか Windows コンテナーを使用するかを尋ねられます。 この決定は、簡単なスイッチを使用して、いつでも変更できます。 このチュートリアルでは、モジュールが Linux デバイスをターゲットとしているので Linux コンテナーを使用します。 詳細については、「[Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)」を参照してください。

* [Install Docker Desktop for Windows](https://docs.docker.com/docker-for-mac/install/)

* いくつかの Linux プラットフォームでのインストール情報については、「[About Docker CE](https://docs.docker.com/install/)」をお読みください。

## <a name="set-up-vs-code-and-tools"></a>VS Code とツール設定する

Visual Studio Code の IoT 拡張機能を使用して、IoT Edge モジュールを開発します。 これらの拡張機能は、プロジェクト テンプレートを提供し、配置マニフェストの作成を自動化し、IoT Edge デバイスの監視および管理を可能にします。 このセクションでは、Visual Studio Code と IoT 拡張機能をインストールし、次に Visual Studio Code 内から IoT Hub のリソースを管理する Azure アカウントを設定します。 

1. 開発マシンに [Visual Studio Code](https://code.visualstudio.com/) をインストールします。 

2. インストールが完了したら、 **[表示]**  >  **[拡張機能]** を選択します。 

3. **Azure IoT Tools** を検索します。これは実際には、IoT Hub や IoT デバイスとの対話、および IoT Edge モジュールの開発に役立つ拡張機能のコレクションです。 

4. **[インストール]** を選択します。 含まれている各拡張機能は個別にインストールされます。 

5. 拡張機能のインストールが完了したら、 **[表示]**  >  **[コマンド パレット]** を選択してコマンド パレットを開きます。 

6. コマンド パレットで、「**Azure:サインイン**」を検索して選びます。 プロンプトに従って Azure アカウントにサインインします。 

7. 再度コマンド パレットで、「**Azure IoT Hub:IoT Hub の選択**」を検索して選びます。 プロンプトに従って、Azure サブスクリプションと IoT Hub を選択します。 

7. 左側のアクティビティ バーでアイコンを選択するか、 **[表示]**  >  **[エクスプローラー]** を選択して、Visual Studio Code のエクスプローラー セクションを開きます。 

8. エクスプローラー セクションの下部で、折りたたまれている **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** メニューを展開します。 コマンド パレットから選択した IoT Hub に関連付けられたデバイスと IoT Edge デバイスが表示されるはずです。 

   ![IoT Hub にデバイスを表示する](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>新しいモジュール プロジェクトを作成する

Azure IoT Tools の拡張機能は、Visual Studio Code でサポートされているすべての IoT Edge モジュール言語のプロジェクト テンプレートを提供します。 これらのテンプレートは、作業モジュールをデプロイして IoT Edge をテストするために必要なすべてのファイルとコードを含んでいます。または、独自のビジネス ロジックを使用してテンプレートをカスタマイズするための開始点を提供します。 

このチュートリアルで、この C# モジュール テンプレートを使用するのは、最も一般的に使用されているテンプレートだからです。 

### <a name="create-a-project-template"></a>プロジェクト テンプレートを作成する

Visual Studio Code のコマンド パレットで、次を検索して選択してください: **Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 プロンプトに従って、次の値を使用してソリューションを作成します。 

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **C# モジュール**を選択します。 |
   | Provide a module name (モジュール名の指定) | 既定の **SampleModule** を受け入れます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 前の手順で指定した名前がコンテナー イメージに事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br> 最終的なイメージ リポジトリは、\<レジストリ名\>.azurecr.io/samplemodule のようになります。 |
 
   ![Docker イメージ リポジトリを指定する](./media/tutorial-develop-for-linux/image-repository.png)

新しいソリューションが Visual Studio Code ウィンドウに読み込まれたら、少し時間を取って、作成されたファイルをよく確認してください。 

* **.vscode** フォルダーには、モジュールのデバッグに使用される **launch.json** というファイルが含まれています。
* **modules** フォルダーには、ソリューション内の各モジュールのフォルダーが含まれています。 現在、これは **SampleModule** (または該当するモジュールに付けられた任意の名前) のみのはずです。 SampleModule フォルダーには、メイン プログラム コード、モジュールのメタデータ、およびいくつかの Docker ファイルが含まれています。 
* **.env** ファイルは、コンテナー レジストリへの資格情報を保持しています。 これらの資格情報は IoT Edge デバイスと共有されており、コンテナー イメージをプルするためにアクセスすることができます。 
* **deployment.debug.template.json** ファイルと **deployment.template.json** ファイルは、配置マニフェストの作成に役立つテンプレートです。 *配置マニフェスト*は、どのモジュールをデバイスにデプロイするか、それらをどのように構成するか、そしてそれらが互いに、およびクラウドとどのように通信するかを正確に定義するファイルです。 テンプレート ファイルは、一部の値にポインターを使用します。 テンプレートを真の配置マニフェストに変換するときに、ポインターは他のソリューション ファイルから取得した値に置き換えられます。 デプロイ テンプレートで 2 つの共通プレース ホルダーを見つけます。 

  * レジストリ資格情報セクションで、アドレスは、ソリューションを作成したときに指定した情報から自動的に入力されています。 ただし、ユーザー名とパスワードは、.env ファイルに格納されている変数を参照します。 これはセキュリティのためです .env ファイルは GIT Ignore ですが、デプロイ テンプレートはそうではないからです。 
  * ソリューションを作成したときにイメージ リポジトリを指定しましたが、SampleModule セクションでコンテナー イメージは入力されていません。 このプレースホルダーは、SampleModule フォルダー内の **module.json** ファイルを指しています。 そのファイルに移動すると、イメージ フィールドにはリポジトリが含まれているだけでなく、バージョンとコンテナーのプラットフォームで構成されるタグ値も含まれていることが分かります。 バージョンは、開発サイクルの一部として手動で反復することができ、コンテナー プラットフォームは、このセクションの後半で紹介するスイッチャーを使用して選択します。 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>レジストリの資格情報を IoT Edge エージェントに提供する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでは、コンテナー イメージを IoT Edge デバイスにプルするためにこれらの資格情報が必要です。 

IoT Edge 拡張機能は、Azure からコンテナー レジストリの資格情報をプルし、それらを環境ファイルに取り込もうとします。 資格情報が既に含まれているかどうかを確認します。 含まれていない場合は、次のようにして追加します。

1. モジュール ソリューション内の **.env** ファイルを開きます。 
2. Azure コンテナー レジストリからコピーした **username** と **password** の値を追加します。
3. 変更内容を .env ファイルに保存します。 

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では Linux AMD64 および ARM32v7 デバイス用の C# モジュールを開発できます。 ソリューションごとにターゲットのアーキテクチャを選択する必要があります。これは、コンテナーのビルド方法と実行方法に影響を与えるからです。 既定値は Linux AMD64 です。 

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。 

   ![サイド バーのアーキテクチャ アイコンを選択する](./media/tutorial-develop-for-linux/select-architecture.png)

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定の **amd64** を維持します。 

### <a name="review-the-sample-code"></a>サンプル コードを確認する

作成したソリューション テンプレートには、IoT Edge モジュールのサンプル コードが含まれています。 このサンプル モジュールは、単にメッセージを受け取って渡すだけです。 パイプライン機能は、モジュールがどのようにして相互に通信を行うかという、IoT Edge での重要な概念を示します。

各モジュールは、コードで宣言された複数の *入力*キューと *出力*キューを持つことができます。 デバイスで実行されている IoT Edge ハブは、1 つのモジュールの出力から、1 つ以上のモジュールの入力にメッセージをルーティングします。 入力と出力を宣言するための特定の言語は、言語によって異なりますが、その概念はすべてのモジュールで同じです。 モジュール間のルーティングの詳細については、[ルートの宣言](module-composition.md#declare-routes)に関する記事を参照してください。

プロジェクト テンプレートに含まれるサンプル C# コードには、.NET 用 IoT Hub SDK の [ModuleClient クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)が使用されています。 

1. **modules/SampleModule/** フォルダー内にある **Program.cs** ファイルを開きます。 

2. program.cs で **SetInputMessageHandlerAsync** メソッドを見つけます。

2. [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) メソッドでは、受信メッセージを受け取る入力キューが設定されます。 このメソッドを確認し、**input1** という入力キューがどのように初期化されるかを確かめます。 

   ![SetInputMessageCallback コンストラクターで入力名を見つける](./media/tutorial-develop-for-linux/declare-input-queue.png)

3. 次に、**SendEventAsync** メソッドを見つけます。

4. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) メソッドでは、受け取ったメッセージが処理され、それらを渡すための出力キューが設定されます。 このメソッドを確認し、**output1** という出力キューが初期化されることを確かめます。 

   ![SendEventToOutputAsync で出力名を見つける](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. **deployment.template.json** ファイルを開きます。

7. $edgeAgent の必要なプロパティの **modules** プロパティを見つけます。 

   ここには 2 つのモジュールがリストされているはずです。 1 つ目は **tempSensor** で、これは既定ですべてのテンプレートに含まれており、モジュールをテストするために使用できるシミュレートされた温度データを提供します。 2 つ目は、このソリューションの一部として作成した **SampleModule** モジュールです。

7. ファイルの下部には、 **$edgeHub** モジュールの必要なプロパティがあります。 

   IoT Edge ハブ モジュールの機能の 1 つは、デプロイ内のすべてのモジュール間でメッセージをルーティングすることです。 **routes** プロパティの値を確認します。 最初のルートである **SampleModuleToIoTHub** は、ワイルドカード文字 ( **\*** ) を使用して、SampleModule モジュール内のどの出力キューからのメッセージも示します。 これらのメッセージは、IoT Hub を示す予約名である *$upstream* に入ります。 2 番目のルートである sensorToSampleModule は、tempSensor モジュールから来たメッセージを取得し、SampleModule コードで初期化されているのを確認した *input1* 入力キューにルーティングします。 

   ![deployment.template.json でルートを確認する](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>ソリューションをビルドしてプッシュする

モジュール コードとデプロイ テンプレートを確認して、重要なデプロイの概念を理解しました。 次に、SampleModule コンテナー イメージをビルドして、コンテナー レジストリにプッシュします。 Visual Studio Code の IoT ツール拡張機能を使用して、この手順では、テンプレート ファイルの情報とソリューション ファイルのモジュール情報に基づいて配置マニフェストも生成します。 

### <a name="sign-in-to-docker"></a>Docker にサインインする

コンテナー イメージをレジストリに格納できるように、Docker にコンテナー レジステリの資格情報を提供します。 

1. **[表示]**  >  **[ターミナル]** の順に選択して、Visual Studio Code 統合ターミナルを開きます。

2. レジストリを作成した後に保存した Azure コンテナー レジステリの資格情報を使用して Docker にサインインします。 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告を受け取る場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスを参照してください。

### <a name="build-and-push"></a>ビルドとプッシュ 

Visual Studio Code がコンテナー レジストリにアクセスできるようになったので、次にソリューション コードをコンテナー イメージに変換します。 

1. Visual Studio Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

   ![IoT Edge モジュールをビルドしてプッシュする](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   ビルドおよびプッシュ コマンドは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 次に、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。 

   このプロセスは、初回は数分間かかる可能性がありますが、次回これらのコマンドを実行するときは、それより速くなります。 

2. 新しく作成した config フォルダーで **deployment.amd64.json** ファイルを開きます。 このファイル名はターゲット アーキテクチャを反映しているため、別のアーキテクチャを選択した場合は別の名前になります。

3. プレースホルダーを持っていた 2 つのパラメーターに、適切な値が入力されたことに注意してください。 **RegistryCredentials** セクションには、.env ファイルからプルされたレジストリのユーザー名とパスワードが入っています。 **SampleModule** には、module.json ファイルからの名前、バージョン、およびアーキテクチャのタグを含む完全なイメージ レポジトリがあります。 

4. SampleModule フォルダー内の **module.json** ファイルを開きます。 

5. モジュール イメージのバージョン番号を変更します。 (version です。$schema-version ではありません。)たとえば、モジュール コードで軽微な修正を行ったかのように、修正プログラムのバージョン番号を **0.0.2** に増分します。 

   >[!TIP]
   >モジュール バージョンではバージョン管理が有効であり、実稼働環境に更新プログラムをデプロイする前に、少数のデバイスで変更をテストすることができます。 ビルドとプッシュの前にモジュール バージョンを増分しないと、コンテナー レジストリ内のリポジトリが上書きされます。 

6. 変更内容を module.json ファイルに保存します。

7. **deployment.template.json** ファイルをもう一度右クリックし、 **[Build and Push IoT Edge Solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を再度選択します。

8. **deployment.amd64.json** ファイルを再び開きます。 ビルドおよびプッシュ コマンドをもう一度実行したときに新しいファイルが作成されなかったことに注意してください。 代わりに、同じファイルが変更を反映するように更新されました。 SampleModule イメージは現在、コンテナーの 0.0.2 バージョンを指しています。 

9. ビルドおよびプッシュ コマンドで何が行われたかをさらに確認するには、[Azure portal](https://portal.azure.com) にアクセスして、コンテナー レジストリに移動します。 

10. コンテナー レジストリで、 **[リポジトリ]** を選択し、次に **samplemodule** を選択します。 イメージの両方のバージョンがレジストリにプッシュされていたことを確認します。

    ![コンテナー レジストリ内の両方のイメージのバージョンを表示する](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>トラブルシューティング

モジュール イメージをビルドおよびプッシュしているときにエラーが発生する場合は、開発マシン上の Docker 構成に関連していることがよくあります。 次のチェックを使用して構成を確認してください。 

* コンテナー レジストリからコピーした資格情報を使用して `docker login` コマンドを実行したか。 これらの資格情報は、Azure にサインインする際に使用するものとは異なります。 
* コンテナー リポジトリは正しいか。 正しいコンテナー レジストリ名と正しいモジュール名が含まれているか。 SampleModule フォルダー内の **module.json** ファイルを開いて確認してください。 リポジトリ値は、 **\<レジストリ名\>.azurecr.io/samplemodule** のようになっているはずです。 
* **SampleModule** とは異なる名前を自分のモジュールに使用した場合、その名前はソリューション全体で一貫しているか。
* 対象のマシンは、ビルドしているのと同じ種類のコンテナーを実行しているか。 このチュートリアルは Linux IoT Edge デバイスを対象としているため、Visual Studio Code のサイド バーには **amd64** または **arm32v7** と表示され、Docker Desktop は Linux コンテナーを実行している必要があります。  

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

ビルドしたコンテナー イメージがコンテナー レジストリに格納されているのを確認したので、次にそれらをデバイスにデプロイします。 お使いの IoT Edge デバイスが稼働していることを確認します。 

1. Visual Studio Code エクスプローラーで、[Azure IoT Hub Devices] (Azure IoT Hub デバイス) セクションを展開します。 

2. デプロイ先の IoT Edge デバイスを右クリックし、次に **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 

   ![単一デバイスのデプロイを作成する](./media/tutorial-develop-for-linux/create-deployment.png)

3. ファイル エクスプローラーで、**config** フォルダーに移動して、**deployment.amd64.json** ファイルを選択します。 

   deployment.template.json ファイルは使用しないでください。これには、コンテナー レジストリ資格情報もモジュール イメージの値も含まれていません。 Linux ARM32 デバイスを対象としている場合、配置マニフェストの名前は deployment.arm32v7.json になります。 

4. IoT Edge デバイスの詳細を展開し、次に、使用するデバイスの**モジュール**一覧を展開します。 

5. tempSensor と SampleModule モジュールがデバイスで実行されているのを確認できるまで、[最新の情報に更新] ボタンを使用してデバイス ビューを更新してください。 

   両方のモジュールが開始するまでに数分かかる場合があります。 IoT Edge ランタイムは、新しい配置マニフェストを受け取り、コンテナー ランタイムからモジュール イメージを取得して、それぞれの新しいモジュールを開始する必要があります。 

   ![IoT Edge デバイスで実行されているモジュールを表示する](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>デバイスからのメッセージを表示する

SampleModule コードは、入力キューを介してメッセージを受け取り、出力キューを介してそれらを渡します。 配置マニフェストは、メッセージを tempSensor から SampleModule に渡し、次に SampleModule から IoT Hub にメッセージを転送したルートを宣言しました。 Visual Studio Code 用の Azure IoT ツールを使用すると、個々のデバイスから IoT Hub に到着したメッセージを表示できます。 

1. Visual Studio Code のエクスプローラーで、監視する IoT Edge デバイスを右クリックして、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。 

2. Visual Studio Code の出力ウィンドウを監視して、IoT ハブに到着するメッセージを確認してください。 

   ![デバイスからクラウドへの着信メッセージを表示する](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>デバイスでの変更を表示する

デバイス自体で何が起こっているかを確認する場合は、このセクションのコマンドを使用して、ご使用のデバイスで実行されている IoT Edge ランタイムとモジュールを検査します。 

このセクションのコマンドは、開発マシンではなく、IoT Edge デバイスを対象としています。 IoT Edge デバイスに仮想マシンを使用している場合は、すぐに接続してください。 Azure で、仮想マシンの概要ページに移動し、 **[接続]** を選択して Secure Shell 接続にアクセスします。 

* お使いのデバイスにデプロイされているすべてのモジュールを表示し、それらの状態をチェックします。

   ```bash
   iotedge list
   ```

   4 つのモジュール (2 つの IoT Edge ランタイム モジュール、tempSensor、および SampleModule) が表示されるはずです。 4 つすべてが実行中として一覧に表示されるはずです。

* 次のように、特定のモジュールのログを検査します。

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge モジュールでは大文字と小文字の区別があります。 

   TempSensor と SamplModule のログには、処理しているメッセージが表示されるはずです。 edgeAgent モジュールには、他のモジュールを開始する責任があります。そのため、そのログには、配置マニフェストの実装に関する情報が含まれます。 いずれかのモジュールが一覧に表示されていない、または実行されていない場合は、おそらく edgeAgent のログにエラーが書き込まれます。 edgeHub モジュールは、モジュールと IoT Hub 間の通信を担当します。 モジュールは稼働しているが、メッセージが IoT ハブに到着していない場合は、おそらく edgeHub のログにエラーが書き込まれます。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、開発マシンに Visual Studio Code を設定し、そこから最初の IoT Edge モジュールをデプロイしました。 これで基本的な概念が理解できたので、モジュールを通過するデータを分析できるように、モジュールに機能を追加してみます。 使用する言語を次から選択します。 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)