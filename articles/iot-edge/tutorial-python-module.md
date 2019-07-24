---
title: カスタム Python モジュールを作成する - Azure IoT Edge | Microsoft Docs
description: このチュートリアルでは、Python コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0affd965bbfc587933a9cdbf5b96c470a6e4dd6a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578290"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>チュートリアル: Python IoT Edge モジュールを開発して、シミュレートされたデバイスにデプロイする

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、クイック スタートで設定した IoT Edge デバイス上のセンサー データをフィルター処理する IoT Edge モジュールの作成とデプロイについて段階的に説明します。 このチュートリアルでは、以下の内容を学習します。    

> [!div class="checklist"]
> * Visual Studio Code を使用して IoT Edge Python モジュールを作成する。
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) 用のクイック スタートに記載された手順に従うことで、Azure 仮想マシンを IoT Edge デバイスとして使用できます。
* IoT Edge 用 Python モジュールは、Windows コンテナーをサポートしていません。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* Visual Studio Code 用 の [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* [Python](https://www.python.org/downloads/)。
* Python パッケージをインストールするための [Pip](https://pip.pypa.io/en/stable/installing/#installation) (通常は、Python のインストールに含まれています)。
* [Docker CE](https://docs.docker.com/install/)。 
  * Windows マシン上で開発している場合は、Docker が [Linux コンテナーを使用するように構成されている](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)ことを確認してください。 

>[!Note]
>`bin` フォルダーがプラットフォームのパスにあることを確認してください。 通常、UNIX および macOS では `~/.local/`、Windows では `%APPDATA%\Python` です。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このチュートリアルでは、Visual Studio Code 用の Azure IoT Tools を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

コンテナー イメージは、Docker と互換性のある任意のレジストリを使用して格納できます。 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

コンテナー レジストリがまだない場合は、次の手順に従って、Azure に新しいコンテナー レジストリを作成します。

1. [Azure portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[コンテナー レジストリ]** の順に選択します。

2. コンテナー レジストリを作成するには、以下の値を指定します。

   | フィールド | 値 | 
   | ----- | ----- |
   | レジストリ名 | 一意の名前を指定します。 |
   | サブスクリプション | ドロップダウン リストで、サブスクリプションを選択します。 |
   | リソース グループ | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | Location | 近くの場所を選択します。 |
   | 管理者ユーザー | **[有効]** に設定します。 |
   | SKU | **[Basic]** を選択します。 | 

5. **作成**を選択します。

6. コンテナー レジストリが作成されたら、その場所を参照し、**[アクセス キー]** を選択します。 

7. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値は、このチュートリアルで後ほどコンテナー レジストリへのアクセスを提供するために使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Tools を使用して、IoT Edge Python モジュールを作成します。

### <a name="create-a-new-solution"></a>新しいソリューションの作成

Python パッケージ **cookiecutter** を使用して、ソリューションのベースにする Python ソリューション テンプレートを作成します。 

1. Visual Studio Code で、**[表示]** > **[ターミナル]** の順に選択し、VS Code 統合ターミナルを開きます。

2. ターミナルで、次のコマンドを入力して、**cookiecutter** をインストール (または更新) します。これは、IoT Edge ソリューション テンプレートを作成するときに使用します。

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >コマンド プロンプトから起動できるように、cookiecutter をインストールするディレクトリが環境の PATH にあることを確認してください。 ディレクトリは、インストール スクリプトの出力の一部です。たとえば、`C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts` などです。
   >
   >PATH への変更を反映するには、Visual Studio Code を再起動します。 

3. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 

4. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、手順に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

5. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 プロンプトに従って、次の情報を指定してソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Python モジュール]** を選択します。 |
   | Provide a module name (モジュール名の指定) | ご自身のモジュール **PythonModule** に名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 前の手順で指定した名前がコンテナー イメージに事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br>最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/pythonmodule のようになります。 |
 
   ![Docker イメージ リポジトリを指定する](./media/tutorial-python-module/repository.png)

VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 ソリューション ワークスペースには、最上位の 5 つのコンポーネントが含まれています。 **modules** フォルダーには、モジュール用の Python コードと、モジュールをコンテナー イメージとして構築するための Dockerfiles が含まれています。 **\.env** ファイルには、コンテナー レジストリの資格情報が格納されています。 **deployment.template.json** ファイルには、デバイスにモジュールをデプロイするために IoT Edge ランタイムが使用する情報が含まれています。 また、**deployment.debug.template.json** ファイルには、モジュールのデバッグ バージョンが含まれています。 このチュートリアルでは、**\.vscode** フォルダーまたは **\.gitignore** ファイルは編集しません。  

ソリューションの作成時にコンテナー レジストリを指定せず、既定値の localhost:5000 のままにすると、\.env ファイルは作成されません。 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー リポジトリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 

1. VS Code エクスプローラーで、**.env** ファイルを開きます。 
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。 
3. .env ファイルを保存します。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

各テンプレートにはサンプル コードが付属しており、シミュレートされたセンサー データが **tempSensor** モジュールから取得され、IoT Hub にルーティングされます。 このセクションでは、メッセージを送信する前に、**PythonModule** を展開してそのメッセージを分析するコードを追加します。 

1. VS Code エクスプローラーで、**[モジュール]** > **[PythonModule]** > **[main.py]** を開きます。

2. **main.py** ファイルの上部で、**json** ライブラリをインポートします。

    ```python
    import json
    ```

3. グローバル カウンターで **TEMPERATURE_THRESHOLD** 変数と **TWIN_CALLBACKS** 変数を追加します。 温度のしきい値により、データが IoT Hub に送信される基準値が設定されます。データは、マシンの測定温度がこの値を超えると送信されます。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. **receive_message_callback** 関数を次のコードに置き換えます。

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. **module_twin_callback** という新しい関数を追加します。 この関数は、必要なプロパティが更新されたときに呼び出されます。

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. **HubManager** クラスで、新しい行を **__init__** メソッドに追加して、先ほど追加した **module_twin_callback** 関数を初期化します。

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. main.py ファイルを保存します。

8. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 このファイルは、IoT Edge エージェントにどのモジュール (この場合は **tempSensor** および **PythonModule**) をデプロイするかを指示し、IoT Edge ハブにそれらの間でメッセージをルーティングする方法を指示します。 デプロイ テンプレートで必要なほとんどの情報は Visual Studio Code 拡張機能によって自動的に入力されますが、ソリューションに関してすべての情報が正確であることを確認します。 

   1. IoT Edge の既定のプラットフォームは、VS Code のステータス バーで **amd64** に設定されています。つまり、**PythonModule** は Linux amd64 バージョンのイメージに設定されています。 IoT Edge デバイスのアーキテクチャに応じて、ステータス バーで既定のプラットフォームを **amd64** から **arm32v7** に変更します。 

      ![モジュール イメージ プラットフォームを更新する](./media/tutorial-python-module/image-platform.png)

   2. テンプレートで、モジュール名が既定の **SampleModule** という名前ではなく、IoT Edge ソリューションの作成時に変更した正しい名前になっていることを確認します。

   3. IoT Edge エージェントがモジュール イメージを取得できるように、**registryCredentials** セクションには Docker レジストリの認証情報が格納されています。 実際のユーザー名とパスワードの組み合わせは、.env ファイル (Git では無視されます) に格納されます。 まだ行っていない場合は、.env ファイルに資格情報を追加します。  

   4. デプロイ マニフェストの詳細については、「[IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します。](module-composition.md)」を参照してください。

9. **PythonModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **moduleContent** セクションの下部、**$edgeHub** モジュール ツインの後に挿入します。 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![モジュール ツインをデプロイ テンプレートに追加する](./media/tutorial-python-module/module-twin.png)

10. deployment.template.json ファイルを保存します。

## <a name="build-and-push-your-solution"></a>ソリューションをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、**PythonModule** にコードを追加しました。これにより、許容されるしきい値をマシンの温度が下回ることを報告するメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。 

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 これで、必要なモジュール イメージを Azure Container Registry にプッシュすることができます。 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   最初のセクションで Azure Container Registry からコピーしたユーザー名、パスワード、ログイン サーバーを使用します。 または、Azure portal でご自身のレジストリの **[アクセス キー]** セクションからこれらの値を取得することもできます。

   --password-stdin パラメーターの使用を推奨するセキュリティ警告が表示される場合があります。 このパラメーターの使用について、ここでは説明していませんが、このベスト プラクティスに従うことをお勧めします。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) コマンドのリファレンスを参照してください。 


2. VS Code エクスプローラーで、deployment.template.json ファイルを右クリックし、**[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 この 2 つのコマンドによって、ご自身のコードがビルドされ、Python コードがコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで実行される `docker build` コマンドで確認できます。 イメージ アドレスは、\<リポジトリ\>:\<バージョン\>-\<プラットフォーム\> の形式で、module.json ファイルの情報から作成されます。 このチュートリアルでは、registryname.azurecr.io/pythonmodule:0.0.1-amd64 になります。

>[!TIP]
>モジュールをビルドしてプッシュしようとするときにエラーが発生した場合は、次の点を確認してください。
>* Visual Studio Code からお使いのコンテナー レジストリの資格情報を使用して Docker にサインインしているか。 これらの資格情報は、Azure portal にサインインする際に使用する情報とは異なります。
>* コンテナー リポジトリは正しいか。 **[modules]** > **[PythonModule]** > **[module.json]** の順に開いて、**[repository]** フィールドを見つけます。 イメージ リポジトリは、**\<registryname\>.azurecr.io/pythonmodule** のようになっている必要があります。 
>* 開発マシンで実行されているものと同じタイプのコンテナーをビルドしているか。 Visual Studio Code の既定のコンテナーは Linux amd64 です。 お使いの開発マシンが Linux arm32v7 コンテナーである場合は、それに合わせて、VS Code ウィンドウ下部の青色のステータス バーでプラットフォームを更新してください。 Python モジュールは Windows コンテナーをサポートしません。 

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

IoT Edge デバイスの設定に使用したクイック スタートの記事では、Azure portal を使用してモジュールをデプロイしました。 また、Visual Studio Code 用の Azure IoT Hub Toolkit 拡張機能 (旧称 Azure IoT Toolkit 拡張機能) を使用して、モジュールをデプロイすることもできます。 シナリオ用の配置マニフェストである **deployment.json** ファイルは、既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

1. VS Code コマンド パレットで、**Azure IoT Hub: Select IoT Hub** を実行します。 

2. 構成する IoT Edge デバイスが含まれているサブスクリプションと IoT ハブを選択します。 

3. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

4. IoT Edge デバイスの名前を右クリックして、**[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 

   ![単一デバイスのデプロイを作成する](./media/tutorial-python-module/create-deployment.png)

5. (ターゲット アーキテクチャに応じて) **config** フォルダー内の **deployment.amd64** または **deployment.arm32v7** ファイルを選択し、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。 

6. 更新ボタンをクリックします。 新しい **PythonModule** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。 

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。 

IoT Edge デバイスのステータスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを使用して確認できます。 デバイスの詳細を展開すると、デプロイされて実行中のモジュールが一覧表示されます。 

IoT Edge デバイス自体では、`iotedge list` コマンドを使用してデプロイ モジュールのステータスを確認できます。 2 つの IoT Edge ランタイム モジュールと tempSensor、さらに、このチュートリアルで作成したカスタム モジュールの 4 つのモジュールが表示されると思います。 すべてのモジュールが開始されるまでには数分かかると考えられます。最初に表示されないモジュールがある場合は、コマンドを再実行してください。 

モジュールによって生成されているメッセージを確認するには、`iotedge logs <module name>` コマンドを使用します。 

IoT ハブに到着したメッセージは、Visual Studio Code を使用して確認できます。 

1. IoT Hub に到着するデータを監視するには、省略記号 (**...**) を選択し、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
2. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスを右クリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
3. データの監視を停止するには、コマンド パレットで **Azure IoT Hub:Stop monitoring D2C message** コマンドを実行します。 
4. モジュール ツインを表示または更新するには、一覧でモジュールを右クリックし、**[Edit module twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインを更新するには、ツインの JSON ファイルを保存し、編集領域を右クリックして、**[Update Module Twin]\(モジュール ツインの更新\)** を選択します。
5. Docker ログを表示するには、[Visual Studio Code 用 Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)をインストールします。 ローカルで実行されているモジュールを Docker エクスプローラーで確認できます。 コンテキスト メニューで、**[ログを表示する]** をクリックし、統合ターミナルで表示します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 引き続き次のチュートリアルに進み、Azure IoT Edge が、エッジでデータをビジネス上の分析情報に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとしてデプロイする](tutorial-deploy-function.md)
> [Azure Stream Analytics をモジュールとしてデプロイする](tutorial-deploy-stream-analytics.md)
