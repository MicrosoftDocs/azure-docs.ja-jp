---
title: "Azure IoT Hub の MQTT サポートについて | Microsoft Docs"
description: "開発者ガイド - MQTT プロトコルを使用して IoT Hub デバイスに接続されているエンドポイントに接続するデバイスのサポート。 Azure IoT デバイス SDK での組み込み MQTT サポートについての情報も含まれます。"
services: iot-hub
documentationcenter: .net
author: kdotchkoff
manager: timlt
editor: 
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kdotchko
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c09caf68b4acf90b5a76d2d715e07fc3a522f18c
ms.openlocfilehash: 7b9b7e558a95de88dedcb744e2a4b3c18cde35cc
ms.lasthandoff: 03/02/2017


---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>MQTT プロトコルを使用した IoT Hub との通信
IoT Hub により、デバイスは、ポート 8883 で [MQTT v3.1.1][lnk-mqtt-org] プロトコルを使用するか、ポート 443 で WebSocket プロトコル経由で MQTT v3.1.1 を使用して、IoT Hub デバイス エンドポイントと通信できます。 IoT Hub では、すべてのデバイスの通信を TLS/SSL を使用してセキュリティで保護する必要があります (したがって、IoT Hub では、ポート 1883 経由のセキュリティで保護されていない接続はサポートしません)。

## <a name="connecting-to-iot-hub"></a>IoT Hub への接続
MQTT プロトコルを使用してデバイスを IoT ハブに接続するには、[Azure IoT SDK][lnk-device-sdks] のライブラリを使用するか、MQTT プロトコルを直接使用します。

## <a name="using-the-device-sdks"></a>デバイス SDK の使用
MQTT プロトコルをサポートする[デバイス SDK][lnk-device-sdks] は、Java、Node.js、C、C #、および Python に対応しています。 デバイス SDK では、標準的な IoT Hub 接続文字列を使用して、IoT ハブへの接続を確立します。 MQTT プロトコルを使用するには、クライアント プロトコル パラメーターを **MQTT**に設定する必要があります。 デバイス SDK は既定では、**CleanSession** フラグを **0** に設定して IoT ハブに接続し、IoT ハブとのメッセージ交換には **QoS 1** を使用します。

デバイスは、IoT ハブに接続されると、デバイス SDK によって提供されるメソッドにより、IoT ハブとの間でメッセージの送受信ができるようになります。

次の表では、サポートされている各言語のコード サンプルへのリンクを提供すると共に、MQTT プロトコルを使用して IoT Hub への接続を確立するために使用するパラメーターを示します。

| 言語 | プロトコル パラメーター |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>デバイス アプリの AMQP から MQTT への移行
[デバイス SDK][lnk-device-sdks] を使用している場合、AMQP から MQTT に切り替えるには前述のようにクライアントの初期化でプロトコル パラメーターを変更する必要があります。

これを行う際は、次の項目をご確認ください。

* AMQP では、MQTT 接続を終了するときに、多くの条件のエラーが返されます。 そのため、例外処理のロジックを一部変更する必要が生じることがあります。
* MQTT は、[クラウドからデバイスへのメッセージ][lnk-messaging]の受信時の "*拒否*" 操作をサポートしていません。 バックエンド アプリでデバイス アプリからの応答を受信する必要がある場合は、[ダイレクト メソッド][lnk-methods]の使用をご検討ください。

## <a name="using-the-mqtt-protocol-directly"></a>MQTT プロトコルの直接使用
デバイスでデバイス SDK を使用できない場合でも、MQTT プロトコルを使用してデバイスをパブリックのデバイス エンドポイントに接続できます。 **接続** パケットで、デバイスは次の値を使用する必要があります。

* **ClientId** フィールドには、**deviceId** を使用します。
* **[Usename]** フィールドには、`{iothubhostname}/{device_id}/api-version=2016-11-14` を使用します。{iothubhostname} は IoT Hub の完全な CName です。

    たとえば、IoT Hub の名前が **contoso.azure-devices.net** であり、デバイスの名前が **MyDevice01** であるとすると、**Username** フィールドの内容は `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14` になります。
* **[Password]** フィールドには、SAS トークンを使用します。 SAS トークンの形式は、HTTP プロトコルや AMQP プロトコルの場合と同じです。<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    SAS トークンの生成方法の詳細については、[IoT Hub のセキュリティ トークンの使用][lnk-sas-tokens]に関するページのデバイス セクションを参照してください。

    テストするときは、[デバイス エクスプローラー][lnk-device-explorer] ツールを使用して SAS トークンをすばやく生成し、それをコピーして独自のコードに貼り付けることもできます。

  1. **デバイス エクスプローラー**で **[管理]** タブに移動します。
  2. **[SAS トークン]** (右上) をクリックします。
  3. **[SASTokenForm]** の **[DeviceID]** ドロップダウンでデバイスを選択します。 **[TTL]**を設定します。
  4. **[生成]** をクリックしてトークンを作成します。

     生成される SAS トークンは、次のような構成になります。`HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     MQTT を使用して接続するために、このトークンの一部を **[Password]** フィールドとして使用できます: `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT の接続および切断パケットでは、IoT Hub は、**操作の監視**チャネルに関するイベントを、接続の問題をトラブルシューティングするために役立つ情報を追加して発行します。

### <a name="sending-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの送信
接続に成功したら、デバイスから IoT Hub に `devices/{device_id}/messages/events/` または `devices/{device_id}/messages/events/{property_bag}` を**トピック名**として使用してメッセージを送信できます。 `{property_bag}` 要素を使用すると、デバイスは追加のプロパティ付きのメッセージを URL エンコード形式で送信できるようになります。 次に例を示します。

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> この `{property_bag}` 要素は、HTTP プロトコルでクエリ文字列に使用したのと同じエンコーディングを使用します。
>
>

また、デバイス アプリは、`devices/{device_id}/messages/events/{property_bag}` を **Will トピック名**として使用することで、テレメトリ メッセージとして転送される "*Will メッセージ*" を定義することもできます。

- IoT Hub では、QoS 2 メッセージはサポートされません。 デバイス アプリから **QoS 2** を使用したメッセージが発行されると、IoT Hub はネットワーク接続を閉じます。
- IoT Hub では、Retain メッセージは保持されません。 デバイスが **RETAIN** フラグを 1 に設定してメッセージを送信すると、IoT Hub はそのメッセージに **x-opt-retain** アプリケーション プロパティを追加します。 この場合、IoT Hub は、Retain メッセージを保持するのではなく、バックエンド アプリにそのメッセージを渡します。
- IoT Hub は、デバイスごとにアクティブな MQTT 接続を&1; つだけサポートします。 同じデバイス ID で新しい MQTT 接続が行われると、IoT Hub は既存の接続を破棄します。

詳細については、[メッセージング開発者ガイド][lnk-messaging]をご覧ください。

### <a name="receiving-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージの受信
IoT Hub からメッセージを受信するには、デバイスで、`devices/{device_id}/messages/devicebound/#` を**トピック フィルター**として使用してサブスクライブする必要があります。 トピック フィルター内の複数レベルのワイルドカード **#** は、デバイスがトピック名の追加プロパティを受信できるようにするためにのみ使用されます。 IoT Hub では、**#** または **?**  ワイルドカードを使用してサブトピックをフィルター処理することはできません。 IoT Hub はパブリッシャーとサブスクライバー間の汎用メッセージング ブローカーではないため、ドキュメント化されたトピック名とトピック フィルターのみをサポートします。

デバイスは、`devices/{device_id}/messages/devicebound/#` トピック フィルターで表されるデバイス固有のエンドポイントへのサブスクライブが成功するまで、IoT Hub からメッセージを受信することはありません。 サブスクリプションの成功が確立された後、デバイスは、サブスクリプション後に送信された C2D メッセージのみの受信を開始します。 デバイスが **CleanSession** フラグを **0** に設定した状態で接続している場合、サブスクリプションは複数のセッションで保持されます。 この場合、次回 **CleanSession 0** の状態で接続したときに、デバイスは、切断中にデバイスに対して送信された未処理メッセージを受信します。 ただし、デバイスが **CleanSession** フラグを **1** に設定して使用している場合は、デバイス エンドポイントにサブスクライブするまで、デバイスが IoT Hub からメッセージを受信することはありません。

IoT Hub は、**トピック名** `devices/{device_id}/messages/devicebound/` またはメッセージ プロパティがある場合は `devices/{device_id}/messages/devicebound/{property_bag}` のメッセージを配信します。 `{property_bag}` には、メッセージ プロパティの URL でエンコードされた値/キーのペアが含まれています。 プロパティ バッグに含められるのは、アプリケーション プロパティとユーザーが設定可能なシステム プロパティ (**messageId**、**correlationId** など) のみです。 システム プロパティの名前にはプレフィックス **$** が付きます。アプリケーション プロパティでは、プレフィックスのない元々のプロパティ名が使用されます。

デバイス アプリが **QoS 2** を使用してトピックをサブスクライブしている場合、IoT Hub は **SUBACK** パケットに最大の QoS レベル 1 を許可します。 その後、IoT Hub は QoS 1 を使用してデバイスにメッセージを配信します。

### <a name="retrieving-a-device-twins-properties"></a>デバイス ツインのプロパティの取得

まずデバイスが、操作の応答を受信するために、`$iothub/twin/res/#` にサブスクライブします。 次に、デバイスは、空のメッセージをトピック `$iothub/twin/GET/?$rid={request id}` に送信します (**request id** に値を指定します)。 その後サービスが、要求と同じ **request id** を使用して、トピック `$iothub/twin/res/{status}/?$rid={request id}` のデバイス ツイン データを含む応答メッセージを送信します。

request id には、[IoT Hub メッセージング開発者ガイド][lnk-messaging]に従って、メッセージ プロパティ値として有効な任意の値を指定できます。ステータスは、整数として検証されます。
応答本文には、デバイス ツインのプロパティ セクションが含まれます。

ID レジストリ エントリの本文は "properties" メンバーに限定されます。以下に例を示します。

        {
            "properties": {
                "desired": {
                    "telemetrySendFrequency": "5m",
                    "$version": 12
                },
                "reported": {
                    "telemetrySendFrequency": "5m",
                    "batteryLevel": 55,
                    "$version": 123
                }
            }
        }

使用できる状態コードは次のとおりです。

|状態 | 説明 |
| ----- | ----------- |
| 200 | 成功 |
| 429 | 要求が多すぎます (スロットル)。[IoT Hub スロットル][lnk-quotas]に関するページを参照してください。 |
| 5** | サーバー エラー |

詳細については、[デバイス ツイン開発者ガイド][lnk-devguide-twin]をご覧ください。

### <a name="update-device-twins-reported-properties"></a>デバイス ツインの報告されるプロパティの更新

IoT Hub のデバイス ツインで報告されるプロパティをデバイスが更新する手順を次に示します。

1. デバイスはまず、`$iothub/twin/res/#` トピックをサブスクライブして、IoT Hub から操作の応答を受信する必要があります。

1. デバイスは、デバイス ツインの更新を含むメッセージを、`$iothub/twin/PATCH/properties/reported/?$rid={request id}` トピックに送信します。 このメッセージには、**要求 ID** の値が含まれます。

1. サービスは、トピック `$iothub/twin/res/{status}/?$rid={request id}` で報告されたプロパティ コレクションの新しい ETag 値を含む応答メッセージを送信します。 この応答メッセージでは、要求と同じ**要求 ID** が使われます。

要求メッセージの本文には、報告されるプロパティの新しい値を提供する JSON ドキュメントが含まれています (他のプロパティやメタデータは変更できません)。
JSON ドキュメントの各メンバーは、デバイス ツインのドキュメントの対応するメンバーを更新または追加します。 メンバーを `null` に設定した場合、メンバーは包含オブジェクトから削除されます。 次に例を示します。

        {
            "telemetrySendFrequency": "35m",
            "batteryLevel": 60
        }

使用できる状態コードは次のとおりです。

|状態 | 説明 |
| ----- | ----------- |
| 200 | 成功 |
| 400 | 正しくない要求。 無効な形式の JSON |
| 429 | 要求が多すぎます (スロットル)。[IoT Hub スロットル][lnk-quotas]に関するページを参照してください。 |
| 5** | サーバー エラー |

詳細については、[デバイス ツイン開発者ガイド][lnk-devguide-twin]をご覧ください。

### <a name="receiving-desired-properties-update-notifications"></a>必要なプロパティの更新通知の受信

デバイスが接続されると、IoT Hub は、ソリューション バックエンドによって実行された更新の内容を含むトピック `$iothub/twin/PATCH/properties/desired/?$version={new version}` に通知を送信します。 For example:

        {
            "telemetrySendFrequency": "5m",
            "route": null
        }

プロパティの更新の場合、値 `null` は、JSON オブジェクト メンバーが削除されることを意味します。


> [!IMPORTANT] 
> IoT Hub は、デバイスが接続されている場合にのみ変更通知を生成します。 IoT Hub とデバイス アプリの間で必要なプロパティを同期させるには、[デバイス再接続フロー][lnk-devguide-twin-reconnection]を必ず実装してください。

詳細については、[デバイス ツイン開発者ガイド][lnk-devguide-twin]をご覧ください。

### <a name="respond-to-a-direct-method"></a>ダイレクト メソッドへの応答

最初に、デバイスは `$iothub/methods/POST/#` にサブスクライブする必要があります。 IoT Hub は、有効な JSON または空の本文と共にメソッド要求をトピック `$iothub/methods/POST/{method name}/?$rid={request id}` に送信します。

デバイスは、応答するために、有効な JSON または空の本文と共にメソッド要求をトピック `$iothub/methods/res/{status}/?$rid={request id}` に送信します。ここで、**request id** は要求メッセージの要求 ID と一致する必要があり、**status** は整数である必要があります。

詳細については、[ダイレクト メソッド開発者ガイド][lnk-methods]をご覧ください。

### <a name="additional-considerations"></a>追加の考慮事項
最後の考慮事項として、MQTT プロトコルの動作をクライアント側でカスタマイズする必要がある場合は、[Azure IoT プロトコル ゲートウェイ][lnk-azure-protocol-gateway]に関するページを確認してください。IoT Hub と直接やり取りする高性能のカスタム プロトコル ゲートウェイをデプロイできます。 Azure IoT プロトコル ゲートウェイでは、ブラウンフィールド MQTT デプロイメントまたは他のカスタム プロトコルに応じてデバイス プロトコルをカスタマイズすることができます。 ただし、このアプローチでは、カスタム プロトコル ゲートウェイを実行して運用する必要があります。

## <a name="next-steps"></a>次のステップ
詳細については、IoT Hub 開発者ガイドの「[MQTT サポートに関する留意事項][lnk-mqtt-devguide]」を参照してください。

MQTT プロトコルの詳細については、[MQTT のドキュメント][lnk-mqtt-docs]を参照してください。

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

* [Azure Certified for IoT デバイス カタログ][lnk-devices]
* [その他のプロトコルのサポート][lnk-protocols]
* [Event Hubs との比較][lnk-compare]
* [スケーリング、HA、および DR][lnk-scaling]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md

