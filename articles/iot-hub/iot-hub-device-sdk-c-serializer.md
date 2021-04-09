---
title: C 用 Azure IoT device SDK – シリアライザー | Microsoft Docs
description: C 用 Azure IoT device SDK でシリアライザー ライブラリを使用し、IoT Hub と通信するデバイス アプリを作成する方法。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f52d1d1c5f264550076688d5e25e110de230eff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152233"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>C 用 Azure IoT device SDK – シリアライザー

このシリーズの最初の記事では、[C 用 Azure IoT device SDK の概要](iot-hub-device-sdk-c-intro.md)を紹介しました。その次の記事では、[C 用 Azure IoT device SDK -- IoTHubClient](iot-hub-device-sdk-c-iothubclient.md) について詳細に説明しました。 今回の記事では、残りのコンポーネントである **シリアライザー** ライブラリについて詳しく説明し、SDK の対応範囲をすべて説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

入門記事では、 **シリアライザー** ライブラリを使用して、IoT Hub にイベントを送信し、IoT Hub からメッセージを受信する方法について説明しました。 この記事では、 **シリアライザー** マクロ言語によるデータのモデル化方法を詳細に説明することで、さらにその内容を深めていきます。 また、ライブラリがメッセージをシリアル化する方法 (場合によっては、シリアル化の動作を制御する方法) についても詳しく説明します。 また、作成するモデルのサイズを決定するいくつかの変更可能なパラメーターについても説明します。

最後に、メッセージとプロパティの処理など、以前の記事で説明したトピックにも、もう一度触れます。 読み進めていくとわかるように、これらの機能は、**シリアライザー** ライブラリを使用する場合でも、**IoTHubClient** ライブラリを使用する場合と同じように動作します。

この記事で説明する内容は、すべて **シリアライザー** SDK サンプルに基づいています。 C 用 Azure IoT device SDK に含まれている **simplesample\_amqp** アプリケーションと **simplesample\_http** アプリケーションを確認すると、理解に役立ちます。

[**C 用 Azure IoT device SDK**](https://github.com/Azure/azure-iot-sdk-c) は GitHub リポジトリから入手でき、API の詳細は [C API リファレンス](/azure/iot-hub/iot-c-sdk-ref/)に関するページで確認できます。

## <a name="the-modeling-language"></a>モデリング言語

このシリーズの [C 用 Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) の記事では、**simplesample\_amqp** アプリケーションで提供されている例を使用して **C 用 Azure IoT device SDK** のモデリング言語を紹介しました。

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

見るとわかるように、モデリング言語は、C マクロに基づいています。 常に、定義は **BEGIN\_NAMESPACE** で始まり、**END\_NAMESPACE** で終了します。 通常は、名前空間には、会社か、またはこの例のように作業しているプロジェクトに関連した名前を付けます。

名前空間の内部で行われるのは、モデル定義です。 この例では、風速計の 1 つのモデルを使用します。 この場合も、モデルには任意の名前を付けることができますが、通常は、デバイスまたは IoT Hub と交換するデータの種類に関する名前を付けます。  

モデルには、IoT Hub に入力できるイベントの定義 (*データ*) と IoT Hub から受信できるメッセージ (*アクション*) の定義が含まれます。 例からわかるように、イベントには、型と名前があります。アクションには、名前と省略可能なパラメーター (それぞれ型を持つ) があります。

このサンプルで示していないのは、SDK がサポートする追加のデータ型です。 これについては、次に説明します。

> [!NOTE]
> デバイスが IoT Hub に送信するデータは、IoT Hub では *イベント* と呼ばれ、モデリング言語では *データ* (**WITH_DATA** を使用して定義) と呼ばれます。 同じように、デバイスに送信するデータは、IoT Hub では *メッセージ* と呼ばれ、モデリング言語では *アクション* (**WITH_ACTION** を使用して定義) と呼ばれます。 この記事では、これらの用語が区別されずに使われる場合があります。
> 
> 

## <a name="supported-data-types"></a>サポートされるデータ型

次のデータの型は、 **シリアライザー** ライブラリを使用して作成されたモデルでサポートされます。

| Type | 説明 |
| --- | --- |
| double |倍精度浮動小数点数 |
| INT |32-bit integer |
| float |単精度浮動小数点数 |
| long |長整数 |
| int8\_t |8 ビット整数 |
| int16\_t |16 ビット整数 |
| int32\_t |32-bit integer |
| int64\_t |64-bit integer |
| [bool] |boolean |
| ascii\_char\_ptr |ASCII 文字列 |
| EDM\_DATE\_TIME\_OFFSET |日時オフセット |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DECLARE\_STRUCT |複合データ型 |

最後のデータ型から説明していきます。 **DECLARE\_STRUCT** を使用すると、複合データ型を定義できます。複合データ型とは、その他のプリミティブ型をグループ化したものです。 これらのグループ化により、次のようなモデルを定義できます。

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

このモデルには、**TestType** 型のデータ イベントが 1 つだけ含まれています。 **TestType** は、複数のメンバーを含む複合型です。これらのメンバー全体が、**シリアライザー** モデリング言語によってサポートされているプリミティブ型を示します。

このようなモデルを使用して、IoT Hub にデータを送信するためのコードを記述できます。以下に例を示します。

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

基本的に、**Test** 構造体の各メンバーに値を割り当ててから、**SendAsync** を呼び出して **Test** データ イベントをクラウドに送信します。 **SendAsync** は、IoT Hub に 1 つのデータ イベントを送信するヘルパー関数です。

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

この関数は、指定されたデータ イベントをシリアル化し、**IoTHubClient\_SendEventAsync** を使用して、そのデータ イベントを IoT Hub に送信します。 これは、以前の記事で説明したコードと同じコードです (**SendAsync** は、ロジックを便利な関数にカプセル化します)。

前のコードで使用されているもう 1 つのヘルパー関数は **GetDateTimeOffset** です。 次のように、この関数は指定された時刻を型 **EDM\_DATE\_TIME\_OFFSET** の値に変換します。

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

このコードを実行すると、次のメッセージが IoT Hub に送信されます。

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

シリアル化は、**シリアライザー** ライブラリによって生成される形式である JSON で行われることに注意してください。 また、シリアル化された JSON オブジェクトの各メンバーとこのモデルで定義した **TestType** のメンバーが一致することにも注意してください。 値も、コードで使用されている値と正確に一致します。 ただし、バイナリ データが Base64 でエンコードされていることに注意してください。"AQID" は {0x01、0x02、0x03} の Base64 エンコードです。

この例から、**シリアライザー** ライブラリを使用する利点がわかります。これにより、アプリケーションでシリアル化を明示的に処理しなくても、クラウドに JSON を送信することができます。 必要なのは、このモデルにデータ イベントの値を設定し、クラウドにそれらのイベントを送信するためにシンプルな API を呼び出すことだけです。

この情報を使用して、複合型など、サポートするデータ型の範囲が含まれるモデルを定義できます (複合型の中に別の複合型を含めることもできます)。 ただし、上の例で生成されるシリアル化された JSON は、重要なポイントを提起します。 *シリアライザー* ライブラリを使用したデータの送信 **シリアライザー** によって、JSON の形式が決定されるということです。 このポイントについては、次で説明します。

## <a name="more-about-serialization"></a>シリアル化の詳細

前のセクションでは、 **シリアライザー** ライブラリによって生成される出力の例を紹介しました。 このセクションでは、ライブラリがデータをシリアル化する方法およびシリアル化 API を使用してその動作を制御する方法について説明します。

シリアル化に関する説明を進めるために、サーモスタットに基づく新しいモデルを使用します。 最初に、扱うシナリオの背景について説明します。

ここでモデル化するのは、気温と湿度を測定するサーモスタットです。 データの各部分は、さまざまな方法で IoT Hub に送信されます。 既定では、サーモスタットは温度イベントを 2 分ごとに受信します。また湿度イベントを 15 分ごとに受信します。 どちらかのイベントが受信されるときに、そのイベントには、対応する温度または湿度の測定時刻を表すタイムスタンプが含まれている必要があります。

このシナリオでは、2 つの異なる方法でデータをモデル化し、シリアル化された出力に対するモデル化の影響について説明します。

### <a name="model-1"></a>モデル 1

ここでは、前のシナリオをサポートするモデルの最初のバージョンを示します。

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

このモデルには、**Temperature** と **Humidity** の 2 つのデータ イベントが含まれていることに注意してください。 これまでの例とは異なり、各イベントの型は、**DECLARE\_STRUCT** を使用して定義された構造体です。 **TemperatureEvent** には温度の測定値とタイムスタンプが含まれており、**HumidityEvent** には湿度の測定値とタイムスタンプが含まれています。 このモデルにより、自然な方法で上のシナリオのデータをモデル化できます。 イベントをクラウドに送信するとき、温度/タイムスタンプのペア、または湿度/タイムスタンプのペアを送信します。

次のようなコードを使用して、クラウドに温度のイベントを送信できます。

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

サンプル コードではハード コーディングされた気温および湿度の値を使用しますが、実際にはサーモスタットの対応するセンサーをサンプリングしてこれらの値を取得することを想像してください。

上のコードでは、以前に紹介した **GetDateTimeOffset** ヘルパーを使用します。 後で理由を説明しますが、このコードは、明示的にイベントのシリアル化とイベントの送信を分離します。 前のコードは、バッファーに温度のイベントをシリアル化します。 次の **sendMessage** は、IoT Hub にイベントを送信するヘルパー関数です (**simplesample\_amqp** に含まれています)。

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

このコードは、前のセクションで説明した **SendAsync** ヘルパーのサブセットであるため、ここでは改めて説明しません。

前のコードを実行して Temperature イベントを送信する場合、このシリアル化されたイベントの形式は、IoT Hub に送信されます。

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

送信されるのは型 **TemperatureEvent** の温度であり、その構造体には **Temperature** および **Time** メンバーが含まれています。 これは、シリアル化されたデータに直接反映されます。

同様に、次のコードで湿度のイベントを送信できます。

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

IoT Hub に送信されるシリアル化された形式は、次のようになります。

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

これも、想定どおりの処理です。

このモデルで、簡単に他のイベントを追加できることがわかります。 **DECLARE\_STRUCT** を使用して追加の構造体を定義し、**WITH\_DATA** を使用して、対応するイベントをモデルに含めます。

今度は、別の構造体を使用して同じデータが含まれるように、モデルを変更します。

### <a name="model-2"></a>モデル 2

上のモデルに代わる、この別のモデルについて考えてみます。

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

この場合は、**DECLARE\_STRUCT** マクロを削除しており、単にモデリング言語の単純型を使用してこのシナリオのデータ項目を定義しています。

しばらくの間は、**Time** イベントを無視します。 それを無視した場合、**Temperature** を受信するコードは次のとおりです。

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

このコードでは、次のシリアル化されたイベントを IoT Hub に送信します。

```C
{"Temperature":75}
```

Humidity イベントを送信するためのコードは、次のようになります。

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

このコードは、イベントを IoT Hub に送信します。

```C
{"Humidity":45}
```

ここまでも、想定どおりの処理です。 今度は、SERIALIZE マクロの使用方法を変更します。

この **SERIALIZE** マクロは、複数のデータ イベントを引数として取得できます。 これにより、1 回の呼び出しで、**Temperature** イベントと **Humidity** イベントを一緒にシリアル化し、IoT Hub に送信できます。

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

このコードを実行すると、2 つのデータ イベントが IoT Hub に送信されると考える人もいるでしょう。

[ {"Temperature":75}, {"Humidity":45} ]

つまり、このコードは **Temperature** と **Humidity** を別々に送信することと同じで、 便宜上、同じ呼び出しで両方のイベントを **SERIALIZE** に渡しているにすぎないという考え方です。 しかし、そうではありません。 上のコードは、次の 1 つのデータ イベントを IoT Hub に送信します。

{"Temperature":75, "Humidity":45}

このことは奇妙に思えるかもしれません。その理由は、モデルでは次のように **Temperature** と **Humidity** が 2 つの *別々の* イベントとして定義されているためです。

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

さらに重要なことに、**Temperature** と **Humidity** が同じ構造体にあるこれらのイベントはモデル化されていません。

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

このモデルを使用すれば、同じシリアル化されたメッセージで **Temperature** と **Humidity** を送信する方法を簡単に理解できます。 ただし、モデル 2 を使用して両方のデータ イベントを **SERIALIZE** に渡す場合にこの方法が機能する理由がわかりにくいと感じる可能性もあります。

この動作は、 **シリアライザー** ライブラリが仮定している条件を理解すると、わかりやすくなります。 このことを理解するために、モデルに話を戻しましょう。

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

モデルをオブジェクト指向の用語を使って考えてみます。 この場合は、物理デバイス (サーモスタット) をモデル化しており、そのデバイスには **Temperature** や **Humidity** などの属性が含まれています。

次のようなコードを使用して、モデル全体の状態を送信できます。

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Temperature、Humidity、Time の値が設定されていると仮定すると、次のようなイベントが IoT Hub に送信されます。

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

場合によっては、モデルの *一部* のプロパティのみをクラウドに送信することもあります (これは、特に、モデルに多数のデータ イベントが含まれている場合に当てはまります)。 そのような場合には、前の例のようにデータ イベントのサブセットのみを送信すると便利です。

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

これにより、モデル 1 のように **Temperature** メンバーと **Time** メンバーを含む **TemperatureEvent** を定義した場合とまったく同じシリアル化されたイベントが生成されます。 この場合は、**SERIALIZE** を別の方法で呼び出したため、別のモデル (モデル 2) を使用して、まったく同じシリアル化されたイベントを生成することができました。

重要な点は、複数のデータ イベントを **SERIALIZE** に渡す場合、各イベントは、1 つの JSON オブジェクトのプロパティと見なされるということです。

最適なアプローチは、モデルについての考え方によって異なります。 "イベント" をクラウドに送信し、定義された一連のプロパティを各イベントに含める場合は、最初のアプローチが非常に役立ちます。 その場合は、**DECLARE\_STRUCT** を使用して各イベントの構造体を定義し、**WITH\_DATA** マクロを使用してモデルに構造体を含めます。 その後、上の最初の例で行ったように各イベントを送信します。 このアプローチでは、**SERIALIZER** に 1 つのデータ イベントだけを渡します。

オブジェクト指向でモデルを考える場合は、2 番目のアプローチが適切である可能性があります。 この場合、**WITH\_DATA** を使用して定義した要素は、オブジェクトの "プロパティ" です。 クラウドに送信する "オブジェクトの" 状態の量に応じて、希望するイベントのサブセットはすべて **SERIALIZE** に渡すことができます。

どちらのアプローチも正しいとか、間違っているとかということはありません。 **シリアライザー** ライブラリのしくみに注意し、ニーズに最適なモデリング アプローチを選択します。

## <a name="message-handling"></a>メッセージの処理

これまで、この記事では IoT Hub への送信についてのみ説明し、メッセージの受信については取り上げてきませんでした。 この理由は、メッセージの受信に関して認識している必要のある内容が [C 用 Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) の記事でほぼ説明されているためです。メッセージを処理するには、メッセージのコールバック関数を登録するということを、その記事から思い出してください。

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

そのうえで、メッセージを受信したときに呼び出されるコールバック関数を記述します。

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

**IoTHubMessage** のこの実装では、モデル内の各アクションに対して特定の関数を呼び出します。 たとえば、モデルで次のアクションが定義されているとします。

```C
WITH_ACTION(SetAirResistance, int, Position)
```

この場合、次のシグネチャを持つ関数を定義する必要があります。

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** は、デバイスにそのメッセージが送信されると呼び出されます。

次のようなメッセージのシリアル化されたバージョンについては、まだ説明していませんでした。 つまり、 **SetAirResistance** メッセージをデバイスに送信する場合、どのような形式になるのかという点です。

デバイスにメッセージを送信する場合、Azure IoT サービス SDK を使用します。 特定のアクションを呼び出すために送信する文字列は知っておく必要があります。 メッセージを送信する一般的な形式は次のようになります。

```C
{"Name" : "", "Parameters" : "" }
```

次の 2 つのプロパティを含むシリアル化された JSON オブジェクトを送信します。**Name** はアクション (メッセージ) の名前であり、**Parameters** にはそのアクションのパラメーターが含まれています。

たとえば、 **SetAirResistance** を呼び出すには、デバイスに次のメッセージを送信できます。

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

アクション名は、モデルで定義されているアクションと正確に一致する必要があります。 パラメーター名も一致する必要があります。 大文字と小文字の区別にも注意してください。 **Name** と **Parameters** は常に大文字です。 モデル内のアクションの名前とパラメーターの大文字と小文字が一致するようにしてください。 この例では、アクションの名前は "SetAirResistance" で、"setairresistance" ではありません。

その他の 2 つのアクション (**TurnFanOn** と **TurnFanOff**) は、デバイスに次のメッセージを送信することによって呼び出すことができます。

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

このセクションでは、 **シリアライザー** ライブラリを使用してイベントを送信し、メッセージを受信する場合に把握する必要がある内容をすべて説明しました。 次に進む前に、モデルの大きさを制御する、構成可能ないくつかのパラメーターについて説明します。

## <a name="macro-configuration"></a>マクロの構成

**シリアライザー** ライブラリを使用する場合に、注意が必要な SDK の重要部分は azure-c-shared-utility ライブラリで参照できます。

GitHub から Azure-iot-sdk-c リポジトリを複製し、`git submodule update --init` コマンドを発行している場合、この共有ユーティリティ ライブラリは次の場所に見つかります。

```C
.\\c-utility
```

ライブラリを複製していない場合は、 [こちら](https://github.com/Azure/azure-c-shared-utility)に見つかります。

共有ユーティリティ ライブラリ内には、次のフォルダーが表示されます:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

このフォルダーには、**macro\_utils\_h\_generator.sln** と呼ばれる Visual Studio ソリューションがあります。

  ![Visual Studio ソリューション maco_utils_h_generator のスクリーンショット](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

このソリューションのプログラムは、**macro\_utils.h** ファイルを生成します。 SDK には、既定の macro\_utils.h ファイルが含まれています。 このソリューションでは、いくつかのパラメーターを変更し、これらのパラメーターに基づいて、ヘッダー ファイルを再作成することができます。

注意すべき 2 つの重要なパラメーターは **nArithmetic** と **nMacroParameters** であり、これらは macro\_utils.tt にある次の 2 行で定義されています。

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

これらの値は、SDK に含まれている既定のパラメーターです。 各パラメーターには、次のような意味があります。

* nMacroParameters – 1 つの DECLARE\_MODEL マクロ定義に使用できるパラメーターの数を制御します。
* nArithmetic – モデルで使用できるメンバーの合計数を制御します。

これらのパラメーターは、モデルの規模を制御するため重要です。 たとえば、次のモデルの定義について考えてみます。

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

前に述べたように、**DECLARE\_MODEL** は単なる C マクロです。 モデルの名前と **WITH\_DATA** ステートメント (別のマクロ) は、**DECLARE\_MODEL** のパラメーターです。 **nMacroParameters** は、**DECLARE\_MODEL** に含めることができるパラメーターの数を定義します。 実質的に、これによって使用可能なデータ イベントおよびアクション宣言の数が定義されます。 124 という既定の制限値の場合、約 60 個のアクションとデータ イベントの組み合わせを使用してモデルを定義できます。 この制限を超えようとすると、次のようなコンパイラ エラーが発生します。

  ![マクロ パラメーター コンパイラ エラーのスクリーンショット](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

**nArithmetic** パラメーターは、アプリケーションよりもマクロ言語の内部動作に関連します。  このパラメーターは、モデルに含めることができるメンバー (**DECLARE_STRUCT** マクロなど) の合計数を制御します。 次のようなコンパイラ エラーが表示されるようになったら、 **nArithmetic** を増やす必要があります。

   ![算術コンパイラ エラーのスクリーンショット](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

これらのパラメーターを変更する場合は、macro\_utils.tt ファイル内の値を変更して、\_utils\_h\_ ソリューションを再コンパイルし、コンパイル済みプログラムを実行します。 この操作を行うと、新しい macro\_utils.h ファイルが生成され、.\\common\\inc ディレクトリに配置されます。

新しいバージョンの macro\_utils.h を使用するには、**シリアライザー** NuGet パッケージをソリューションから削除し、そこに **シリアライザー** Visual Studio プロジェクトを挿入します。 こうすることで、シリアライザー ライブラリのソース コードをコンパイルできるようになります。 これには、更新された macro\_utils.h が含まれます。 **simplesample\_amqp** に対してこの操作を行う場合は、まず、ソリューションからシリアライザー ライブラリの NuGet パッケージを削除します。

   ![シリアライザー ライブラリ用の NuGet パッケージの削除のスクリーンショット](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

その後、Visual Studio ソリューションに次のプロジェクトを追加します。

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

完了すると、ソリューションは次のようになります。

   ![simplesample_amqp Visual Studio ソリューションのスクリーンショット](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

これで、ソリューションをコンパイルすると、更新された macro\_utils.h がバイナリに含まれるようになります。

これらの値を十分に増やすと、コンパイラの制限を超えることができる点に注意してください。 この時点で、**nMacroParameters** が重要なメイン パラメーターになります。 C99 仕様では、マクロ定義内で最低 127 のパラメーターが指定できると規定しています。 Microsoft コンパイラはこの仕様に厳密に従っています (よって 127 という制限があります)。そのため、既定値を超えて **nMacroParameters** を増やすことはできません。 このことが可能なコンパイラもあります (たとえば、GNU コンパイラは、より大きな制限をサポートします)。

ここまでで、 **シリアライザー** ライブラリを使用したコードの記述方法について、知っておくべきすべてのことを説明しました。 説明を終える前に、疑問となりそうな以前の記事のいくつかのトピックについて、もう一度説明します。

## <a name="the-lower-level-apis"></a>下位レベルの API
この記事で詳しく説明したサンプル アプリケーションは、**simplesample\_amqp** です。 このサンプルでは、より高レベルの (**LL** 以外の) API を使用してイベントを送信したり、メッセージを受信したりします。 これらの API を使用する場合、バックグラウンド スレッドが実行され、イベントの送信とメッセージの受信の両方を処理します。 ただし、下位レベル (LL) API を使用してこのバックグラウンド スレッドを排除し、イベントの送信またはクラウドからのメッセージの受信のタイミングを明示的に制御できます。

[前回の記事](iot-hub-device-sdk-c-iothubclient.md)の説明のとおり、以下のように、上位 API で構成される一連の関数があります。

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

これらの API は、**simplesample\_amqp** で示されます。

下位レベル API に類似したセットもあります。

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

下位レベル API が、以前の記事での説明とまったく同じ動作をすることに注意してください。 イベントの送信とメッセージの受信を処理するバックグラウンド スレッドが必要な場合、最初の API のセットを使用できます。 データの送信と IoT Hub からのデータの受信のタイミングを明示的に制御する場合は、2 番目の API のセットを使用します。 どちらの API のセットも **シリアライザー** ライブラリを使用して同じように正しく動作します。

**シリアライザー** ライブラリと一緒に下位レベル API を使用する方法の例については、**simplesample\_http** アプリケーションを参照してください。

## <a name="additional-topics"></a>関連トピック
これ以外に、もう一度説明する必要があるトピックとして、プロパティの処理、代替デバイスの資格情報の使用、および構成オプションがあります。 これらは、すべて [前回の記事](iot-hub-device-sdk-c-iothubclient.md)で説明したトピックです。 要点は、これらの機能はすべて、**シリアライザー** ライブラリを使用する場合でも、**IoTHubClient** ライブラリの場合と同じ動作をするということです。 たとえば、プロパティをモデルのイベントにアタッチする場合、前に説明した方法と同じように、**IoTHubMessage\_Properties** と **Map**\_**AddorUpdate** を使用します。

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

イベントが **シリアライザー** ライブラリにより生成されたか、または **IoTHubClient** ライブラリを使用して手動で作成されたかは関係ありません。

代替デバイスの資格情報については、**IoTHubClient\_LL\_Create** を使用すると **IoTHubClient\_CreateFromConnectionString** と同じように動作して **IOTHUB\_CLIENT\_HANDLE** が割り当てられます。

最後に、**シリアライザー** ライブラリを使用する場合、**IoTHubClient** ライブラリを使用した場合と同様に、**IoTHubClient\_LL\_SetOption** を使用して構成オプションを設定できます。

**シリアライザー** ライブラリに固有の機能の 1 つに、初期化 API があります。 ライブラリの使用を開始できるようにするには、**serializer\_init** を呼び出す必要があります。

```C
serializer_init(NULL);
```

これは、**IoTHubClient\_CreateFromConnectionString** を呼び出す直前に実行されます。

同様に、ライブラリの操作を終了するには、最後に **serializer\_deinit** を呼び出します。

```C
serializer_deinit();
```

それ以外の場合、上に示したその他の機能はすべて、**シリアライザー** ライブラリの場合でも **IoTHubClient** ライブラリの場合と同じように動作します。 これらのいずれかのトピックの詳細については、このシリーズの [前回の記事](iot-hub-device-sdk-c-iothubclient.md) を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、**C 用 Azure IoT device SDK** に含まれている **シリアライザー** ライブラリの独自の側面について詳しく説明しました。説明した情報により、モデルを使用してイベントを送信したり、IoT Hub からメッセージを受信したりする方法についての理解が深まります。

また、この記事は、**C 用 Azure IoT device SDK** を使用したアプリケーションの開発方法に関する 3 部構成のシリーズの最終回でもあります。この記事を読むことで、API の概要だけでなく、API のしくみについて理解するための十分な情報を得ることができます。 追加情報として、ここで取り上げなかった SDK のサンプルがいくつかあります。 それ以外では、[Azure IoT SDK のドキュメント](https://github.com/Azure/azure-iot-sdk-c)が追加情報を入手するための良いリソースになります。

IoT Hub 用の開発の詳細については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

IoT Hub の機能の詳細については、「[Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)」を参照してください。