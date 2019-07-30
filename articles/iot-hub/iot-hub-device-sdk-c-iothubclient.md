---
title: C 用 Azure IoT device SDK – IoTHubClient | Microsoft Docs
description: C 用 Azure IoT device SDK で IoTHubClient ライブラリを使用し、IoT Hub と通信するデバイス アプリを作成する方法。
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: ff766375dd9ad7cb3bbdf1ef686abb77d1206099
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797864"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>C 用 Azure IoT device SDK – IoTHubClient の詳細

「[C 用 Azure IoT device SDK](iot-hub-device-sdk-c-intro.md)」は、本シリーズの最初の記事で、**C 用 Azure IoT device SDK** を紹介しました。その記事では、SDK に 2 つのアーキテクチャの層が存在することを説明しました。 その基礎となるのが、IoT Hub との通信を直接管理する **IoTHubClient** ライブラリです。 これを土台にしシリアル化サービスを提供する **serializer** ライブラリもあります。 この記事では、**IoTHubClient** ライブラリについてさらに詳しく説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

前の記事では、 **IoTHubClient** ライブラリを使用して IoT Hub にイベントを送信してメッセージを受信する方法を説明しました。 この記事では、 *下位レベルの API* を紹介し、データを送受信する **タイミング**をより厳密に管理する方法について掘り下げて説明します。 **IoTHubClient** ライブラリでプロパティ処理機能を使用してプロパティをイベントに添付する (およびプロパティをメッセージから取得する) 方法についても説明します。 最後に、IoT Hub から受信したメッセージの別の処理方法をいくつか追加で紹介します。

この記事は、デバイスの資格情報の詳細や構成オプションで **IoTHubClient** の動作を変更する方法など、その他のトピックの説明で締めくくります。

**IoTHubClient** SDK のサンプルを使用して、これらのトピックを説明します。 この内容に従って理解するには、C 用 Azure IoT device SDK に含まれている **iothub\_client\_sample\_http** と **iothub\_client\_sample\_amqp** の各アプリケーションを確認してください。以降のセクションで説明する内容はすべて、これらのサンプルで例示します。

[**C 用 Azure IoT device SDK**](https://github.com/Azure/azure-iot-sdk-c) は GitHub リポジトリから入手でき、API の詳細は [C API リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)に関するページで確認できます。

## <a name="the-lower-level-apis"></a>下位レベルの API

前の記事では、**iothub\_client\_sample\_amqp** アプリケーションのコンテキスト内の **IotHubClient** の基本的な操作を説明しました。 たとえば、次のコードを使用してライブラリを初期化する方法について説明しました。

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

次の関数呼び出しを使用してイベントを送信する方法についても説明しました。

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

この記事では、コールバック関数を登録することでメッセージを受信する方法についても説明しました。

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

この記事では、次のようなコードを使用してリソースを解放する方法も示しました。

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

これらの API にはそれぞれ次のコンパニオン関数があります。

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

これらの関数の API 名にはすべて **LL** が使用されています。 名前の **LL**の部分以外は、これらの各関数のパラメーターは、LL が付いていないその対応するものと同じです。 ただし、これらの関数の動作は、1 つの重要な点で異なります。

**IoTHubClient\_CreateFromConnectionString** を呼び出すと、基になるライブラリは、バックグラウンドで実行される新しいスレッドを作成します。 このスレッドは、IoT Hub にイベントを送信し、IoT Hub ハブからメッセージを受信します。 **LL** API を使用するときは、このようなスレッドは作成されません。 バックグラウンド スレッドが作成されるのは、開発者にとって便利です。 IoT Hub とのイベントの送信とメッセージの受信を明示的に実行することについて心配する必要はありません。自動的にバックグラウンドで実行されます。 これに対して、**LL** API では、必要に応じて IoT Hub との通信を明示的に制御することができます。

この概念の理解を深めるために、例を確認してみましょう。

**IoTHubClient\_SendEventAsync** を呼び出したとき、実際に実行されるのは、バッファー内へのイベントの配置です。 **IoTHubClient\_CreateFromConnectionString** を呼び出したときに作成されたバックグラウンド スレッドは、このバッファーを監視し続け、このバッファーに含まれるすべてのデータを IoT Hub に送信します。 これは、メイン スレッドが他の作業を実行しているときに同時にバックグラウンドで実行されます。

**IoTHubClient\_SetMessageCallback** を使用してメッセージのコールバック関数を登録するときと同様に、メッセージを受信したときに、メイン スレッドから独立して、バックグラウンド スレッドからコールバック関数を呼び出すよう SDK に指示することになります。

**LL** API では、バックグラウンド スレッドは作成されません。 代わりに、新しい API を呼び出し、明示的にデータを送信して IoT Hub からデータを受信する必要があります。 これを次の例に示します。

SDK に含まれる **iothub\_client\_sample\_http** アプリケーションは、下位レベルの API の例を示しています。 そのサンプルでは、次のようなコードを使用して、イベントを IoT Hub に送信します。

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

最初の 3 行でメッセージを作成して、最後の 1 行でイベントを送信します。 ただし、先ほど説明したように、イベントの送信は、データが単にバッファーに配置されることを意味します。 **IoTHubClient\_LL\_SendEventAsync** を呼び出しても、ネットワークには何も転送されません。 実際にデータを IoT Hub に入力するには、次の例のように **IoTHubClient\_LL\_DoWork** を呼び出す必要があります。

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

(**iothub\_client\_sample\_http** アプリケーションの) このコードは、**IoTHubClient\_LL\_DoWork** を繰り返し呼び出します。 **IoTHubClient\_LL\_DoWork** を呼び出すたびに、バッファーから IoT Hub にいくつかのイベントが送信され、キューに登録されているデバイスへの送信対象のメッセージが取得されます。 後者では、メッセージのコールバック関数が登録されている場合、(メッセージがキューに登録されていれば) そのコールバック関数が呼び出されます。 次のようなコードを使用して、そのようなコールバック関数を登録しています。

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

**IoTHubClient\_LL\_DoWork** が多くの場合ループ処理で呼び出される理由は、呼び出されるたびに、バッファリングされた*いくつかの*イベントを IoT Hub に送信し、キューに登録された*次の*デバイス用メッセージを取得するためです。 呼び出すたびに、バッファリングされたすべてのイベントが送信され、キューに登録されたすべてのメッセージが取得されるという保証はありません。 バッファー内のすべてのイベントを送信してから他の処理を続けるには、前のループ処理を次のようなコードで置き換えます。

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

このコードは、バッファー内のすべてのイベントが IoT Hub に送信されるまで **IoTHubClient\_LL\_DoWork** を呼び出します。 これは、キューのすべてのメッセージが受信されたことを意味するわけではありません。 その理由の 1 つは、すべてのメッセージを確認することは、決定性のあるアクションではないためです。 メッセージをすべて取得した直後に、別のものがデバイスに送信された場合を考えてください。 より優れた処理方法に、プログラムでタイムアウトを設定する方法があります。 たとえば、メッセージのコールバック関数を使用すると、呼び出すたびに、タイマーをリセットできます。 ロジックを記述して、直近 *X* 秒に受信したメッセージがない場合などに、処理を続行することができます。

イベントの入力とメッセージの受信が完了したら、該当する関数を必ず呼び出してリソースをクリーンアップしてください。

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

基本的に、バックグラウンド スレッドを使用してデータを送受信する API のセットが 1 つのみと、バックグラウンド スレッドを使用せず同じ動作を実行する別の API のセットがあります。 多くの開発者は LL でない API を好みますが、下位レベルの API は開発者がネットワーク転送を明示的に制御する場合に便利です。 たとえば、一部のデバイスは時間をかけてデータを収集しますが、指定された間隔 (1 時間に 1 回や 1 日に 1 回など) でのみイベントを入力します。 下位レベルの API を使用すると、IoT Hub からデータを送受信するタイミングを明確に制御できるようになります。 下位レベルの API が提供する単純さを好む開発者もいるでしょう。 バック グラウンドでいくつかの動作が行われるのではなく、すべてがメイン スレッドで発生します。

どちらのモデルを選択するにしても、使用する API に一貫性を持たせる必要があります。 最初に **IoTHubClient\_LL\_CreateFromConnectionString** を呼び出す場合は、後続のすべての作業でも、対応する下位レベルの API のみを使用してください。

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

逆の場合も同様です。 最初に **IoTHubClient\_CreateFromConnectionString** を呼び出す場合は、その他の処理でも LL でない API を常に使用します。

C 用 Azure IoT device SDK における下位レベルの API の完全な例は **iothub\_client\_sample\_http** アプリケーションで確認してください。 **iothub\_client\_sample\_amqp** アプリケーションでは、LL でない API の完全な例について参照できます。

## <a name="property-handling"></a>プロパティの処理

これまでデータ送信の説明の際は、メッセージの本文に言及してきました。 たとえば、次のコードを検討してみましょう。

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

この例では、テキスト "Hello World" を使用して IoT Hub にメッセージを送信します。 ただし、IoT Hub では、各メッセージにプロパティを添付することもできます。 プロパティは、名前と値のペアのことで、メッセージに添付することができます。 たとえば、前のコードを変更して、メッセージにプロパティを添付することができます。

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

まず、**IoTHubMessage\_Properties** を呼び出してメッセージのハンドルを渡します。 返されるのは、プロパティを追加することができる **MAP\_HANDLE** 参照です。 最後に、MAP\_HANDLE への参照、プロパティ名、プロパティ値を取得する **Map\_AddOrUpdate** を呼び出します。 この API を使用すると、必要な数のプロパティを追加できます。

イベントが **Event Hub**から読み込まれると、受信側はプロパティを列挙して対応する値を取得することができます。 たとえば、.NET では、 [EventData オブジェクトのプロパティ コレクション](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx)にアクセスしてこれを実行することができます。

前の例では、IoT Hub に送信するイベントにプロパティを添付しています。 プロパティは IoT Hub から受信するメッセージに添付することもできます。 メッセージからプロパティを取得する必要がある場合は、メッセージのコールバック関数で次のようなコードを使用できます。

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

**IoTHubMessage\_Properties** の呼び出しにより、**MAP\_HANDLE** 参照が返されます。 その後、この参照を **Map\_GetInternals** に渡して、(プロパティの数だけでなく) 名前と値ペアの配列への参照を取得します。 この時点で、プロパティを列挙して必要な値を取得するだけの簡単な処理です。

アプリケーションでプロパティを使用する必要はありません。 ただし、イベントに設定したりメッセージから取得したりする必要がある場合は、 **IoTHubClient** ライブラリを使用すると簡単に実行できます。

## <a name="message-handling"></a>メッセージの処理

前に説明したように、IoT Hub からメッセージが到着すると、 **IoTHubClient** ライブラリが登録済みのコールバック関数を呼び出すことで応答します。 この関数には追加で説明する必要がある戻り値のパラメーターがあります。 次に、**iothub\_client\_sample\_http** サンプル アプリケーション内のコールバック関数の抜粋を示します。

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

戻り値の型が **IOTHUBMESSAGE\_DISPOSITION\_RESULT** であり、この場合は **IOTHUBMESSAGE\_ACCEPTED** を返すことに注目してください。 **IoTHubClient** ライブラリがメッセージのコールバックに対する反応を変える、この関数から返すことができる値が他にもあります。 返される値は次のとおりです。

* **IOTHUBMESSAGE\_ACCEPTED**: メッセージが正常に処理されました。 **IoTHubClient** ライブラリは、同じメッセージでコールバック関数を再度呼び出すことはありません。

* **IOTHUBMESSAGE\_REJECTED**: メッセージは処理されませんでした。今後これが実行される可能性はありません。 **IoTHubClient** ライブラリは、同じメッセージでコールバック関数を再度呼び出すことはありません。

* **IOTHUBMESSAGE\_ABANDONED**: メッセージが正常に処理されませんでしたが、同じメッセージを使用して **IoTHubClient** ライブラリはもう一度コールバック関数を呼び出す必要があります。

最初の 2 つのリターン コードでは、 **IoTHubClient** ライブラリが、メッセージをデバイスのキューから削除して、再度配信されないようにする必要があることを示すメッセージを IoT Hub に送信します。 実質的な効果は同じ (デバイスのキューからメッセージを削除する) ですが、メッセージが受け入れられるか拒否するかも記録されます。  この違いを記録することは、メッセージの送信側がフィードバックに注目し、デバイスが特定のメッセージを受け入れるか拒否するかを調べる際に役立ちます。

最後の場合もメッセージは IoT Hub に送信されますが、これは、メッセージを再配信する必要があることを示しています。 いくつかのエラーが発生したがメッセージをもう一度処理する必要がある場合、通常はメッセージを破棄します。 これに対して、回復不可能なエラーが発生した場合 (またはメッセージを処理しないことを単に決定した場合) は、メッセージを拒否するのは適切なことです。

いずれの場合も、 **IoTHubClient** ライブラリから目的の動作を導き出せるように、さまざまなリターン コードがあることのみ知っておいてください。

## <a name="alternate-device-credentials"></a>代替デバイスの資格情報

前に説明したように、**IoTHubClient** ライブラリを使用するときに最初に実行する必要があるのは、次のように、呼び出しで **IOTHUB\_CLIENT\_HANDLE** を取得することです。

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

**IoTHubClient\_CreateFromConnectionString** の引数は、デバイスの接続文字列と、IoT Hub との通信に使用するプロトコルを示すパラメーターです。 デバイスの接続文字列の形式は次のとおりです。

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

この文字列には、IoT Hub 名、IoT Hub サフィックス、デバイス ID、共有アクセス キーの 4 つの情報が含まれています。 Azure ポータルで IoT Hub インスタンスを作成するときに、IoT Hub の完全修飾ドメイン名 (FQDN) を取得します。これにより、IoT Hub 名 (FQDN の最初の部分) と IoT Hub サフィックス (FQDN の残りの部分) を取得できます。 デバイス ID と共有アクセス キーは、([前の記事](iot-hub-device-sdk-c-intro.md)の説明のように) IoT Hub にデバイスを登録するときに取得します。

**IoTHubClient\_CreateFromConnectionString** は、ライブラリを初期化する方法の 1 つです。 必要に応じて、デバイスの接続文字列ではなくこれらの個々のパラメーターを使用して、新しい **IOTHUB\_CLIENT\_HANDLE** を作成できます。 これは次のコードで実現します。

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

これは、**IoTHubClient\_CreateFromConnectionString** と同じ処理を実行します。

このような冗長な初期化方法ではなく、**IoTHubClient\_CreateFromConnectionString** を使用しようと考えるのは当然のことと言えます。 ただし、IoT Hub にデバイスを登録するときに取得するのは、(接続文字列ではなく) デバイス ID とデバイス キーであることを留意してください。 [前の記事](iot-hub-device-sdk-c-intro.md)で紹介されている*デバイス エクスプローラー* SDK ツールは、**Azure IoT サービス SDK** のライブラリを使用して、デバイス ID、デバイス キー、IoT Hub ホスト名からデバイスの接続文字列を作成します。 そのため、**IoTHubClient\_LL\_Create** の呼び出しは、接続文字列を生成する手順を省略できるという理由で好まれる可能性があります。 いずれかの便利な方法を使用してください。

## <a name="configuration-options"></a>構成オプション

これまでに説明した **IoTHubClient** ライブラリの動作にはすべて、その既定の動作が反映されています。 このライブラリの動作を変更するために設定できるオプションがいくつかあります。 これは、**IoTHubClient\_LL\_SetOption** API を利用することで実行されます。 次の例を考えてみましょう。

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

よく使用されるオプションがいくつかあります。

* **SetBatching** (ブール値): **true** の場合、IoT Hub に送信されるデータはバッチで送信されます。 **false** の場合は、メッセージは個別に送信されます。 既定値は **false** です。 AMQP や AMQP-WS によるバッチ処理と D2C メッセージへのシステム プロパティの追加がサポートされています。

* **Timeout** (符号なし整数): この値はミリ秒単位で表現されます。 HTTPS 要求の送信や応答の受信にこの時間より長くかかる場合は、接続がタイムアウトします。

バッチ処理オプションは重要です。 既定では、このライブラリはイベントを個別に入力します (**IoTHubClient\_LL\_SendEventAsync** に渡したものが 1 つのイベントになります)。 バッチ処理オプションが **true**の場合、ライブラリはバッファーから可能な限りの (IoT Hub が許容する最大メッセージ サイズまでの) イベントを収集します。  イベントのバッチは、(個々のイベントが JSON 配列にまとめられて) 単一の HTTPS 呼び出しで IoT Hub に送信されます。 バッチ処理を有効にすると、通常はネットワーク ラウンドトリップが減少するため、パフォーマンスの大幅な向上につながります。 個別の各イベントの一連のヘッダーではなく、イベント バッチで一連の HTTPS ヘッダーが送信されるため、帯域幅が大幅に削減されます。 それ以外の方法で実行する特別な理由がない限り、一般的にバッチ処理を有効にします。

## <a name="next-steps"></a>次の手順

この記事は、**C 用 Azure IoT device SDK** にある **IoTHubClient** ライブラリの動作の詳細を説明しました。この情報は、**IoTHubClient** ライブラリの機能の理解に役立ててください。 このシリーズの 2 番目の記事は「[C 用 Azure IoT device SDK - シリアライザー](iot-hub-device-sdk-c-serializer.md)」です。よく似ている**シリアライザー** ライブラリについて詳しく説明します。

IoT Hub 用の開発の詳細については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

IoT Hub の機能の詳細については、「[Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)」を参照してください。
