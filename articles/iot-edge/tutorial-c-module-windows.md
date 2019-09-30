---
title: 'チュートリアル: Windows 用の C モジュールを開発する - Azure IoT Edge| Microsoft Docs'
description: このチュートリアルでは、C コードで IoT Edge モジュールを作成して、IoT Edge を実行する Windows デバイスにデプロイする方法について説明します
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: b69000e251bb71bc08ea97b24a7422daf2cd89e4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813816"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>チュートリアル:Windows デバイス用の C IoT Edge モジュールを開発する

Visual Studio を使用して C コードを開発して、Azure IoT Edge を実行している Windows デバイスにデプロイします。 

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 このチュートリアルでは、以下の内容を学習します。    

> [!div class="checklist"]
> * Visual Studio を使用して C SDK に基づく IoT Edge モジュールを作成する。
> * Visual Studio と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>ソリューション スコープ

このチュートリアルでは、**Visual Studio 2019** を使用して **C** でモジュールを開発し、それを **Windows デバイス**にデプロイする方法について説明します。 Linux デバイス用のモジュールを開発する場合は、代わりに、[Linux デバイス用の C IoT Edge モジュールの開発](tutorial-c-module.md)に関する記事をご覧ください。 

次の表を使用し、C モジュールを開発して Windows にデプロイする際のオプションをご確認ください。 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Visual Studio で WinAMD64 用の C モジュールを開発する](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、前のチュートリアルを完了して、Windows コンテナー開発用の開発環境を設定している必要があります。[Windows デバイス用の IoT Edge モジュールを開発する](tutorial-develop-for-windows.md) そのチュートリアルを完了すると、以下の前提条件が満たされます。 

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge を実行している Windows デバイス](quickstart.md)
* コンテナー レジストリ ([Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) など)。
* [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 拡張機能が構成された [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。
* Windows コンテナーを実行するように構成された [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。
* vcpkg を使用して Windows x64 用の Azure IoT C SDK をインストールします:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Visual Studio 2017 (バージョン 15.7 以降) を使用している場合は、Visual Studio Marketplace から VS 2017 用の [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) をダウンロードしてインストールしてください

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

以下の手順では、Visual Studio と Azure IoT Edge Tools 拡張機能を使用して、C SDK に基づく IoT Edge モジュール プロジェクトを作成します。 プロジェクト テンプレートが作成されたら、新しいコードを追加して、報告されたプロパティに基づいてモジュールがメッセージをフィルター処理するようにします。 

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる C ソリューション テンプレートを作成します。

1. Visual Studio 2019 を起動し、 **[新しいプロジェクトの作成]** を選択します。

2. 新しいプロジェクトのウィンドウで、 **[IoT Edge]** プロジェクトを検索し、 **[Azure IoT Edge (Windows amd64)]** プロジェクトを選択します。 **[次へ]** をクリックします。 

   ![新しい Azure IoT Edge プロジェクトを作成する](./media/tutorial-c-module-windows/new-project.png)

3. [新しいプロジェクトを構成します] ウィンドウで、プロジェクトとソリューションを、**CTutorialApp** のように、わかりやすい名前に変更します。 **[作成]** をクリックしてプロジェクトを作成します。 

   ![新しい Azure IoT Edge プロジェクトを構成する](./media/tutorial-c-module-windows/configure-project.png)

4. IoT Edge アプリケーションとモジュールのウィンドウで、次の値を使用してプロジェクトを構成します。 

   | フィールド | 値 |
   | ----- | ----- |
   | テンプレートの選択 | **[C モジュール]** を選択します。 | 
   | モジュール プロジェクト名 | ご自身のモジュール **CModule** に名前を付けます。 | 
   | Docker イメージ リポジトリ | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、モジュール プロジェクト名の値から事前に入力されています。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br> 最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/cmodule のようになります。 |

   ![ターゲット デバイス、モジュールの種類、コンテナー レジストリ用にプロジェクトを構成する](./media/tutorial-c-module-windows/add-application-and-module.png)

5. **[OK]** を選択して変更を適用します。 

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

配置マニフェストは、コンテナー レジストリの資格情報を IoT Edge ランタイムと共有します。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 該当する Azure コンテナー レジストリの **[アクセス キー]** セクションの資格情報を使用します。 

1. Visual Studio ソリューション エクスプローラーで、**deployment.template.json** ファイルを開きます。 

2. $edgeAgent の必要なプロパティで、**registryCredentials** プロパティを見つけます。 

3. 次の形式に従って、自分の資格情報でプロパティを更新します。 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. deployment.template.json ファイルを保存します。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

既定のモジュール コードは、入力キュー上のメッセージを受け取り、出力キューを介してそれらを渡します。 モジュールがメッセージを IoT Hub に転送する前に、エッジでそれらを処理できるように、追加のコードを追加してみましょう。 メッセージごとに温度データを分析し、温度が特定のしきい値を超えた場合にのみ IoT Hub にメッセージを送信するように、モジュールを更新します。 


1. このシナリオでは、センサーからのデータは JSON 形式で提供されます。 JSON 形式のメッセージをフィルター処理するには、C 用の JSON ライブラリをインポートします。このチュートリアルでは、Parson を使用します。

   1. [Parson GitHub リポジトリ](https://github.com/kgabis/parson)をダウンロードします。 **parson.c** と **parson.h** ファイルを **CModule** プロジェクトにコピーします。

   2. Visual Studio で、CModule プロジェクト フォルダーの **CMakeLists.txt** ファイルを開きます。 ファイルの先頭で、**my_parson** という名前のライブラリとして Parson ファイルをインポートします。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. CMakeLists.txt ファイルの **target_link_libraries** セクション内のライブラリの一覧に **my_parson** を追加します。

   4. **CMakeLists.txt** ファイルを保存します。

   5. **[CModule]**  >  **[main.c]** の順に開きます。 include ステートメントの一覧の一番下に、JSON サポート用の `parson.h` をインクルードするための新しいステートメントを追加します。

      ```c
      #include "parson.h"
      ```

2.  **main.c** ファイルで、messagesReceivedByInput1Queue 変数の横に `temperatureThreshold` というグローバル変数を追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。

    ```c
    static double temperatureThreshold = 25;
    ```

3. main.c で `CreateMessageInstance` 関数を探します。 内部の if-else ステートメントを、数行の機能を追加する次のコードに置き換えます。 

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

   else ステートメント内の新しいコード行は、メッセージに新しいプロパティを追加し、これによりメッセージにアラートのラベルが付けられます。 高温を報告する場合に IoT Hub に単にメッセージを送信する機能を追加するため、このコードはすべてのメッセージにアラートのラベルを付けます。 

4. `InputQueue1Callback` 関数を見つけ、その関数全体を次のコードに置き換えます。 この関数は、実際のメッセージング フィルターを実装します。 メッセージを受信すると、報告された温度がしきい値を超えているかどうかが確認されます。 そうである場合は、出力キューを介してメッセージが転送されます。 そうでない場合、メッセージは無視されます。 

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

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. `moduleTwinCallback` 関数を追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

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

6. `SetupCallbacksForModule` 関数を検索します。 この関数を、モジュール ツインが更新されているかどうかを確認する **else if** ステートメントを追加する次のコードに置き換えます。 

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

7. main.c ファイルを保存します。

8. **deployment.template.json** ファイルを開きます。 

9. CModule モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを `moduleContent` セクションの下部、`$edgeHub` モジュール ツインの後に挿入します。

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![CModule ツインをデプロイ テンプレートに追加する](./media/tutorial-c-module-windows/module-twin.png)

1. **deployment.template.json** ファイルを保存します。

## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションで、IoT Edge ソリューションを作成し、報告されたマシンの温度が許容しきい値を下回るメッセージをフィルターに掛けて除去するコードを **CModule** に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。 

1. 次のコマンドを使用して、開発マシンで Docker にサインインします。 Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用してサインインしてください。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスをご覧ください。

2. Visual Studio のソリューション エクスプローラーで、ビルドするプロジェクト名を右クリックします。 既定の名前は **AzureIotEdgeApp1** です。そして、Windows モジュールをビルドするので、拡張子は **Windows.Amd64** になります。 

3. **[IoT Edge モジュールをビルドしてプッシュする]\(Build and Push IoT Edge Modules\)** を選択します。 

   ビルドおよびプッシュ コマンドは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。 

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

IoT Edge デバイスにモジュール プロジェクトをデプロイするには、Visual Studio Cloud Explorer と Azure IoT Edge Tools 拡張機能を使用します。 このシナリオ用の配置マニフェストである **deployment.json** ファイルは、config フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。 

1. Visual Studio Cloud Explorer でリソースを展開して、IoT デバイスの一覧を表示します。 

2. デプロイを受け取る IoT Edge デバイスの名前を右クリックします。 

3. **[デプロイの作成]** を選択します。

4. ファイル エクスプローラーで、ソリューションの config フォルダー内にある **deployment.windows-amd64** ファイルを選択します。 

5. クラウド エクスプローラーを更新して、デプロイされたモジュールがデバイスの下に一覧表示されていることを確認します。 


## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。 

IoT Edge Tools 拡張機能を使用すると、IoT Hub に到着したメッセージを表示することができます。 

1. Visual Studio Cloud Explorer で、お使いの IoT Edge デバイスの名前を選択します。 

2. **[操作]** の一覧で、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。 

3. 自分の IoT ハブに到着するメッセージを表示します。 IoT Edge デバイスは新しいデプロイを受け取って、すべてのモジュールを開始する必要があるため、メッセージの到着にはしばらく時間がかかる場合があります。 その後、CModule コードに対して行った変更は、コンピューターの温度が 25 度に達するまで待ってからメッセージを送信します。 さらに、その温度しきい値に達するすべてのメッセージにメッセージ型 **Alert** が追加されます。 

   ![IoT Hub に到着するメッセージを表示する](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

CModule モジュール ツインを使用して、温度しきい値を 25 度に設定しました。 モジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio で、**deployment.windows-amd64.json** ファイルを開きます。 (deployment.template ファイルではありません。 ソリューション エクスプローラーの config ファイルに配置マニフェストが表示されない場合は、エクスプローラーのツールバーで **[すべてのファイルを表示]** アイコンを選択します。)

2. CModule ツインを見つけ、**temperatureThreshold** パラメーターの値を、報告された最新の温度より 5 度から 10 度高い新規の温度に変更します。 

3. **deployment.windows-amd64.json** ファイルを保存します。

4. もう一度デプロイ手順に従って、更新された配置マニフェストをお使いのデバイスに適用します。 

5. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまでメッセージが停止するのを確認できるはずです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[独自の IoT Edge モジュールの開発](module-development.md)または [Visual Studio を使用したモジュールの開発](how-to-visual-studio-develop-module.md)に関する詳細について確認することができます。 次のチュートリアルに進み、Azure Cloud Services をデプロイしてエッジでデータを処理および分析するうえで、Azure IoT Edge がどのように役立つかを確認することができます。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
