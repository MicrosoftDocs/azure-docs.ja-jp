---
title: 'チュートリアル: Linux 用の C モジュールを開発する - Azure IoT Edge | Microsoft Docs'
description: このチュートリアルでは、C コードで IoT Edge モジュールを作成し、IoT Edge を実行する Linux デバイスに展開する方法について説明します
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 581d2e03474eb7e740f9d0468022269bdb20b663
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813807"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>チュートリアル:Linux デバイス用の C IoT Edge モジュールを開発する

Visual Studio Code を使用して、Azure IoT Edge を実行している Linux デバイス用の C コードを作成し、展開します。 

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Visual Studio Code を使用して C の IoT Edge モジュールを作成する
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、コンテナー レジストリに発行する
> * モジュールを IoT Edge デバイスにデプロイする
> * 生成されたデータを表示する

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>ソリューション スコープ

このチュートリアルでは、**Visual Studio Code** を使用して **C** でモジュールを開発し、そのモジュールを **Linux デバイス**に展開する方法について説明します。 Windows デバイス用のモジュールを開発する場合は、「[Develop a C IoT Edge module for Windows devices (Windows デバイス用の C IoT Edge モジュールを開発する)](tutorial-c-module-windows.md)」を参照してください。

C モジュールを開発して Linux に展開する際のオプションについては、次の表を使用してください。 

| C | Visual Studio Code | Visual Studio | 
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Linux AMD64 の C モジュール用の VS Code を使用します](./media/tutorial-c-module/green-check.png) | ![Linux AMD64 の C モジュール用の VS を使用します](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Linux ARM32 の C モジュール用の VS Code を使用します](./media/tutorial-c-module/green-check.png) | ![Linux ARM32 の C モジュール用の VS を使用します](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、前のチュートリアルを完了して、Linux コンテナー開発用の開発環境を設定しておく必要があります。[Linux のデバイス用の IoT Edge モジュールを開発する](tutorial-develop-for-linux.md)。 このチュートリアルを完了すると、次の前提条件が満たされます。 

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge を実行している Linux デバイス](quickstart-linux.md)
* コンテナー レジストリ ([Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

C で IoT Edge モジュールを開発するには、開発用マシンに次の追加の前提条件をインストールします。 

* Visual Studio Code 用の [C/C++ 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)。

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

以下の手順では、Visual Studio Code と Azure IoT Tools 拡張機能を使用して、C の IoT Edge モジュール プロジェクトを作成します。 プロジェクト テンプレートが作成されたら、新しいコードを追加し、報告されるプロパティに基づいてモジュールがメッセージをフィルター処理するようにします。 

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる C ソリューション テンプレートを作成します。

1. **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

2. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、手順に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **C モジュール**を選択します。 |
   | Provide a module name (モジュール名の指定) | ご自身のモジュール **CModule** に名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 前の手順で指定した名前がコンテナー イメージに事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br> 最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/cmodule のようになります。 |
 
   ![Docker イメージ リポジトリを指定する](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

1. VS Code エクスプローラーで、.env ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。
3. このファイルを保存します。

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では、Linux AMD64 および Linux ARM32v7 デバイス用の C モジュールを開発できます。 ソリューションごとにターゲットとするアーキテクチャを選択する必要があります。これは、アーキテクチャの種類によって、コンテナーのビルド方法と実行方法が異なるためです。 既定値は Linux AMD64 です。 

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。 

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定値の **amd64** のままにします。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

既定のモジュール コードは、入力キュー上のメッセージを受け取り、出力キューを介してそれらを渡します。 モジュールがメッセージを IoT Hub に転送する前に、エッジでそれらを処理できるように、追加のコードを追加してみましょう。 メッセージごとに温度データを分析し、温度が特定のしきい値を超えた場合にのみ IoT Hub にメッセージを送信するように、モジュールを更新します。 

1. このシナリオでは、センサーからのデータは JSON 形式で提供されます。 JSON 形式のメッセージをフィルター処理するには、C 用の JSON ライブラリをインポートします。このチュートリアルでは、Parson を使用します。

   1. [Parson GitHub リポジトリ](https://github.com/kgabis/parson)をダウンロードします。 **parson.c** と **parson.h** ファイルを **CModule** フォルダーにコピーします。

   2. **[modules]**  >  **[CModule]**  >  **[CMakeLists.txt]** の順に開きます。 ファイルの先頭で、**my_parson** という名前のライブラリとして Parson ファイルをインポートします。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. CMakeLists.txt の **target_link_libraries** 関数内のライブラリの一覧に **my_parson** を追加します。

   4. **CMakeLists.txt** ファイルを保存します。

   5. **[modules]**  >  **[CModule]**  >  **[main.c]** の順に開きます。 include ステートメントの一覧の一番下に、JSON サポート用の `parson.h` をインクルードするための新しいステートメントを追加します。

      ```c
      #include "parson.h"
      ```

1. **main.c** ファイルで、include セクションの後に `temperatureThreshold` というグローバル変数を追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。

    ```c
    static double temperatureThreshold = 25;
    ```

1. main.c で `CreateMessageInstance` 関数を探します。 内部の if-else ステートメントを、数行の機能を追加する次のコードに置き換えます。 

   ```c
       if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
       {
           free(messageInstance);
           messageInstance = NULL;
       }
       else
       {
           messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
           MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
           if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
           {
              printf("ERROR: Map_AddOrUpdate Failed!\r\n");
           }
       }
   ```

   else ステートメント内の新しいコード行は、メッセージに新しいプロパティを追加し、これによりメッセージにアラートのラベルが付けられます。 高温を報告するメッセージのみを IoT Hub に送信する機能を追加するため、このコードでは、すべてのメッセージにアラートのラベルが付けられます。 

1. `InputQueue1Callback` 関数全体を次のコードに置き換えます。 この関数は、実際のメッセージング フィルターを実装します。 メッセージを受信すると、報告された温度がしきい値を超えているかどうかが確認されます。 そうである場合は、出力キューを介してメッセージが転送されます。 そうでない場合、メッセージは無視されます。 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. `moduleTwinCallback` 関数を追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. `SetupCallbacksForModule` 関数を検索します。 この関数を、モジュール ツインが更新されているかどうかを確認する **else if** ステートメントを追加する次のコードに置き換えます。

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. main.c ファイルを保存します。

1. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 

1. CModule モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを `moduleContent` セクションの下部、`$edgeHub` モジュール ツインの後に挿入します。

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![CModule ツインをデプロイ テンプレートに追加する](./media/tutorial-c-module/module-twin.png)

1. **deployment.template.json** ファイルを保存します。

## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、許容される限度内のマシン温度を報告するメッセージを除外するコードを CModule に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

1. **[表示]**  >  **[ターミナル]** を選択して、VS Code ターミナルを開きます。

1. ターミナルで次のコマンドを入力して、Docker にサインインします。 自分の Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用してサインインします。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスを参照してください。

2. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

IoT Edge デバイスにモジュール プロジェクトをデプロイするには、Visual Studio Code エクスプローラーと Azure IoT Tools 拡張機能を使用します。 シナリオ用の配置マニフェストである **deployment.json** ファイルは、config フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。 

1. Visual Studio Code エクスプローラーで **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開して、IoT デバイスの一覧を確認します。

2. IoT Edge デバイスの名前を右クリックして、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

3. **config**フォルダーで **deployment.json** ファイルを選択し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。

4. 更新ボタンをクリックします。 新しい **CModule** が、**SimulatedTemperatureSensor** モジュール、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。

IoT Edge デバイスのステータスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを使用して確認できます。 デバイスの詳細を展開すると、デプロイされて実行中のモジュールが一覧表示されます。

1. Visual Studio Code のエクスプローラーで、IoT Edge デバイスの名前を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

2. 自分の IoT ハブに到着するメッセージを表示します。 IoT Edge デバイスは新しいデプロイを受け取って、すべてのモジュールを開始する必要があるため、メッセージの到着にはしばらく時間がかかる場合があります。 その後、CModule コードに対して行った変更は、コンピューターの温度が 25 度に達するまで待ってからメッセージを送信します。 さらに、その温度しきい値に達するすべてのメッセージにメッセージ型 **Alert** が追加されます。 

   ![IoT Hub に到着するメッセージを表示する](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

配置マニフェストで CModule モジュール ツインを使用して温度のしきい値を 25 度に設定しました。 このモジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio Code で、対象の IoT Edge デバイスの詳細を展開し、実行中のモジュールを表示します。 

2. **[CModule]** を右クリックし、 **[Edit module twin]\(モジュール ツインの編集\)** を選択します。 

3. 目的のプロパティの中から **TemperatureThreshold** を見つけます。 その値を、新しい温度 (最後にレポートされた温度より 5 度から 10 度高い温度) に変更します。 

4. モジュール ツイン ファイルを保存します。

5. モジュール ツイン編集ウィンドウ内の任意の場所を右クリックして、 **[Update module twin]\(モジュール ツインの更新\)** を選択します。 

5. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまでメッセージが停止するのを確認できるはずです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[独自の IoT Edge モジュールの開発](module-development.md)または [Visual Studio Code を使用してモジュールを開発](how-to-vs-code-develop-module.md)する方法の詳細をご確認ください。 次のチュートリアルに進むと、Azure のクラウド サービスをデプロイしてエッジでデータの処理と分析を行ううえで、Azure IoT Edge がどのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)

