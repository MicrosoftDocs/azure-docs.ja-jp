---
title: チュートリアル:カスタム モジュールの作成とデプロイ - Azure IoT Edge での Machine Learning
description: このチュートリアルでは、機械学習モデルを使用してリーフ デバイスからのデータを処理する IoT Edge モジュールを作成およびデプロイした後、その分析情報を IoT Hub に送信します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c096b26f31bdf92bb5ab91c8dad7876f228ff14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462798"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>チュートリアル:カスタム IoT Edge モジュールの作成とデプロイ

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、リーフ IoT デバイスからメッセージを受信し、機械学習モデルを使用してデータを実行し、分析情報を IoT Hub に転送する 3 つの IoT Edge モジュールを作成します。

IoT Edge ハブを使用することで、モジュール間の通信が容易になっています。 メッセージ ブローカーとして IoT Edge ハブを使用することで、モジュールの相互独立性を維持できます。 モジュールは、メッセージを受信する入力と、メッセージを書き出す出力を指定するだけです。

IoT Edge デバイスで 4 つのことを達成できるようにします。

* リーフ デバイスからデータを受信する
* データを送信したデバイスの残存耐用年数 (RUL) を予測する
* デバイスの RUL を含んだメッセージを IoT Hub に送信する。 RUL が特定のレベルを下回った場合にのみデータを送信するようにこの機能を変更することもできます。
* リーフ デバイスのデータを IoT Edge デバイス上のローカル ファイルに保存する。 このデータ ファイルは、機械学習モデルのトレーニングを洗練するために、定期的に IoT Hub にアップロードされます。 絶えず続くメッセージ ストリーミングの代わりにファイル アップロードを使用するとコスト効率が良くなります。

これらのタスクを達成するために、3 つのカスタム モジュールを使用します。

* **RUL 分類器:** [Azure Machine Learning モデルのトレーニングとデプロイ](tutorial-machine-learning-edge-04-train-model.md)に関するページで作成した turboFanRulClassifier モジュールは、"amlInput" という入力と "amlOutput" という出力を公開する標準的な機械学習モジュールです。 "amlInput" は、その入力が、ACI ベースの Web サービスに送信した入力とまったく同じようになることを期待します。 同様に、"amlOutput" は Web サービスと同じデータを返します。

* **Avro ライター:** このモジュールは、"avroModuleInput" 入力時にメッセージを受信し、後で IoT Hub にアップロードするためにメッセージを Avro 形式でディスクに保持します。

* **ルーター モジュール:** ルーター モジュールは、ダウンストリーム リーフ デバイスからメッセージを受信した後、メッセージをフォーマットして分類器に送信します。 モジュールはその後、分類器からメッセージを受信し、そのメッセージを Avro ライター モジュールに転送します。 最後に、モジュールは RUL 予測のみを IoT Hub に送信します。

  * 入力:
    * **deviceInput**: リーフ デバイスからメッセージを受信する
    * **rulInput:** "amlOutput" からメッセージを受信する

  * 出力:
    * **classify:** "amlInput" にメッセージを送信する
    * **writeAvro:** "avroModuleInput" にメッセージを送信する
    * **toIotHub:** $upstream にメッセージを送信し、$upstream は接続済み IoT Hub にメッセージを渡す

次の図は、完全なソリューションのモジュール、入力、出力、および IoT Edge Hub ルートを示しています。

![IoT Edge の 3 つのモジュールのアーキテクチャ図](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

この記事の手順は通常、クラウド開発者によって実行されます。

チュートリアルのこのセクションで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * カスタム コードから IoT Edge モジュールを作成する。
> * カスタム モジュールから Docker イメージを生成する。
> * カスタム モジュールをサポートするように IoT Hub ルーティングを再構成する。
> * カスタム モジュールをビルド、発行、およびデプロイする。

## <a name="prerequisites"></a>前提条件

この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 シリーズの各記事は、前の記事の作業に基づいています。 この記事に直接アクセスしている場合は、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)を参照してください。

## <a name="create-a-new-iot-edge-solution"></a>新しい IoT Edge ソリューションを作成する

2 つの Azure Notebooks のうち 2 番目の実行中に、RUL モデルを含むコンテナー イメージを作成および発行しました。 Azure Machine Learning では、イメージを Azure IoT Edge モジュールとしてデプロイ可能にするために、イメージ作成プロセスの一部として、そのモデルがパッケージ化されています。

このステップでは、"Azure Machine Learning" モジュールを使用して Azure IoT Edge ソリューションを作成し、そのモジュールが、Azure Notebooks を使用して発行したイメージをポイントするようにします。

1. 開発用 VM へのリモート デスクトップ セッションを開きます。

1. **C:\\source\\IoTEdgeAndMlSample** フォルダーを Visual Studio Code で開きます。

1. エクスプローラーのパネル (の何もない部分) を右クリックし、 **[New IoT Edge Solution] \(新しい IoT Edge ソリューション\)** を選択します。

    ![新しい IoT Edge ソリューションを作成する](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. 既定のソリューション名 **EdgeSolution** をそのまま使用します。

1. モジュール テンプレートとして **[Azure Machine Learning]** を選択します。

1. モジュールの名前を **turbofanRulClassifier** にします。

1. 機械学習ワークスペースを選択します。 このワークスペースは、「[チュートリアル: Azure Machine Learning モデルをトレーニングしてデプロイする](tutorial-machine-learning-edge-04-train-model.md)」で作成した **turboFanDemo** ワークスペースです。

1. Azure Notebook の実行中に作成したイメージを選択します。

1. ソリューションを確認すると、次のファイルが作成されています。

   * **deployment.template.json:** このファイルには、ソリューション内の各モジュールの定義が含まれています。 このファイルには、注意を払う必要がある 3 つのセクションがあります。

     * **レジストリ資格情報:** ソリューションで使用しているカスタム コンテナー レジストリのセットを定義します。 この時点では、Azure Machine Learning イメージが格納された場所である機械学習ワークスペースのレジストリが含まれているはずです。 コンテナー レジストリの数に制限はありませんが、わかりやすくするため、この 1 つのレジストリをすべてのモジュールに使用します。

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **モジュール:** このセクションには、このソリューションに対応するユーザー定義モジュールのセットが含まれています。 turbofanRulClassifier モジュール定義は、コンテナー レジストリ内のイメージをポイントします。 ソリューションにモジュールを追加していくと、このセクションに表示されます。

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **ルート**: このチュートリアルでは、ルートをかなり多く扱います。 ルートは、モジュール間の通信方法を定義します。 テンプレートによって定義される既存のルートは、必要としているルーティングに一致しません。 `turbofanRulClassifierToIoTHub` ルートを削除してください。

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.json:** このファイルは deployment.template.json のデバッグ バージョンです。 通常、このファイルは、deployment.template.json ファイルの内容と同期させておく必要がありますが、このチュートリアルではそのようにする必要はありません。

   * **.env**: このファイルには、レジストリにアクセスするためのユーザー名とパスワードを指定する必要があります。

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Visual Studio Code エクスプローラーで deployment.template.json ファイルを右クリックし、 **[Build IoT Edge Solution] \(IoT Edge ソリューションのビルド\)** を選択します。

1. このコマンドは config フォルダーと deployment.amd64.json ファイルを作成することがわかります。 このファイルは、ソリューションの具体的なデプロイ テンプレートです。

## <a name="add-router-module"></a>ルーター モジュールを追加する

次に、Router モジュールをソリューションに追加します。 Router モジュールには、ソリューションにおいて複数の責任があります。

* **リーフ デバイスからメッセージを受信する:** メッセージがダウンストリーム デバイスから IoT Edge デバイスに到達すると、Router モジュールがメッセージを受信し、メッセージのルーティングのオーケストレーションを開始します。
* **RUL Classifier モジュールにメッセージを送信する:** ダウンストリーム デバイスから新しいメッセージを受信したら、Router モジュールは、RUL Classifier が期待する形式にメッセージを変換します。 Router は RUL 予測のために RUL Classifier にメッセージを送信します。 分類器は、予測が終わったら、メッセージを Router モジュールに返送します。
* **RUL メッセージを IoT Hub に送信する:** Router は分類器からメッセージを受信すると、必須の情報 (デバイス ID と RUL) のみが含まれるようにメッセージを変換し、短縮したメッセージを IoT ハブに送信します。 ここでは行っていませんが、さらなる洗練化では、RUL 予測がしきい値を下回る (たとえば、RUL が 100 サイクルに満たない) 場合にのみメッセージを IoT Hub に送信します。 このようにフィルター処理することで、メッセージのボリュームと IoT ハブのコストが削減されます。
* **Avro Writer モジュールにメッセージを送信する:** ダウンストリーム デバイスから送信されたすべてのデータを保持するために、Router モジュールは分類器から受信したメッセージ全体を Avro Writer モジュールに送信し、このモジュールは IoT Hub ファイル アップロードを使用してデータを保持およびアップロードします。

Router モジュールは、メッセージを確実に正しい順序で処理するうえで重要なソリューション要素です。

### <a name="create-the-module-and-copy-files"></a>モジュールを作成してファイルをコピーする

1. Visual Studio Code で modules フォルダーを右クリックし、 **[Add IoT Edge Module] \(IoT Edge モジュールの追加\)** を選択します。

1. モジュール テンプレートの **C# モジュール** を選択します。

1. モジュールの名前を **turbofanRouter** にします。

1. Docker Image Repository の指定を求められたら、機械学習ワークスペースのレジストリを使用します (レジストリは *deployment.template.json* ファイルの registryCredentials ノードにあります)。 この値はレジストリの完全修飾アドレスです (例: **\<your registry\>.azurecr.io/turbofanrouter**)。

    > [!NOTE]
    > この記事では、Azure Machine Learning ワークスペースによって作成された Azure Container Registry を使用します。 これは単に利便性のためです。 新しいコンテナー レジストリを作成し、そこでモジュールを発行することも可能でした。

1. ターミナルからコマンド プロンプト シェルを使用して、サンプル モジュールのファイルをソリューションにコピーします。

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. プロンプトに同意して program.cs ファイルを上書きします。

### <a name="build-router-module"></a>ルーター モジュールをビルドする

1. Visual Studio Code で、 **[Terminal] \(ターミナル\)**  >  **[Configure Default Build Task] \(既定のビルド タスクを構成する\)** を選択します。

1. **[テンプレートから tasks.json を生成]** を選択します。

1. **[.NET Core]** を選択します。

1. tasks.json の内容を、次のコードに置き換えます。

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. tasks.json を保存して閉じます。

1. `Ctrl + Shift + B` キーまたは **[Terminal] \(ターミナル\)**  >  **[ビルド タスクの実行]** でビルドを実行します。

### <a name="set-up-module-routes"></a>モジュールのルートを設定する

前述のように、IoT Edge ランタイムでは、*deployment.template.json* ファイルで構成されたルートを使用して疎結合モジュール間の通信を管理します。 このセクションでは、turbofanRouter モジュールのルートを設定する方法について詳しく説明します。 入力ルート、出力の順に説明します。

#### <a name="inputs"></a>入力

1. Program.cs の Init() メソッドで、モジュールに対して 2 つのコールバックを登録します。

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. 最初のコールバックは、**deviceInput** シンクに送信されるメッセージをリッスンします。 上の図から、任意のリーフ デバイスからのメッセージをこの入力にルーティングしたいことがわかります。 *deployment.template.json* ファイルで、IoT Edge デバイスが受信したメッセージのうち IoT Edge モジュールが送信元でないものはすべて、turbofanRouter モジュールの "deviceInput" という入力にルーティングするようエッジ ハブに指示するルートを追加します。

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. 次に、rulClassifier モジュールから turbofanRouter モジュールへのメッセージのルートを追加します。

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>出力

Router モジュールからの出力を処理するために、4 つの追加ルートを $edgeHub ルート パラメーターに追加します。

1. Program.cs はメソッド SendMessageToClassifier() を定義します。このメソッドは、モジュール クライアントを使用して、次のルートでメッセージを RUL 分類器に送信します。

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() は、モジュール クライアントを使用して、デバイスの RUL データのみを次のルートで IoT Hub に送信します。

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() は、モジュール クライアントを使用して、avroFileWriter モジュールに追加された RUL データと共にメッセージを送信します。

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() は、失敗したメッセージをアップストリームで IoT Hub に送信し、そこで後の処理のためにメッセージをルーティングできます。

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

すべてのルートをまとめると、"$edgeHub" ノードは次の JSON のようになります。

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > turbofanRouter モジュールの追加により、次の追加ルートが作成されました: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`。 このルートを削除し、これまでに説明したルートのみを deployment.template.json ファイルに残します。

## <a name="add-avro-writer-module"></a>Avro Writer モジュールを追加する

Avro Writer モジュールには、ソリューションにおいて、メッセージの格納とファイルのアップロードという 2 つの責任があります。

* **メッセージの格納:** Avro Writer モジュールはメッセージを受信すると、そのメッセージを Avro 形式でローカル ファイル システムに書き込みます。 ここではバインド マウントを使用します。これは、ディレクトリ (この場合は /data/avrofiles) をモジュールのコンテナー内のパスにマウントすることをいいます。 このマウントにより、モジュールはローカル パス (/avrofiles) に書き込むことができ、IoT Edge デバイスからそれらのファイルに直接アクセスできます。

* **ファイルのアップロード:** Avro Writer モジュールは、Azure IoT Hub のファイル アップロード機能を使用して、Azure ストレージ アカウントにファイルをアップロードします。 ファイルが正常にアップロードされると、モジュールはそのファイルをディスクから削除します。

### <a name="create-module-and-copy-files"></a>モジュールを作成してファイルをコピーする

1. Visual Studio Code で、**表示** > **コマンド パレット** を選択し、**Python: Select Interpreter**」と入力します。

1. インストールされている Python バージョン 3.7 以降を選択します。

1. Visual Studio Code で modules フォルダーを右クリックし、 **[Add IoT Edge Module] \(IoT Edge モジュールの追加\)** を選択します。

1. **[Python モジュール]** を選択します。

1. モジュール `avroFileWriter` に名前を付けます。

1. Docker Image Repository の指定を求められたら、Router モジュールの追加時に使用したものと同じレジストリを使用します。

1. サンプル モジュールからソリューションにファイルをコピーします。

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. main.py の上書きに同意します。

1. filemanager.py と schema.py がソリューションに追加され、main.py が更新されていることがわかります。

> [!NOTE]
> Python ファイルを開くと、pylint のインストールを求められる場合があります。 このチュートリアルを完了するために linter をインストールする必要はありません。

### <a name="bind-mount-for-data-files"></a>データ ファイルのバインド マウント

既に述べたように、ライター モジュールは、Avro ファイルをデバイスのファイル システムに書き込むためにバインド マウントの存在に依存しています。

#### <a name="add-directory-to-device"></a>ディレクトリをデバイスに追加する

1. Azure portal で、IoT Edge デバイスの VM を起動します (まだ実行されていない場合)。 そこに SSH を使用して接続してください。 この接続には DNS 名が必要となります。DNS 名は、Azure portal にある VM の概要ページからコピーできます。

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. ログイン後、保存されたリーフ デバイス メッセージを保持するディレクトリを作成します。

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. ディレクトリのアクセス許可を更新して、コンテナーから書き込み可能にします。

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. ユーザー、グループ、所有者による書き込み (w) アクセス許可がディレクトリにあることを確認します。

   ```bash
   ls -la /data
   ```

   ![avrofiles のディレクトリのアクセス許可](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>ディレクトリをモジュールに追加する

ディレクトリをモジュールのコンテナーに追加するために、avroFileWriter モジュールに関連付けられた Dockerfile を修正します。 モジュールに関連付けられている Dockerfile は 3 つあります: Dockerfile.amd64、Dockerfile.amd64.debug、および Dockerfile.arm32v7。 arm32 デバイスがデバッグの対象またはデプロイ先になる場合に備えて、これらのファイルの同期を維持する必要があります。 この記事では Dockerfile.amd64 のみを扱います。

1. 開発用 VM で、**C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64** ファイルを開きます。

1. 次の例のようにファイルを変更します。

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` および `chown` コマンドは、/avrofiles という最上位ディレクトリをイメージ内に作成して moduleuser をそのディレクトリの所有者にするよう、Docker ビルド プロセスに指示します。 これらのコマンドは、`useradd` コマンドでモジュール ユーザーがイメージに追加された後、コンテキストが moduleuser (USER moduleuser) に切り替わる前に挿入することが重要です。

1. 必要であれば、対応する変更を Dockerfile.amd64.debug と Dockerfile.arm32v7 に行います。

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>avroFileWriter にバインド構成を追加する

バインドを作成する最後の手順は、deployment.template.json (および deployment.debug.template.json) ファイルをバインド情報で更新することです。

1. deployment.template.json を開きます。

2. avroFileWriter のモジュール定義を変更します。具体的には、`Binds` パラメーターを追加して、コンテナー ディレクトリ /avrofiles がエッジ デバイス上のローカル ディレクトリをポイントするようにします。 モジュール定義は次の例と一致するはずです。

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>config.yaml へのアクセスのためのバインド マウント

ライター モジュールのバインドをもう 1 つ追加する必要があります。 このバインドにより、IoT Edge デバイス上の /etc/iotedge/config.yaml ファイルから接続文字列を読み取るためのアクセスがモジュールに付与されます。 upload\_blob\_async メソッドを呼び出してファイルを IoT ハブにアップロードできるようにするために、IoTHubClient を作成しますが、それには接続文字列が必要です。 このバインドを追加する手順は、前のセクションの手順と同様です。

#### <a name="update-directory-permission"></a>ディレクトリのアクセス許可を更新する

1. SSH を使用して IoT Edge デバイスに接続します。

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. 読み取りアクセス許可を config.yaml ファイルに追加します。

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. アクセス許可が正しく設定されていることを検証します。

   ```bash
   ls -la /etc/iotedge/
   ```

1. config.yaml のアクセス許可が **-r--r--r--** であることを確認します。

#### <a name="add-directory-to-module"></a>ディレクトリをモジュールに追加する

1. 開発用コンピューターで、**Dockerfile.amd64** ファイルを開きます。

1. `mkdir` および `chown` コマンドの追加セットをファイルに追加して、次のようにします。

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. 対応する変更を Dockerfile.amd64.debug および Dockerfile.arm32v7 に行います。

#### <a name="update-the-module-configuration"></a>モジュールの構成を更新する

1. **deployment.template.json** ファイルを開きます。

1. avroFileWriter のモジュール定義を変更します。具体的には、`Binds` パラメーターに 2 行目を追加して、コンテナー ディレクトリ (/app/iotconfig) が、デバイス上のローカル ディレクトリ (/etc/iotedge) をポイントするようにします。

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. 対応する変更を deployment.debug.template.json に行います。

## <a name="install-dependencies"></a>依存関係のインストール

ライター モジュールは 2 つの Python ライブラリ、fastavro と PyYAML に依存します。 依存関係を開発用コンピューターにインストールし、それらをモジュールのイメージにインストールするよう Docker ビルド プロセスに指示する必要があります。

### <a name="pyyaml"></a>PyYAML

1. 開発マシンで `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` ファイルを開き、新しい行に「pyyaml」を追加します。

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. **Dockerfile.amd64** ファイルを開き、setuptools をアップグレードする `pip install` コマンドを追加します。

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. コマンド プロンプトで、開発マシンに pyyaml をインストールします。

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. requirements.txt で、pyyaml の後に fastavro を追加します。

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. 開発マシンに fastavro をインストールします。

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT Hub を再構成する

IoT Edge デバイスとモジュールをシステムに導入したことで、どのようなデータがどのような目的でハブに送信されるのかに関する期待が変わりました。 新しい現実に対応するために、ハブでのルーティングを再構成する必要があります。

> [!NOTE]
> モジュールをデプロイする前にハブを再構成します。なぜなら、avroFileWriter モジュールを正しく実行するには、一部のハブ設定 (特にファイル アップロード) を正しくセットアップする必要があるからです。

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>IoT Hub で RUL メッセージのルートを設定する

ルーターと分類器の設定が正しければ、デバイス ID と、そのデバイスに対する RUL 予測のみを含んだ通常のメッセージを受信することが期待されます。 デバイスの状態を管理したり、レポートを作成したり、必要に応じて警告を出したりできる独自の格納場所に RUL データをルーティングしたいと考えています。 同時に、まだ IoT Edge デバイスに接続していないリーフ デバイスから直接送信されているデバイス データがまだある場合、それらのデータは引き続き、現在の格納場所にルーティングされるようにしたいと考えています。

#### <a name="create-a-rul-message-route"></a>RUL メッセージ ルートを作成する

1. Azure portal で、お使いの IoT Hub に移動します。

1. 左ペインのメニューの **[メッセージング]** にある **[メッセージ ルーティング]** を選択します。

1. **[ルート]** タブで **[追加]** を選択します。

1. ルートの名前を **RulMessageRoute** にします。

1. **[エンドポイント]** セレクターの右側にある **[エンドポイントの追加]** を選択し、 **[Storage]\(ストレージ\)** を選択します。

1. **[Add a storage endpoint] \(ストレージ エンドポイントの追加\)** ページで、エンドポイントの名前を **ruldata** にします。

1. **[コンテナーを選択します]** を選択します。

1. このチュートリアル全体を通して使用している (**iotedgeandml\<unique suffix\>** などの名前の) ストレージ アカウントを **[ストレージ アカウント]** ページで探します。

1. **ruldata** コンテナーを選択して **[選択]** をクリックします。

1. **[ストレージ エンドポイントの追加]** ページに戻り、 **[作成]** を選択してストレージ エンドポイントを作成します。

1. **[ルートの追加]** ページに戻り、 **[ルーティング クエリ]** の `true` を次のクエリに置き換えます。

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. **[テスト]** セクション、 **[Message body] \(メッセージ本文\)** セクションの順に展開します。 期待されるメッセージの例として示している次の内容に、メッセージ本文を置き換えます。

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. **[Test route] \(ルートのテスト\)** を選択します。 テストが成功した場合、"The message matched the query." (メッセージがクエリに一致しました。) と表示されます。

1. **[保存]** をクリックします。

#### <a name="update-turbofandevicedatatostorage-route"></a>turbofanDeviceDataToStorage ルートを更新する

新しい予測データを古い格納場所にルーティングしたくないので、それを防ぐためにルートを更新します。

1. IoT Hub の **[メッセージ ルーティング]** ページで、 **[ルート]** タブを選択します。

1. **turbofanDeviceDataToStorage**、または初期デバイス データ ルートに付けた別の名前を選択します。

1. ルーティング クエリを次のように更新します。

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. **[テスト]** セクション、 **[Message body] \(メッセージ本文\)** セクションの順に展開します。 期待されるメッセージの例として示している次の内容に、メッセージを置き換えます。

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. **[Test route] \(ルートのテスト\)** を選択します。 テストが成功した場合、"The message matched the query." (メッセージがクエリに一致しました。) と表示されます。

1. **[保存]** を選択します。

### <a name="configure-file-upload"></a>ファイルのアップロードを構成する

IoT Hub のファイル アップロード機能を構成して、ファイル ライター モジュールがファイルをストレージにアップロードできるようにします。

1. IoT Hub の左ペインのメニューから、 **[メッセージング]** の **[ファイルのアップロード]** を選択します。

1. **[Azure ストレージ コンテナー]** を選択します。

1. リストからストレージ アカウントを選択します。

1. **azureml-blobstore** + GUID で始まるコンテナーを選択し、 **[選択]** をクリックします。

1. **[保存]** を選択します。 保存が完了すると、ポータルから通知されます。

> [!Note]
> このチュートリアルではアップロード通知を有効化していませんが、ファイルのアップロード通知を処理する方法の詳細については、「[ファイル アップロードの通知の受信](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification)」を参照してください。

## <a name="build-publish-and-deploy-modules"></a>モジュールをビルド、発行、デプロイする

構成の変更が完了したので、イメージをビルドして Azure コンテナー レジストリに発行する準備ができました。 ビルド プロセスでは deployment.template.json ファイルを使用して、どのモジュールをビルドする必要があるか判断します。 バージョンを含む各モジュールの設定は、モジュール フォルダーの module.json ファイルにあります。 ビルド プロセスではまず、module.json ファイルにある現在の構成に一致する Dockerfile に対して Docker ビルドを実行することでイメージが作成されます。 次に、module.json ファイル内のものと一致するバージョン タグを使用して、module.json ファイルからレジストリにイメージが発行されます。 最後に、構成固有のデプロイ マニフェスト (例: deployment.amd64.json) が生成されるので、これを IoT Edge デバイスにデプロイします。 IoT Edge デバイスはデプロイ マニフェストから情報を読み取り、指示に基づいてモジュールをダウンロードし、ルートを構成し、望ましいプロパティがあれば設定します。 このデプロイ方法には、注意が必要な 2 つの副作用があります。

* **デプロイの遅延:** IoT Edge ランタイムは再構成を開始する前に、その望ましいプロパティへの変更を認識する必要があるため、モジュールをデプロイしてから、ランタイムがそれらを選択して IoT Edge デバイスの更新を開始するまである程度の時間がかかることがあります。

* **モジュールのバージョンの問題:** モジュールのコンテナーの新しいバージョンをコンテナー レジストリに発行するときに、以前のモジュールと同じバージョン タグを使用した場合、ランタイムはモジュールの新しいバージョンをダウンロードしません。 ローカル イメージのバージョン タグと、デプロイ マニフェストでの望ましいイメージが比較されます。 これらのバージョンが一致する場合、ランタイムは何もしません。 したがって、新しい変更をデプロイするたびに、モジュールのバージョンをインクリメントすることは重要です。 バージョンをインクリメントするには、変更しているモジュールの module.json ファイルで **tag** プロパティの下にある **version** プロパティを変更します。 その後、モジュールをビルドして発行します。

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>ビルドして発行する

1. 開発用 VM で Docker を起動します (まだ実行していない場合)。

1. Visual Studio Code で新しいターミナルを起動し、コマンド プロンプトから Azure Container Registry (ACR) にログインします。

  必要なユーザー名、パスワード、ログイン サーバーの値は、Azure portal で確認できます。 コンテナー レジストリは、"turbofandemo\<unique id\>" という形式の名前になっています。 左ペインのメニューの **[設定]** で **[アクセス キー]** を選択すると、それらが表示されます。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Visual Studio Code で、deployment.template.json を右クリックして **[Build and Push IoT Edge Solution] \(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

### <a name="view-modules-in-the-registry"></a>レジストリでモジュールを表示する

ビルドが正常に完了したら、Azure portal を使用して、発行済みモジュールを確認できます。

1. このチュートリアルの Azure Container Registry を開きます。 コンテナー レジストリは、"turbofandemo\<unique id\>" という形式の名前になっています。 

1. 左ペインのメニューの **[サービス]** で **[リポジトリ]** を選択します。

1. 作成した **avrofilewriter** モジュールと **turbofanrouter** モジュールはどちらもリポジトリとして表示されることに注意してください。

1. **turbofanrouter** を選択し、0.0.1-amd64 というタグが付いた 1 つのイメージを発行したことを確認します。

   ![turbofanrouter の最初のタグ付きバージョンを表示する](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>IoT Edge デバイスにモジュールをデプロイする

ソリューションでモジュールをビルドして構成したので、次は、モジュールを IoT Edge デバイスにデプロイします。

1. Visual Studio Code で、config フォルダーにある **deployment.amd64.json** ファイルを右クリックします。

1. **[Create Deployment for Single Device] \(単一デバイスのデプロイの作成\)** を選択します。

1. IoT Edge デバイス **aaTurboFanEdgeDevice** を選択します。

1. Visual Studio Code エクスプローラーで、[Azure IoT Hub devices] (Azure IoT Hub デバイス) パネルを更新します。 3 つの新しいモジュールがデプロイされているがまだ実行されていないことがわかります。

1. 数分後にもう一度更新すると、モジュールが実行されていることがわかります。

   ![実行中モジュールを Visual Studio Code で表示する](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> モジュールが起動して安定した実行状態に落ち着くまで数分かかることがあります。 この期間中、IoT Edge ハブ モジュールとの接続を確立しようとしてモジュールが起動および停止することがあります。

## <a name="diagnosing-failures"></a>障害の診断

このセクションでは、モジュールで発生した問題について理解するための手法をいくつか説明します。 多くの場合、障害はまず Visual Studio Code のステータスから発見できます。

### <a name="identify-failed-modules"></a>障害が発生したモジュールを識別する

* **Visual Studio Code:** [Azure IoT Hub devices] (Azure IoT Hub デバイス) パネルに注目します。 ほとんどのモジュールが実行中の状態で 1 つが停止している場合、その停止しているモジュールをさらに調査する必要があります。 すべてのモジュールが長期間にわたって停止状態になっている場合も、障害を示している可能性があります。

* **Azure portal:** ポータルで IoT ハブに移動してデバイス詳細ページを開く ([IoT Edge] から階層を下って対象のデバイスまで進む) と、モジュールが IoT ハブにエラーを報告したか、それとも何も報告していないかを確認できます。

### <a name="diagnosing-from-the-device"></a>デバイスからの診断

IoT Edge デバイス (このケースでは Linux VM) にログインすると、モジュールの状態に関する多くの情報にアクセスできます。 主に使用するメカニズムは、デバイス上のコンテナーとイメージを検査するための Docker コマンドです。

1. IoT Edge デバイスにログインします。

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. すべての実行中コンテナーを一覧表示します。 期待されるのは、モジュールに対応した名前のコンテナーがモジュールごとに存在することです。 また、このコマンドは、コンテナーの正確なイメージをバージョンを含めて一覧表示するため、期待に合った結果が得られます。 コマンドで "container" の代わりに "image" を使用してイメージを一覧表示することもできます。

   ```bash
   sudo docker container ls
   ```

1. コンテナーのログを取得します。 このコマンドは、コンテナーで StdErr と StdOut に書き込まれたすべての内容を出力します。 このコマンドは、起動したが何らかの理由で異常終了したコンテナーに有効です。 edgeAgent または edgeHub コンテナーで起きていることの理解にも役立ちます。

   ```bash
   sudo docker container logs <container id>
   ```

1. コンテナーを検査します。 このコマンドにより、イメージについて大量の情報が得られます。 目的に応じてデータをフィルター処理できます。 たとえば、avroFileWriter でのバインドが正しいかどうか確認したい場合、次のコマンドを使用できます。

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. 実行中のコンテナーに接続します。 このコマンドは、その実行中にコンテナーを検査したい場合に役立つことがあります。

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルはセットの一部であり、各記事は前の記事の作業が行われたことが前提になっています。 最後のチュートリアルを完了するまで、リソースのクリーンアップはしないでください。

## <a name="next-steps"></a>次のステップ

この記事では、分類器、ルーター、ファイル ライター (アップローダー) の 3 つのモジュールを含む IoT Edge ソリューションを Visual Studio Code で作成しました。 モジュールがエッジ デバイス上で互いに通信できるようにするためのルートを設定したほか、 エッジ デバイスの構成を変更し、また、モジュールのコンテナーに依存関係をインストールしバインド マウントを追加するために Dockerfile を更新しました。 

次に、種類に応じてメッセージをルーティングしたり、ファイルのアップロードを処理したりするために、IoT Hub の構成を更新しました。 すべての準備が整ったので、モジュールを IoT Edge デバイスにデプロイし、モジュールが正しく実行されていることを確認しました。

次の記事に進んでデータの送信を開始し、ソリューションの実際の動作を確認してください。

> [!div class="nextstepaction"]
> [Send data via transparent gateway](tutorial-machine-learning-edge-07-send-data-to-hub.md) (透過的なゲートウェイを介してデータを送信する)