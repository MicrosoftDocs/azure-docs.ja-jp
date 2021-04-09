---
title: IoT プラグ アンド プレイ ブリッジのアダプターを作成する方法 | Microsoft Docs
description: IoT プラグ アンド プレイ ブリッジ アダプター コンポーネントを識別します。 独自のアダプターを作成してブリッジを拡張する方法について説明します。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746811"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジの拡張
[IoT プラグ アンド プレイ ブリッジ](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture)を使用すると、ゲートウェイに接続されている既存のデバイスを IoT Hub に接続できます。 ブリッジを使用して、接続されているデバイスに IoT プラグ アンド プレイ インターフェイスをマップします。 IoT プラグ アンド プレイ インターフェイスでは、デバイスによって送信されるテレメトリ、デバイスとクラウド間で同期されるプロパティ、およびデバイスによって応答されるコマンドが定義されます。 オープンソースのブリッジ アプリケーションを Windows または Linux のゲートウェイにインストールして構成することができます。 さらに、ブリッジを Azure IoT Edge ランタイム モジュールとして実行できます。

この記事では、以下の方法について説明します。

- アダプターを使用して IoT プラグ アンド プレイ ブリッジを拡張します。
- ブリッジ アダプターの共通コールバックを実装します。

ブリッジの使用方法を示す簡単な例については、「[Linux または Windows 上で実行されている IoT プラグ アンド プレイ ブリッジのサンプルを IoT Hub に接続する方法](howto-use-iot-pnp-bridge.md)」を参照してください。

この記事のガイダンスとサンプルでは、[Azure Digital Twins](../digital-twins/overview.md) と [IoT プラグ アンド プレイ](overview-iot-plug-and-play.md)に関する基本的な知識があることを前提としています。 また、この記事では、[IoT プラグ アンド プレイ ブリッジのビルドとデプロイ](howto-build-deploy-extend-pnp-bridge.md)方法について理解していることを前提としています。

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>アダプターを使用して IoT プラグ アンド プレイ ブリッジを拡張するための設計ガイド

ブリッジの機能を拡張するために、独自のブリッジ アダプターを作成することができます。

ブリッジでは、アダプターを使用して次のことが行われます。

- デバイスとクラウドの間の接続が確立されます。
- デバイスとクラウドの間のデータ フローが有効にされます。
- クラウドからのデバイス管理が有効になります。

すべてのブリッジ アダプターでは次のことが行われる必要があります。

- デジタル ツイン インターフェイスが作成されます。
- そのインターフェイスを使用して、デバイス側の機能がクラウドベースの機能 (テレメトリ、プロパティ、コマンドなど) にバインドされます。
- デバイスのハードウェアまたはファームウェアを使用して、コントロールとデータの通信が確立されます。

各ブリッジ アダプターでは、アダプターがデバイスに接続され、デバイスと通信する方法に基づいて、特定の種類のデバイスとやり取りします。 デバイスとの通信でハンドシェイク プロトコルが使用されている場合でも、デバイスからのデータを解釈する方法がブリッジ アダプターに複数あることがあります。 このシナリオでは、ブリッジ アダプターによって構成ファイル内のアダプターの情報が使用され、アダプターでデータを解析するために使用される "*インターフェイスの構成*" が決定されます。

デバイスとやり取りするために、ブリッジ アダプターでは、デバイスでサポートされる通信プロトコルと、基になるオペレーティング システムまたはデバイス ベンダーによって提供される API が使用されます。

クラウドとやり取りするために、ブリッジ アダプターでは、Azure IoT Device C SDK によって提供される API が使用され、テレメトリの送信、デジタル ツイン インターフェイスの作成、プロパティの更新の送信、およびプロパティの更新とコマンドのコールバック関数の作成が行われます。

### <a name="create-a-bridge-adapter"></a>ブリッジ アダプターを作成する

ブリッジには、[_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) インターフェイスに定義されている API を実装するブリッジ アダプターが必要です。

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

このインターフェイスでは:

- `PNPBRIDGE_ADAPTER_CREATE` によってアダプターが作成され、インターフェイス管理リソースがセットアップされます。 アダプターでは、アダプターの作成にグローバル アダプター パラメーターを使用することもできます。 この関数は、1 つのアダプターに対して 1 回呼び出されます。
- `PNPBRIDGE_COMPONENT_CREATE` によってデジタル ツイン クライアント インターフェイスが作成され、コールバック関数がバインドされます。 アダプターでは、デバイスへの通信チャネルが開始されます。 アダプターでは、テレメトリ フローを有効にするためにリソースがセットアップされることがありますが、`PNPBRIDGE_COMPONENT_START` が呼び出されるまでテレメトリの報告は開始されません。 この関数は、構成ファイル内のインターフェイス コンポーネントごとに 1 回呼び出されます。
- ブリッジ アダプターでデバイスからデジタル ツイン クライアントへのテレメトリの転送を開始できるようにするために、`PNPBRIDGE_COMPONENT_START` が呼び出されます。 この関数は、構成ファイル内のインターフェイス コンポーネントごとに 1 回呼び出されます。
- `PNPBRIDGE_COMPONENT_STOP` によってテレメトリ フローが停止されます。
- `PNPBRIDGE_COMPONENT_DESTROY` によってデジタル ツイン クライアントおよび関連するインターフェイス リソースが破棄されます。 この関数は、ブリッジが破棄されたとき、または致命的なエラーが発生したときに、構成ファイル内のインターフェイス コンポーネントごとに 1 回呼び出されます。
- `PNPBRIDGE_ADAPTER_DESTROY` によってブリッジ アダプターのリソースがクリーンアップされます。

### <a name="bridge-core-interaction-with-bridge-adapters"></a>ブリッジ コアとブリッジ アダプターのやり取り

ブリッジが開始されたときの動作の概要を次の一覧に示します。

1. ブリッジが開始されると、ブリッジ アダプター マネージャーによって、構成ファイルで定義されている各インターフェイス コンポーネントが参照され、適切なアダプターで `PNPBRIDGE_ADAPTER_CREATE` が呼び出されます。 アダプターでは、グローバル アダプター構成パラメーターを使用して、さまざまな "*インターフェイス構成*" がサポートされるようにリソースを設定できます。
1. 構成ファイル内のすべてのデバイスについて、ブリッジ マネージャーによって、適切なブリッジ アダプターで `PNPBRIDGE_COMPONENT_CREATE` が呼び出され、インターフェイスの作成が開始されます。
1. アダプターで、インターフェイス コンポーネントのオプションのアダプター構成設定が受信され、この情報を使用してデバイスへの接続が設定されます。
1. アダプターによって、デジタル ツイン クライアント インターフェイスが作成され、プロパティの更新とコマンドのコールバック関数がバインドされます。 デジタル ツイン インターフェイスの作成が成功した後に、デバイスの接続を確立することによってコールバックの戻り値をブロックしないようにしてください。 アクティブなデバイスの接続は、ブリッジによって作成されるアクティブなインターフェイス クライアントから独立しています。 接続に失敗した場合、アダプターではデバイスが非アクティブであると想定されます。 ブリッジ アダプターでは、この接続の再試行を選択できます。
1. ブリッジ アダプター マネージャーでは、構成ファイルで指定されたすべてのインターフェイス コンポーネントを作成した後、すべてのインターフェイスが Azure IoT Hub に登録されます。 登録はブロックされた非同期呼び出しです。 この呼び出しが完了すると、ブリッジ アダプターでコールバックがトリガーされ、クラウドからのプロパティとコマンドのコールバックの処理を開始できるようになります。
1. その後、ブリッジ アダプター マネージャーによって各コンポーネントで `PNPBRIDGE_INTERFACE_START` が呼び出され、ブリッジ アダプターでデジタル ツイン クライアントへのテレメトリの報告が開始されます。

### <a name="design-guidelines"></a>設計ガイドライン

新しいブリッジ アダプターを開発するときは、次のガイドラインに従うようにしてください。

- サポートされるデバイスの機能と、このアダプターを使用するコンポーネントのインターフェイス定義の概要を決定します。
- アダプターで構成ファイルに定義する必要のあるインターフェイスとグローバル パラメーターを決定します。
- コンポーネントのプロパティとコマンドをサポートするために必要な低レベルのデバイス通信を識別します。
- アダプターでデバイスからの生データが解析され、IoT プラグ アンド プレイ インターフェイスの定義によって指定されたテレメトリの種類に変換される方法を決定します。
- 前に説明したブリッジ アダプター インターフェイスを実装します。
- アダプター マニフェストに新しいアダプターを追加し、ブリッジをビルドします。

### <a name="enable-a-new-bridge-adapter"></a>新しいブリッジ アダプターを有効にする

[adapter_manifest.c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c) に参照を追加して、ブリッジでアダプターを有効にします。

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> ブリッジ アダプターのコールバックは順番に呼び出されます。 ブリッジ コアで処理を続行できなくなるため、アダプターでコールバックがブロックされないようにしてください。

### <a name="sample-camera-adapter"></a>カメラ アダプターのサンプル

[カメラ アダプターの readme](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) では、有効にできるサンプルのカメラ アダプターについて説明しています。

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>一般的なアダプターのシナリオまたはコールバックのコード例

次のセクションでは、ブリッジのアダプターがいくつかの一般的なシナリオおよび使用方法のためにコールバックを実装する方法について詳しく説明します。このセクションでは、次のコールバックについて説明します。
- [受信プロパティの更新 (クラウドからデバイス)](#receive-property-update-cloud-to-device)
- [プロパティ更新の報告 (デバイスからクラウド)](#report-a-property-update-device-to-cloud)
- [テレメトリの送信 (デバイスからクラウド)](#send-telemetry-device-to-cloud)
- [クラウドからのコマンド更新コールバックの受信およびデバイス側での処理 (クラウドからデバイス)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [デバイス側のコマンド更新への応答 (デバイスからクラウド)](#respond-to-command-update-on-the-device-side-device-to-cloud)

次の例は、[環境センサーのサンプル アダプター](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor)に基づいています。

### <a name="receive-property-update-cloud-to-device"></a>受信プロパティの更新 (クラウドからデバイス)
最初の手順では、コールバック関数を登録します。

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
次の手順では、デバイスのプロパティの更新を読み取るコールバック関数を実装します。

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>プロパティ更新の報告 (デバイスからクラウド)
コンポーネントが作成された後の任意の時点で、デバイスは状態を使用してプロパティをクラウドに報告できます。 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>テレメトリの送信 (デバイスからクラウド)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>クラウドからのコマンド更新コールバックの受信およびデバイス側での処理 (クラウドからデバイス)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>デバイス側のコマンド更新への応答 (デバイスからクラウド)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>次のステップ

IoT プラグ アンド プレイ ブリッジの詳細については、[IoT プラグ アンド プレイ ブリッジ](https://github.com/Azure/iot-plug-and-play-bridge)の GitHub リポジトリを参照してください。
