---
title: C 用 Azure IoT device SDK | Microsoft Docs
description: C 用 Azure IoT device SDK を使用し、IoT Hub と通信するデバイス アプリを作成する方法について説明します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 17b31e365e311b97e322828927827f40733313a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588830"
---
# <a name="azure-iot-device-sdk-for-c"></a>C 用 Azure IoT device SDK

**Azure IoT device SDK** は、**Azure IoT Hub** サービスとのメッセージの送受信プロセスを簡略化するために設計された一連のライブラリです。 SDK にはさまざまなバリエーションがあり、それぞれが特定のプラットフォームを対象としていますが、この記事では、 **C 用 Azure IoT device SDK** について説明します。

> [!NOTE]
> Embedded C SDK は、制約のあるデバイスの代替手段であり、BYON (bring your own network) アプローチをサポートします。 IoT 開発者はデバイス ソリューションを作成するために、任意の MQTT クライアント、TLS、ソケットを自由に使用できます。 [Embedded C SDK の詳細については、こちらを参照してください](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

C 用 Azure IoT デバイス SDK は、移植性を最大限まで高めるために ANSI C (C99) で記述されています。 この機能により、複数のプラットフォームとデバイス、特にディスクとメモリの量を最小限に抑えることが優先される環境でライブラリを操作しやすくなります。

この SDK は幅広いプラットフォームでテストされています (詳細は、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)に関するページを参照してください)。 この記事には、Windows プラットフォームで実行されるサンプル コードのチュートリアルが含まれますが、この記事で説明するコードはサポートされるプラットフォーム全体で同じです。

次のビデオでは、C 用 Azure IoT SDK の概要が紹介されています。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

この記事では、C 用 Azure IoT device SDK のアーキテクチャについて紹介します。また、デバイスのライブラリを初期化する方法、IoT Hub にデータを送信する方法、IoT Hub からメッセージを受信する方法を示します。 この記事の情報は、SDK を使用し始めるにあたり十分な内容ですが、ライブラリに関する追加情報を入手できる場所も紹介します。

## <a name="sdk-architecture"></a>SDK のアーキテクチャ

[**C 用 Azure IoT device SDK**](https://github.com/Azure/azure-iot-sdk-c) は GitHub リポジトリから入手でき、API の詳細は [C API リファレンス](/azure/iot-hub/iot-c-sdk-ref/)に関するページで確認できます。

最新バージョンのライブラリは、このリポジトリの **メイン** ブランチにあります。

  ![リポジトリのメイン ブランチのスクリーンショット](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* SDK の中心となる実装は **iothub\_client** フォルダー内にあり、このフォルダーには SDK 内の最下位の API 層の実装 (**IoTHubClient**) が格納されています。 **IoTHubClient** ライブラリには、IoT Hub とのメッセージの送受信のために、未加工メッセージングを実装する API が含まれています。 このライブラリを使用する場合、メッセージのシリアル化はユーザー自身が実装する必要がありますが、IoT Hub と通信するためのその他の詳細は自動で処理されます。

* **serializer** フォルダーには、クライアント ライブラリを使用して Azure IoT Hub に送信する前にデータをシリアル化する方法を示す、ヘルパー関数とサンプルが含まれています。 シリアライザーの使用は必須ではなく、利便性のために提供されています。 **serializer** ライブラリを使用するには、IoT Hub に送信するデータと IoT Hub から受信するメッセージを指定するモデルを定義します。 いったんモデルを定義すれば、SDK が提供する API にアクセスして、シリアル化の詳細を気にすることなく、デバイスからクラウド、クラウドからデバイスへのメッセージを簡単に処理できます。 ライブラリは、MQTT や AMQP などのプロトコルを使用してトランスポートを実装するその他のオープン ソース ライブラリに依存しています。

* **IoTHubClient** ライブラリは、次に示すその他のオープン ソース ライブラリに依存しています。

  * [Azure C 共有ユーティリティ](https://github.com/Azure/azure-c-shared-utility) ライブラリ。複数の Azure 関連 C SDK で必要となる基本タスク (文字列、リストの操作、IO など) に共通する機能を提供します。

  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) ライブラリ。リソースに制約のあるデバイス用に最適化された AMQP のクライアント側の実装です。

  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) ライブラリ。MQTT プロトコルを実装する汎用ライブラリで、リソースに制約のあるデバイス用に最適化されています。

これらのライブラリの使用については、サンプル コードを確認した方が簡単に理解できます。 次のセクションでは、SDK に含まれているいくつかのサンプル アプリケーションについて説明します。 このチュートリアルは、SDK のアーキテクチャの層のさまざまな機能や、API の動作のしくみの概要を理解するうえで役立ちます。

## <a name="before-you-run-the-samples"></a>サンプルを実行する前に

C 用 Azure IoT device SDK のサンプルを実行する前に、Azure サブスクリプションで [IoT Hub サービスのインスタンスを作成](iot-hub-create-through-portal.md)する必要があります。 そのうえで、次のタスクを実行してください。

* 開発環境を準備する
* デバイスの資格情報を取得する

### <a name="prepare-your-development-environment"></a>開発環境を準備する

パッケージは一般的なプラットフォーム (Windows 用 NuGet、Debian と Ubuntu 用 apt_get など) に提供され、利用可能な場合にサンプルはこれらのパッケージを使用します。 場合によっては、デバイス向けに、またはデバイス上で SDK をコンパイルする必要があります。 SDK をコンパイルする必要がある場合は、GitHub リポジトリで「[Prepare your development environment (開発環境の準備)](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」を参照してください。

サンプル アプリケーション コードを取得するには、GitHub から SDK のコピーをダウンロードします。 [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-c)の **メイン** ブランチからソースのコピーを入手します。


### <a name="obtain-the-device-credentials"></a>デバイスの資格情報を取得する

これでサンプル ソース コートが手に入ったので、次にデバイスの資格情報のセットを取得します。 デバイスを IoT ハブにアクセスできるようにするには、まず、そのデバイスを IoT Hub ID レジストリに追加する必要があります。 デバイスを追加すると、そのデバイスから IoT ハブに接続するために必要な、デバイスの資格情報のセットが得られます。 次のセクションで確認するサンプル アプリケーションでは、これらの資格情報は **デバイスの接続文字列** の形式であると想定しています。

IoT ハブの管理に役立つオープン ソース ツールがいくつかあります。

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) という Windows アプリケーション。

* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) と呼ばれるクロスプラットフォームの Visual Studio Code 拡張機能。

* [Azure CLI 向けの IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)と呼ばれるクロスプラットフォーム Python CLI。

このチュートリアルでは、グラフィカル *デバイス エクスプローラー* ツールを使います。 VS Code で開発する場合は、*VS Code 用の Azure IoT Tools* を使用できます。 CLI ツールを使いたい場合は、"*Azure CLI 2.0 向けの IoT 拡張機能*" ツールを使うこともできます。

デバイス エクスプローラー ツールは、Azure IoT サービス ライブラリを使用して、デバイスの追加など、IoT Hub のさまざまな機能を実行します。 デバイス エクスプローラー ツールを使用してデバイスを追加すると、デバイス用の接続文字列が表示されます。 この接続文字列は、サンプル アプリケーションを実行するために必要です。

デバイス エクスプローラー ツールに慣れていない方のために、次の手順で、デバイス エクスプローラー ツールを使用してデバイスを追加し、デバイスの接続文字列を取得する方法について説明します。

1. デバイス エクスプローラー ツールをインストールするには、「[How to use Device Explorer for IoT Hub devices (IoT Hub デバイス向けにデバイス エクスプローラーを使用する方法)](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/)」を参照してください。

1. プログラムを実行すると、次のインターフェイスが表示されます。

   ![Device Explorer ツインのスクリーンショット](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. **IoT Hub の接続文字列** を最初のフィールドに入力し、 **[更新]** をクリックします。 この手順により、IoT Hub と通信できるようにツールが構成されます。 

**接続文字列** は、 **[IoT Hub サービス]**  >  **[設定]**  >  **[共有アクセス ポリシー]**  >  **[iothubowner]** で確認できます。

1. IoT Hub の接続文字列を構成したら、 **[管理]** タブをクリックします。

   ![Device Explorer ツイン/管理のスクリーンショット](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

このタブで、IoT Hub に登録されたデバイスを管理します。

1. **[作成]** をクリックしてデバイスを作成します。 ダイアログが、一連のキー (プライマリおよびセカンダリ) が入力された状態で表示されます。 **デバイス ID** を入力し、 **[作成]** をクリックします。

   ![デバイス作成のスクリーンショット](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. デバイスが作成されたら、デバイスの一覧が更新され、今作成したデバイスを含むすべての登録済みデバイスが表示されます。 新しいデバイスを右クリックすると、次のメニューが表示されます。

   ![Device Explorer ツインの右クリックの結果](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. **[Copy connection string for selected device (選択したデバイスの接続文字列のコピー)]** を選択すると、デバイスの接続文字列がクリップボードにコピーされます。 デバイス接続文字列のコピーを保存しておきます。 次のセクションで説明するサンプル アプリケーションを実行するときにこれが必要になります。

前の手順が完了すると、コードを実行する準備が整います。 多くのサンプルでは、メインのソース ファイルの上部に、接続文字列を入力するための定数が使用されています。 たとえば、**iothub_client\_samples\_iothub_convenience_sample** アプリケーションの対応する行は次のとおりです。

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>IoTHubClient ライブラリを使用する

[azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) リポジトリ内の **iothub\_client** フォルダーに **samples** フォルダーがあり、**iothub\_client\_sample\_mqtt** というアプリケーションが格納されています。

**iothub_client\_samples\_iothub_convenience_sample** アプリケーションの Windows バージョンには、次の Visual Studio のソリューションが含まれます。

  ![Visual Studio のソリューション エクスプローラー](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Visual Studio で、プロジェクトのターゲットを最新バージョンに変更することを要求するプロンプトが表示された場合は、そのプロンプトを受け入れてください。

このソリューションには単一のプロジェクトが含まれています。 このソリューションには 4 つの NuGet パッケージがインストールされています。

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

SDK を使った作業には必ず **Microsoft.Azure.C.SharedUtility** パッケージが必要となります。 このサンプルでは MQTT プロトコルを使用するため、**Microsoft.Azure.umqtt** パッケージと **Microsoft.Azure.IoTHub.MqttTransport** パッケージを含める必要があります (AMQP と HTTPS にも同等のパッケージが存在します)。 サンプルでは **IoTHubClient** ライブラリが使用されているため、ソリューション内に **Microsoft.Azure.IoTHub.IoTHubClient** パッケージも含める必要があります。

サンプル アプリケーションの実装は、**iothub_client\_samples\_iothub_convenience_sample** ソース ファイル内にあります。

次の手順では、このサンプル アプリケーションを使用して、**IoTHubClient** ライブラリの使用に必要な内容を説明します。

### <a name="initialize-the-library"></a>ライブラリを初期化する

> [!NOTE]
> ライブラリの使用を開始する前に、プラットフォーム固有の初期化の実行が必要になる場合があります。 たとえば、Linux 上で AMQP を使用する計画がある場合は、OpenSSL ライブラリを初期化する必要があります。 [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-c)のサンプルはクライアントの起動時にユーティリティ関数 **platform\_init** を呼び出し、終了する前に **platform\_deinit** 関数を呼び出します。 これらの関数は、platform.h ヘッダー ファイルで宣言されます。 ターゲットとするプラットフォームについて[リポジトリ](https://github.com/Azure/azure-iot-sdk-c)でこれらの関数の定義を確認し、クライアントにいずれかのプラットフォーム固有の初期化コードを含める必要があるかどうかを判断する必要があります。

ライブラリで作業を開始するには、最初に、IoT Hub クライアントのハンドルを割り当てる必要があります。

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

デバイス エクスプローラー ツールから取得したデバイスの接続文字列のコピーをこの関数に渡します。 また、使用する通信プロトコルを指定します。 この例では MQTT を使用しますが、AMQP と HTTPS も使用できます。

有効な **IOTHUB\_CLIENT\_HANDLE** がある場合は、その API の呼び出しを開始して、IoT Hub との間でメッセージを送受信できます。

### <a name="send-messages"></a>メッセージを送信する

このサンプル アプリケーションによって、メッセージを IoT ハブに送信するループがセットアップされます。 次のスニペットが必要です。

- メッセージを作成する。
- プロパティをメッセージに追加する。
- メッセージを送信する。

まず、次のようにメッセージを作成します。

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

メッセージを送信するたびに、データが送信されるときに呼び出されるコールバック関数への参照を指定します。 この例では、コールバック関数は **SendConfirmationCallback** です。 次のスニペットは、このコールバック関数を示しています。

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

メッセージの処理が終了したときに呼び出される **IoTHubMessage\_Destroy** 関数に注意してください。 この関数は、メッセージを作成したときに割り当てられたリソースを解放します。

### <a name="receive-messages"></a>メッセージを受信する

メッセージの受信は非同期操作です。 最初に、デバイスがメッセージを受信したときに呼び出すコールバックを登録します。

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

最後のパラメーターは任意のものを指す void ポインターです。 このサンプルのポインターは整数を指していますが、より複雑なデータ構造を指すこともできます。 このパラメーターにより、このコールバック関数を、この関数の呼び出し元と共有した状態で操作できます。

デバイスがメッセージを受信すると、登録済みのコールバック関数が呼び出されます。 このコールバック関数は次のものを受信します。

* メッセージのメッセージ ID と関連付け ID。
* メッセージのコンテンツ。
* メッセージの任意のカスタム プロパティ。

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

**IoTHubMessage\_GetByteArray** 関数を使用してメッセージを取得します。この例では文字列です。

### <a name="uninitialize-the-library"></a>ライブラリの初期化を解除する

イベントの送信とメッセージの受信が完了すると、IoT ライブラリの初期化を解除することができます。 これを行うには、次の関数呼び出しを発行します。

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

この呼び出しにより、以前に割り当てられていたリソースが、**IoTHubClient\_CreateFromConnectionString** 関数によって解放されます。

ご覧のように、**IoTHubClient** ライブラリを使用すると、簡単にメッセージを送受信できます。 使用するプロトコルなど (開発者の観点からは単純な構成オプションです)、IoT Hub との通信に関する詳細情報は、ライブラリで処理されます。

**IoTHubClient** ライブラリでは、デバイスから IoT Hub に送信されるデータをシリアル化する方法を正確に制御することもできます。 この制御のレベルが利点になる場合もありますが、こうした実装の詳細が不要な場合もあります。 その場合は、次のセクションで説明する **serializer** ライブラリの使用を検討してください。

## <a name="use-the-serializer-library"></a>serializer ライブラリを使用する

概念的には、**serializer** ライブラリは、SDK の **IoTHubClient** ライブラリの上位に位置します。 IoT Hub との基礎になる通信で **IoTHubClient** ライブラリを使用しますが、モデリング機能が追加されるため、開発者がメッセージのシリアル化を処理する負担が削減されます。 このライブラリの動作のわかりやすい例を示します。

[azure-iot-sdk-c リポジトリ](https://github.com/Azure/azure-iot-sdk-c)内の **serializer** フォルダー内には **samples** フォルダーがあり、**simplesample\_mqtt** というアプリケーションが格納されています。 このサンプルの Windows バージョンには、次の Visual Studio のソリューションが含まれます。

  ![mqtt サンプルの Visual Studio ソリューション](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Visual Studio で、プロジェクトのターゲットを最新バージョンに変更することを要求するプロンプトが表示された場合は、そのプロンプトを受け入れてください。

前のサンプルと同様、このソリューションにも、いくつかの NuGet パッケージが含まれています。

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

ほとんどのパッケージは前のサンプルにも含まれていましたが、**Microsoft.Azure.IoTHub.Serializer** を使うのはこれが初めてです。 このパッケージは、**serializer** ライブラリを使用するときに必要です。

サンプル アプリケーションの実装は、**iothub_client\_samples\_iothub_convenience_sample** ファイル内にあります。

次のセクションで、このサンプルの主要な部分について説明します。

### <a name="initialize-the-library"></a>ライブラリを初期化する

**serializer** ライブラリの使用を開始するには、初期化 API を呼び出します。

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

**serializer\_init** 関数の呼び出しは 1 回だけの呼び出しであり、基になるライブラリを初期化します。 次に、**IoTHubClient\_LL\_CreateFromConnectionString** 関数を呼び出します。これは、**IoTHubClient** サンプルに含まれる API と同じものです。 この呼び出しにより、デバイスの接続文字列が設定されます (この呼び出しは、使用するプロトコルを選択する場所でもあります)。 このサンプルでは、トランスポートとして MQTT を使用していますが、AMQP または HTTPS を使用することもできます。

最後に、**CREATE\_MODEL\_INSTANCE** 関数を呼び出します。 **WeatherStation** はモデルの名前空間で、**ContosoAnemometer** はモデルの名前です。 モデルのインスタンスが作成されたら、そのモデルを使用して、メッセージの送受信を開始することができます。 ただし、モデルがどのようなものかを理解する必要があります。

### <a name="define-the-model"></a>モデルを定義する

**serializer** ライブラリ内のモデルは、デバイスから IoT Hub に送信できるメッセージと、モデリング言語で "*アクション*" と呼ばれる、デバイスで受信できるメッセージを定義します。 **iothub_client\_samples\_iothub_convenience_sample** サンプル アプリケーションと同様に、一連の C マクロを使用してモデルを定義します。

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**BEGIN\_NAMESPACE** と **END\_NAMESPACE** の両方のマクロは、モデルの名前空間を引数として取得します。 これらのマクロの間には、モデルの定義とモデルが使用するデータ構造があることが想定されます。

この例では、 **ContosoAnemometer** という単一のモデルがあります。 このモデルは、デバイスが IoT Hub に送信できる、**DeviceId** と **WindSpeed** という 2 つのデータを定義します。 また、デバイスが受信できる、**TurnFanOn**、**TurnFanOff**、および **SetAirResistance** の 3 つのアクション (メッセージ) も定義されます。 各データ要素には型があり、各アクションには名前 (また、必要に応じて一連のパラメーター) があります。

モデルで定義されたデータとアクションは、IoT Hub へのメッセージの送信とデバイスに送信されたメッセージへの応答に使用できる API へのアクセスを定義します。 このモデルの使用については、例を通じて理解するのが一番です。

### <a name="send-messages"></a>メッセージを送信する

このモデルは、IoT Hub に送信できるデータを定義します。 この例では、**WITH_DATA** マクロを使用して定義した 2 つのデータ項目のどちらかを意味します。 **DeviceId** 値と **WindSpeed** 値を IoT ハブに送信するのに必要な手順はいくつかあります。 最初に、送信するデータを設定します。

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

前に定義したモデルでは、**struct** のメンバーを設定して値を設定できます。 次に、送信するメッセージをシリアル化します。

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

このコードは、デバイスからクラウドへのデータをシリアル化して、(**送信先** によって参照される) バッファーに格納します。 次に、**sendMessage** 関数を呼び出して、メッセージを IoT Hub に送信します。

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

**IoTHubClient\_LL\_SendEventAsync** の最後から 2 番目のパラメーターは、データが正常に送信されたときに呼び出されるコールバック関数の参照です。 サンプル内のコールバック関数は次のとおりです。

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

2 番目のパラメーターは、ユーザー コンテキストを指すポインターであり、**IoTHubClient\_LL\_SendEventAsync** に渡したポインターと同じものです。 ここでは、このコンテキストは単純なカウンターですが、任意の内容にすることができます。

デバイスからクラウドへのメッセージの送信に必要なものは以上です。 残りは、メッセージを受信する方法の説明のみです。

### <a name="receive-messages"></a>メッセージを受信する

メッセージの受信は、 **IoTHubClient** ライブラリでメッセージが動作するしくみと同様です。 まず、メッセージのコールバック関数を登録します。

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

そのうえで、メッセージを受信したときに呼び出されるコールバック関数を記述します。

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

このコードは定型句であるため、すべてのソリューションで同じになります。 この関数は、メッセージを受信し、**EXECUTE\_COMMAND** を呼び出して、適切な関数へのルーティングを処理します。 この時点で呼び出す関数は、モデルのアクションの定義によって異なります。

モデルのアクションを定義するとき、対応するメッセージをデバイスが受信するときに呼び出される関数を実装する必要があります。 たとえば、モデルで次のアクションが定義されているとします。

```c
WITH_ACTION(SetAirResistance, int, Position)
```

この場合、次のシグネチャを持つ関数を定義します。

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

関数の名前はモデル内のアクションの名前に一致し、関数のパラメーターはアクションに対して指定されたパラメーターに一致することに注意してください。 最初のパラメーターは常に必要であり、モデルのインスタンスへのポインターが含まれます。

デバイスがこのシグネチャと一致するメッセージを受信すると、対応する関数が呼び出されます。 したがって、 **IoTHubMessage** の定型コードを含める必要があることを除けば、メッセージの受信は、モデルで定義されている各アクションに単純な関数を定義する処理のみで実行できます。

### <a name="uninitialize-the-library"></a>ライブラリの初期化を解除する

データの送信とメッセージの受信が完了すると、次の方法で IoT ライブラリの初期化を解除することができます。

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

この 3 つの各関数は、前に説明した 3 つの初期化関数に対応しています。 これらの API を呼び出すと、以前に割り当てたリソースが確実に解放されます。

## <a name="next-steps"></a>次の手順

この記事では、**C 用 Azure IoT device SDK** のライブラリの使用方法の基本を説明しました。SDK の内容、そのアーキテクチャ、Windows サンプルの実行を開始する方法を理解するうえで十分な情報を提供しました。 次の記事では、 [IoTHubClient ライブラリに関する詳細](iot-hub-device-sdk-c-iothubclient.md)を説明することで、引き続き SDK について記述します。

IoT Hub 用の開発の詳細については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)
