---
title: チュートリアル - Azure IoT Edge を使用して Windows 用の C モジュールを開発する
description: このチュートリアルでは、C コードで IoT Edge モジュールを作成し、IoT Edge を実行している Windows デバイスにそれらを展開する方法について説明します。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8f019c8f3c560fdfdc0c8e5992389c253c9b0d74
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463376"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>チュートリアル: Windows コンテナーを使用して C の IoT Edge モジュールを開発する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、Visual Studio を使用して C コードを開発し、Azure IoT Edge を実行している Windows デバイスに展開する方法について説明します。

>[!NOTE]
>IoT Edge 1.1 LTS は、Windows コンテナーをサポートする最後のリリース チャネルです。 バージョン 1.2 以降では、Windows コンテナーはサポートされません。 Windows デバイスで IoT Edge を実行するには、[IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用またはこちらへの移行を検討してください。

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Visual Studio を使用して C SDK に基づく IoT Edge モジュールを作成する。
> * Visual Studio と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 温度が指定されたしきい値を超えた場合にのみ、モジュールからアップストリームにメッセージが送信されます。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Visual Studio 2019 を使用して C でモジュールを開発し、それを Windows デバイスに展開する方法について説明します。 Linux コンテナーを使用してモジュールを開発する場合は、「[Linux コンテナーを使用して C の IoT Edge モジュールを開発する](tutorial-csharp-module.md)」を参照してください。

Windows コンテナーを使用して C モジュールを開発してデプロイする際のオプションについては、次の表を参照してください。

| C | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;および&nbsp;2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Visual Studio で WinAMD64 用の C モジュールを開発する](./media/tutorial-c-module/green-check.png) |

このチュートリアルを開始する前に、チュートリアル「[Windows コンテナーを使用して IoT Edge モジュールを開発する](tutorial-develop-for-windows.md)」の手順に従って開発環境を設定します。 これを完了すると、環境には次の前提条件が含まれます。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge を実行している Windows デバイス](how-to-install-iot-edge-windows-on-windows.md)。
* コンテナー レジストリ ([Azure Container Registry](../container-registry/index.yml) など)。
* [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 拡張機能で構成された [Visual Studio 2019](/visualstudio/install/install-visual-studio)。
* Windows コンテナーを実行するように構成された [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。

次のコマンドを実行して、vcpkg を使って Windows x64 用の Azure IoT C SDK をインストールします。

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Visual Studio 2017 (バージョン 15.7 以降) を使用している場合は、[Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) から Visual Studio 2017 用の Azure IoT Edge Tools をダウンロードしてインストールしてください。

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

このセクションでは、Visual Studio と Azure IoT Edge Tools 拡張機能を使用して、C SDK に基づく IoT Edge モジュール プロジェクトを作成します。 プロジェクト テンプレートを作成したら、レポートされたプロパティに基づいてモジュールがメッセージを除外するように、新しいコードを追加します。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる C ソリューション テンプレートを作成します。

1. Visual Studio 2019 を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** ペインで、**IoT Edge** を検索し、結果リストで **[Azure IoT Edge (Windows amd64)]** プロジェクトを選択します。

   ![IoT Edge の [新しいプロジェクトの作成] ペインのスクリーンショット。](./media/tutorial-c-module-windows/new-project.png)

1. **[次へ]** を選択します。

    **[新しいプロジェクトの構成]** ペインが開きます。

   ![[新しいプロジェクトの構成] ペインのスクリーンショット。](./media/tutorial-c-module-windows/configure-project.png)

1. **[新しいプロジェクトの構成]** ペインで、プロジェクトとソリューションの名前を、**CTutorialApp** のようなわかりやすい名前に変更します。 

1. **[作成]** を選択してプロジェクトを作成します。

   **[モジュールの追加]** ペインが開きます。

   ![プロジェクトを構成するための [モジュールの追加] ペインのスクリーンショット。](./media/tutorial-c-module-windows/add-application-and-module.png)

1. **[新しいプロジェクトの構成]** ページで、次を実行します。

   a. 左ペインで、 **[C Module]\(C モジュール\)** テンプレートを選択します。  
   b. **[モジュール名]** ボックスに「**CModule**」と入力します。  
   c. **[リポジトリ URL]** ボックスで、**localhost:5000** を、Azure コンテナー レジストリの **ログイン サーバー** の値 (`<registry name>.azurecr.io/cmodule` の形式) に置き換えます。

    > [!NOTE]
    > イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、モジュール プロジェクト名の値から事前に入力されています。  ログイン サーバーは、Azure portal のコンテナー レジストリの概要ページから取得できます。

1. **[追加]** を選択してプロジェクトを作成します。

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

配置マニフェストは、コンテナー レジストリの資格情報を IoT Edge ランタイムと共有します。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 該当する Azure コンテナー レジストリの **[アクセス キー]** セクションの資格情報を使用します。

1. Visual Studio ソリューション エクスプローラーで、*deployment.template.json* ファイルを開きます。

1. $edgeAgent の必要なプロパティで、**registryCredentials** プロパティを探します。 このプロパティのレジストリ アドレスには、プロジェクトの作成時に指定した情報が自動入力されます。 ユーザー名とパスワードのフィールドには変数名が含まれています。 次に例を示します。

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. 自分のモジュール ソリューションで環境 (ENV) ファイルを開きます。 このファイルは、既定ではソリューション エクスプローラーで非表示になっているので、表示するために、 **[すべてのファイルを表示]** ボタンを選択することが必要な場合があります。 ENV ファイルには、*deployment.template.json* ファイルで確認したのと同じユーザー名とパスワードの変数が含まれている必要があります。

1. Azure コンテナー レジストリからの **Username** と **Password** の値を追加します。

1. 変更内容を ENV ファイルに保存します。

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

既定のモジュール コードでは、入力キューでメッセージを受け取り、出力キューを介してそれらを渡します。 モジュールがメッセージを IoT ハブに転送する前にエッジで処理するように、コードを追加しましょう。 各メッセージの温度データを分析し、温度が特定のしきい値を超えた場合にのみ IoT ハブにメッセージを送信するようにモジュールを更新します。

1. このシナリオでは、センサーからのデータは JSON 形式で提供されます。 JSON 形式のメッセージをフィルター処理するには、C 用の JSON ライブラリをインポートします。このチュートリアルでは、Parson を使用します。

   a. [Parson GitHub リポジトリ](https://github.com/kgabis/parson)をダウンロードします。  
   b. *parson.c* および *parson.h* ファイルを CModule プロジェクトにコピーします。  
   c. Visual Studio で、CModule プロジェクト フォルダーの *CMakeLists.txt* ファイルを開きます。  
   d. ファイルの先頭で、**my_parson** という名前のライブラリとして Parson ファイルをインポートします。

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. *CMakeLists.txt* ファイルの "target_link_libraries" セクションにあるライブラリのリストに `my_parson` を追加します。  
   f. *CMakeLists.txt* ファイルを保存します。  
   g. **[CModule]**  >  **[main.c]** の順に選択します。 include ステートメントのリストの一番下に、JSON サポート用の `parson.h` をインクルードするための新しいステートメントを追加します。

      ```c
      #include "parson.h"
      ```

1. *main.c* ファイルで、`messagesReceivedByInput1Queue` 変数の横に `temperatureThreshold` というグローバル変数を追加します。 この変数により、温度のしきい値が設定されます。これを測定温度が超えると、データが IoT ハブに送信されます。

    ```c
    static double temperatureThreshold = 25;
    ```

1. *main.c* で `CreateMessageInstance` 関数を見つけます。 内部の *if-else* ステートメントを、数行の機能を追加する次のコードに置き換えます。

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

   *else* ステートメント内の新しいコード行では、メッセージにアラートのラベルを付ける新しいプロパティをメッセージに追加します。 高温がレポートされた場合にのみ、メッセージを IoT ハブに送信する機能を追加するため、このコードでは、すべてのメッセージにアラートのラベルを付けます。

1. `InputQueue1Callback` 関数を見つけ、その関数全体を次のコードに置き換えます。 この関数は、実際のメッセージング フィルターを実装します。 メッセージを受信すると、報告された温度がしきい値を超えているかどうかが確認されます。 温度がしきい値を超えている場合は、出力キューを介してメッセージが転送されます。 しきい値を超えていない場合、メッセージは無視されます。

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. `moduleTwinCallback` 関数を追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあります。これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

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

1. `SetupCallbacksForModule` 関数を探します。 この関数を次のコードに置き換えます。ここでは、モジュール ツインが更新されているかどうかを確認する *else if* ステートメントを追加しています。

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

1. *main.c* ファイルを保存します。

1. *deployment.template.json* ファイルを開きます。

1. **CModule** モジュール ツインを展開マニフェストに追加します。 次の JSON コンテンツを `moduleContent` セクションの下部、`$edgeHub` モジュール ツインの後に挿入します。

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![展開テンプレートに追加されるモジュール ツインを示すスクリーンショット。](./media/tutorial-c-module-windows/module-twin.png)

1. *deployment.template.json* ファイルを保存します。

## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、レポートされたマシンの温度が許容されるしきい値を下回っているメッセージを除外するコードを **CModule** に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

### <a name="sign-in-to-docker"></a>Docker にサインインする

コンテナー イメージをレジストリにプッシュして格納できるように、開発マシン上の Docker にコンテナー レジストリの資格情報を入力します。

1. PowerShell またはコマンド プロンプト ウィンドウを開きます。

1. レジストリを作成した後に保存した Azure コンテナー レジストリの資格情報を使用して Docker にサインインします。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 これは、運用環境のシナリオではベスト プラクティスとして推奨されていますが、このチュートリアルの範囲外になります。 詳細については、[docker login リファレンス](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)を参照してください。

### <a name="build-and-push"></a>ビルドとプッシュ

これで開発マシンはコンテナー レジストリにアクセスできるようになり、ご使用の IoT Edge デバイスもアクセスできるようになりました。 次に、プロジェクト コードをコンテナー イメージに変換します。

1. Visual Studio ソリューション エクスプローラーで、ビルドしたいプロジェクトの名前を右クリックします。 既定の名前は **AzureIotEdgeApp1** です。 このチュートリアルでは、名前を **CTutorialApp** にしました。Windows モジュールをビルドするので、拡張子は **Windows.Amd64** になります。

1. **[IoT Edge モジュールをビルドしてプッシュする]\(Build and Push IoT Edge Modules\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。
   * まず、*config* という名前の新しいフォルダーをソリューション内に作成します。そこに完全な展開マニフェストが保持されます。 これは、展開テンプレートと他のソリューション ファイルの情報からビルドされます。 
   * 次に、`docker build` を実行して、ターゲット アーキテクチャの適切な Dockerfile に基づいてコンテナー イメージをビルドします。 
   * 最後に、`docker push` を実行して、イメージ リポジトリを自分のコンテナー レジストリにプッシュします。

   このプロセスは、初回は数分かかることがありますが、次回これらのコマンドを実行するときには、それより速くなります。

## <a name="deploy-modules-to-the-device"></a>モジュールをデバイスに展開する

IoT Edge デバイスにモジュール プロジェクトをデプロイするには、Visual Studio Cloud Explorer と Azure IoT Edge Tools 拡張機能を使用します。 お客様のシナリオの展開マニフェストである *deployment.windows-amd64.json* ファイルは、*config* フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。

1. Visual Studio Cloud Explorer で、リソースを展開して IoT デバイスの一覧を表示します。

1. デプロイを受け取る IoT Edge デバイスの名前を右クリックします。

1. **[デプロイの作成]** を選択します。

1. Visual Studio エクスプローラーで、自分のソリューションの *config* フォルダーにある *deployment.windows-amd64.json* ファイルを選択します。

1. Cloud Explorer を最新の情報に更新して、展開されたモジュールが自分のデバイスとして一覧表示されていることを確認します。

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに展開マニフェストを適用すると、デバイス上の IoT Edge ランタイムによって新しい展開情報が収集され、そこで実行が開始されます。 デバイスで実行されていても、展開マニフェストに含まれていないモジュールはすべて停止されます。 デバイスから不足しているモジュールはすべて開始されます。

IoT Edge Tools 拡張機能を使用すると、自分の IoT ハブに到着したメッセージを表示できます。

1. Visual Studio Cloud Explorer で、お使いの IoT Edge デバイスの名前を選択します。

1. **[操作]** の一覧で、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

1. 自分の IoT ハブに到着するメッセージを表示します。 IoT Edge デバイスは、新しい展開を受け取り、すべてのモジュールを開始する必要があるため、メッセージが到着するまでにしばらく時間がかかる場合があります。 CModule コードの変更により、マシンの温度が 25 度に達するまで待ってから、メッセージを送信する必要があります。 また、その温度しきい値に達したメッセージには、メッセージの種類として **Alert** が追加されます。

   ![IoT ハブに到着するメッセージを表示する出力ウィンドウのスクリーンショット。](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

CModule モジュール ツインを使用して、温度しきい値を 25 度に設定しました。 モジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio で、*deployment.windows-amd64.json* ファイルを開きます。 

   *deployment.template* ファイルは "*開かない*" でください。 ソリューション エクスプローラーで *config* ファイルに展開マニフェストが表示されない場合は、ソリューション エクスプローラーのツール バーで **[すべてのファイルを表示]** アイコンを選択します。

1. CModule ツインを探し、**temperatureThreshold** パラメーターの値を、レポートされた最新の温度より 5 から 10 度高い新しい温度に変更します。

1. *deployment.windows-amd64.json* ファイルを保存します。

1. もう一度展開手順に従って、更新された展開マニフェストをデバイスに適用します。

1. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまで、メッセージは停止されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、このチュートリアルで作成したリソースと構成を保持して再利用できます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

それ以外の場合は、課金されないようにするために、ローカル構成と、ここで使用した Azure リソースを削除できます。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。

Azure クラウド サービスをデプロイしてエッジでデータの処理と分析を行う際に、Azure IoT Edge がどのように役立つかを確認するために、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
