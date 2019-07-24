---
title: 'チュートリアル: カスタム C モジュールを作成する - Azure IoT Edge | Microsoft Docs'
description: このチュートリアルでは、C コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: eeaff4769dba5b6e6951665d09cd12d13f22af07
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273715"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>チュートリアル:C IoT Edge モジュールを開発して、シミュレートされたデバイスに展開する

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Visual Studio Code を使用して C の IoT Edge モジュールを作成する
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、コンテナー レジストリに発行する
> * モジュールを IoT Edge デバイスにデプロイする
> * 生成されたデータを表示する


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) または [Windows デバイス](quickstart.md)用のクイック スタートに記載された手順に従うことで、Azure 仮想マシンを IoT Edge デバイスとして使用できます。 

   >[!TIP]
   >このチュートリアルでは、Visual Studio Code で、Linux コンテナーを使用する C モジュールを開発します。 C for Windows コンテナーで開発する場合は、Visual Studio 2017 を使用する必要があります。 詳細については、「[Visual Studio 2017 を使用して Azure IoT Edge 用のモジュールを開発してデバッグする](how-to-visual-studio-develop-module.md)」を参照してください。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。
* Visual Studio Code 用の [C/C++ 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)。
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* [Docker CE](https://docs.docker.com/install/)。

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
以下の手順では、Visual Studio Code と Azure IoT Tools を使用して、.NET core 2.0 に基づく IoT Edge モジュール プロジェクトを作成する方法を示します。

### <a name="create-a-new-solution"></a>新しいソリューションの作成

独自のコードでカスタマイズできる C ソリューション テンプレートを作成します。

1. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

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

VS Code ウィンドウに、IoT Edge ソリューション ワークスペースと 5 つの上位レベル コンポーネントが表示されます。 **modules** フォルダーには、モジュール用の C コードと、モジュールをコンテナー イメージとして構築するための Dockerfiles が含まれています。 **\.env** ファイルには、コンテナー レジストリの資格情報が格納されています。 **deployment.template.json** ファイルには、デバイスにモジュールをデプロイするために IoT Edge ランタイムが使用する情報が含まれています。 また、**deployment.debug.template.json** ファイルには、モジュールのデバッグ バージョンが含まれています。 このチュートリアルでは、**\.vscode** フォルダーまたは **\.gitignore** ファイルは編集しません。

ソリューションの作成時にコンテナー レジストリを指定せず、既定値の localhost:5000 のままにすると、\.env ファイルは作成されません。

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

1. VS Code エクスプローラーで、.env ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。
3. このファイルを保存します。

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

C モジュールにコードを追加して、報告されたマシンの温度が安全なしきい値を超えているかどうかをチェックできるようにします。 温度が高すぎる場合、モジュールでは、データが IoT Hub に送信される前にメッセージにアラート パラメーターが追加されます。 

1. このシナリオでは、センサーからのデータは JSON 形式で提供されます。 JSON 形式のメッセージをフィルター処理するには、C 用の JSON ライブラリをインポートします。このチュートリアルでは、Parson を使用します。

   1. [Parson GitHub リポジトリ](https://github.com/kgabis/parson)をダウンロードします。 **parson.c** と **parson.h** ファイルを **CModule** フォルダーにコピーします。

   2. **[modules]** > **[CModule]** > **[CMakeLists.txt]** の順に開きます。 ファイルの先頭で、**my_parson** という名前のライブラリとして Parson ファイルをインポートします。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. CMakeLists.txt の **target_link_libraries** 関数内のライブラリの一覧に **my_parson** を追加します。

   4. **CMakeLists.txt** ファイルを保存します。

   5. **[modules]** > **[CModule]** > **[main.c]** の順に開きます。 include ステートメントの一覧の一番下に、JSON サポート用の `parson.h` をインクルードするための新しいステートメントを追加します。

      ```c
      #include "parson.h"
      ```

1. **main.c** ファイルで、include セクションの後に `temperatureThreshold` というグローバル変数を追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。

    ```c
    static double temperatureThreshold = 25;
    ```

1. `CreateMessageInstance` 関数全体を次のコードに置き換えます。 この関数によって、コールバックのコンテキストが割り当てられます。

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

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
        }

        return messageInstance;
    }
    ```

1. `InputQueue1Callback` 関数全体を次のコードに置き換えます。 この関数は、実際のメッセージング フィルターを実装します。

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
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

1. `SetupCallbacksForModule` 関数を次のコードに置き換えます。

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. main.c ファイルを保存します。

1. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 このファイルは、IoT Edge エージェントにどのモジュール (この場合は **tempSensor** および **CModule**) をデプロイするかを指示し、IoT Edge ハブにそれらの間でメッセージをルーティングする方法を指示します。 デプロイ テンプレートで必要なほとんどの情報は Visual Studio Code 拡張機能によって自動的に入力されますが、ソリューションに関してすべての情報が正確であることを確認します。 

   1. IoT Edge の既定のプラットフォームは、VS Code のステータス バーで **amd64** に設定されています。つまり、**CModule** は Linux amd64 バージョンのイメージに設定されています。 IoT Edge デバイスのアーキテクチャに応じて、ステータス バーで既定のプラットフォームを **amd64** から **arm32v7** に変更します。 

      ![モジュール イメージ プラットフォームを更新する](./media/tutorial-c-module/image-platform.png)

   2. テンプレートで、モジュール名が既定の **SampleModule** という名前ではなく、IoT Edge ソリューションの作成時に変更した正しい名前になっていることを確認します。

   3. IoT Edge エージェントがモジュール イメージを取得できるように、**registryCredentials** セクションには Docker レジストリの認証情報が格納されています。 実際のユーザー名とパスワードの組み合わせは、.env ファイル (Git では無視されます) に格納されます。 まだ行っていない場合は、.env ファイルに資格情報を追加します。  

   4. デプロイ マニフェストの詳細については、「[IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します。](module-composition.md)」を参照してください。

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

## <a name="build-and-push-your-solution"></a>ソリューションをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、許容される限度内のマシン温度を報告するメッセージを除外するコードを CModule に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

1. **[表示]** > **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 モジュール イメージをレジストリにプッシュできるように、Azure Container Registry の資格情報でサインインする必要があります。
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   最初のセクションでご自身の Azure コンテナー レジストリからコピーした、ユーザー名、パスワード、およびログイン サーバーを使用します。 または、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから再度取得します。

2. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、**[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

ソリューションのビルドを指示すると、最初に新しい **config** フォルダーに `deployment.json` ファイルが生成されます。 deployment.json ファイルのための情報は、更新したテンプレート ファイル、コンテナー レジストリの資格情報を保存するために使用した .env ファイル、および CModule フォルダー内の module.json ファイルから収集されます。

次に、Visual Studio Code が、`docker build` と `docker push` の 2 つのコマンドを統合ターミナルで実行します。 この 2 つのコマンドによって、ご自身のコードがビルドされ、`CModule.dll` がコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにプッシュされます。

タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで確認できます。 イメージ アドレスは、**\<リポジトリ\>:\<バージョン\>-\<プラットフォーム\>** の形式で、`module.json` ファイルの情報から作成されます。 このチュートリアルでは、**myregistry.azurecr.io/cmodule:0.0.1-amd64** になります。

>[!TIP]
>モジュールをビルドしてプッシュしようとするときにエラーが発生した場合は、次の点を確認してください。
>* Visual Studio Code からお使いのコンテナー レジストリの資格情報を使用して Docker にサインインしているか。 これらの資格情報は、Azure portal にサインインする際に使用する情報とは異なります。
>* コンテナー リポジトリは正しいか。 **[modules]** > **[cmodule]** > **[module.json]** の順に開いて、**[repository]** フィールドを見つけます。 イメージ リポジトリは、**\<registryname\>.azurecr.io/cmodule** のようになっている必要があります。 
>* 開発マシンで実行されているものと同じタイプのコンテナーをビルドしているか。 Visual Studio Code の既定のコンテナーは Linux amd64 です。 お使いの開発マシンで Linux arm32v7 コンテナーが実行されている場合は、実際のコンテナーのプラットフォームに合わせて、VS Code ウィンドウ下部の青色のステータス バーでプラットフォームを更新してください。 C モジュールを Windows コンテナーとしてビルドすることはできません。 

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

IoT Edge デバイスの設定に使用したクイック スタートの記事では、Azure portal を使用してモジュールをデプロイしました。 また、Visual Studio Code 用の Azure IoT Hub Toolkit 拡張機能 (旧称 Azure IoT Toolkit 拡張機能) を使用して、モジュールをデプロイすることもできます。 シナリオ用の配置マニフェストである **deployment.json** ファイルは、既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

1. VS Code コマンド パレットで、**Azure IoT Hub: Select IoT Hub** を実行します。

2. 構成する IoT Edge デバイスが含まれているサブスクリプションと IoT ハブを選択します。

3. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。

4. IoT Edge デバイスの名前を右クリックして、**[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

   ![単一デバイスのデプロイを作成する](./media/tutorial-c-module/create-deployment.png)

5. **config**フォルダーで **deployment.json** ファイルを選択し、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。

6. 更新ボタンをクリックします。 新しい **CModule** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。

IoT Edge デバイスのステータスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを使用して確認できます。 デバイスの詳細を展開すると、デプロイされて実行中のモジュールが一覧表示されます。

IoT Edge デバイス自体では、`iotedge list` コマンドを使用してデプロイ モジュールのステータスを確認できます。 2 つの IoT Edge ランタイム モジュールと tempSensor、さらに、このチュートリアルで作成したカスタム モジュールの 4 つのモジュールが表示されると思います。 すべてのモジュールが開始されるまでには数分かかると考えられます。最初に表示されないモジュールがある場合は、コマンドを再実行してください。

モジュールによって生成されているメッセージを確認するには、`iotedge logs <module name>` コマンドを使用します。

IoT ハブに到着したメッセージは、Visual Studio Code を使用して確認できます。

1. IoT ハブに到着するデータを監視するには、**[...]** をクリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
2. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスを右クリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
3. データの監視を停止するには、コマンド パレットで **Azure IoT Hub:Stop monitoring D2C message** コマンドを実行します。
4. モジュール ツインを表示または更新するには、一覧でモジュールを右クリックし、**[Edit module twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインを更新するには、ツインの JSON ファイルを保存し、編集領域を右クリックして、**[Update Module Twin]\(モジュール ツインの更新\)** を選択します。
5. Docker ログを表示するには、VS Code 用の [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) をインストールし、ローカルで実行されているモジュールを Docker エクスプローラーで見つけます。 コンテキスト メニューで、**[ログを表示する]** をクリックし、統合ターミナルで表示します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code 用の Azure IoT Edge で C モジュールを開発する](how-to-develop-c-module.md)方法の詳細をご覧ください。 引き続き次のチュートリアルを実行し、Azure IoT Edge が、エッジでデータをビジネス上の分析情報に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [SQL Server データベースを使用してエッジでデータを格納する](tutorial-store-data-sql-server.md)

