---
title: "Azure  IoT Hub ファイルのアップロードについて | Microsoft Docs"
description: "開発者ガイド - IoT Hub のファイルのアップロード機能を使用して、デバイスから Azure Storage Blob コンテナーへのファイルのアップロードを管理します。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9ded95283b52f0fc21ca5b99df8e72e1e152fe1c
ms.openlocfilehash: c56a568fa003ec45e92279e070e6309763071827
ms.lasthandoff: 01/05/2017


---
# <a name="file-uploads-with-iot-hub"></a>IoT Hub を使用したファイルのアップロード

## <a name="overview"></a>概要

[IoT Hub エンドポイント][lnk-endpoints] に関する記事で詳しく説明したように、デバイスは、デバイス向けのエンドポイント (**/devices/{deviceId}/files**) を介して通知を送信することで、ファイルのアップロードを開始できます。  デバイスが IoT Hub にアップロードの完了を通知すると、IoT Hub は、サービス向けエンドポイント (**/messages/servicebound/filenotifications**) 経由でメッセージとして受信できるファイルのアップロード通知を生成します。

IoT Hub 自体を介してメッセージをやり取りする代わりに、IoT Hub が、関連付けられている Azure ストレージ アカウントへのディスパッチャーとして機能します。 デバイスは、アップロードするファイルに固有のストレージ トークンを IoT Hub に要求します。 SAS URI を使用して、ファイルをストレージにアップロードし、アップロードが完了すると、IoT Hub に完了の通知を送信します。 IoT Hub は、ファイルがアップロードされたことを確認すると、新しいサービス向けファイル通知メッセージング エンドポイントにファイルのアップロード通知を追加します。

デバイスから IoT Hub にファイルをアップロードするには、[Azure Storage アカウントを関連付ける][lnk-associate-storage]ことによってハブを構成する必要があります。

その後、デバイスは、[アップロードを開始][lnk-initialize]し、アップロードの完了時に [IoT Hub に通知][lnk-notify]できます。 必要に応じて、デバイスがアップロードの完了を IoT Hub に通知するときに、サービスによって[通知メッセージ][lnk-service-notification]を生成できます。

### <a name="when-to-use"></a>使用時の注意

ファイルのアップロードを使用して、断続的に接続されたデバイスでアップロードされた、または帯域幅を節約するために圧縮されたメディア ファイルや大容量のテレメトリ バッチを送信します。

報告プロパティ、Device-to-cloud メッセージ、またはファイルのアップロードのどれを使用するべきか不明な場合は、「[Device-to-cloud communication guidance (デバイスからクラウドへの通信に関するガイダンス)][lnk-d2c-guidance]」を参照してください。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage アカウントと IoT Hub の関連付け

ファイルのアップロード機能を使用するには、最初に Azure ストレージ アカウントを IoT Hub にリンクする必要があります。 このタスクは、[Azure Portal][lnk-management-portal] から、または [IoT Hub のリソース プロバイダー REST API][lnk-resource-provider-apis] からプログラムで完了することができます。 Azure Storage アカウントと IoT Hub を関連付けると、デバイスがファイルのアップロード要求を開始したときに、サービスがデバイスに SAS URI を返します。

> [!NOTE]
> [Azure IoT SDK][lnk-sdks] では、SAS URI の取得、ファイルのアップロード、および IoT Hub へのアップロード完了の通知を自動的に処理します。


## <a name="initialize-a-file-upload"></a>ファイルのアップロードの初期化
IoT Hub には、ファイルをアップロードするためのストレージの SAS URI を要求する、特にデバイス向けのエンドポイントがあります。 デバイスは、`{iot hub}.azure-devices.net/devices/{deviceId}/files` で 次の JSON 本文と共に POST を IoT Hub に送信することで、ファイルのアップロード プロセスを開始します。

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub は次のデータを返します。デバイスはこのデータを使用してファイルをアップロードします。

```json
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>不使用: ファイルのアップロードの初期化

> [!NOTE]
> このセクションでは、IoT Hub から SAS URI を 受信するための廃止された機能について説明しています。 前述の POST メソッドを使用する必要があります。

IoT Hub には、ファイルのアップロードをサポートする&2; つの REST エンドポイントがあります。1 つは、ストレージの SAS URI を取得します。もう&1; つは、アップロードの完了を IoT Hub に通知します。 デバイスは、`{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` で IoT Hub に GET を送信することで、ファイルのアップロード処理を開始します。 IoT Hub は、アップロードするファイルに固有の SAS URI と、アップロードの完了後に使用する相関 ID を返します。

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>IoT Hub へのファイルのアップロード完了の通知

デバイスは、Azure Storage SDK を使用してストレージにファイルをアップロードします。 アップロードが完了すると、デバイスは、`{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` で次の JSON 本文と共に POST を IoT Hub に送信します。

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` の値は、ファイルが正常にアップロードされたかどうかを示すブール値です。 状態コード `statusCode` は、ストレージに対するファイルのアップロードの状態であり、`statusDescription` は `statusCode` に対応します。

## <a name="reference-topics"></a>参照トピック:

以下の参照トピックは、デバイスからのファイルのアップロードに関する詳細情報を提供しています。

## <a name="file-upload-notifications"></a>ファイルのアップロード通知

デバイスがファイルをアップロードし、アップロード完了を IoT Hub に通知すると、サービスは必要に応じてファイルの名前と保存場所を含む通知メッセージを生成します。

「[エンドポイント][lnk-endpoints]」で説明したように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/fileuploadnotifications**) 経由でメッセージとしてファイルのアップロード通知を配信します。 ファイルのアップロード通知の受信セマンティクスは Cloud-to-device メッセージの場合と同様であり、[メッセージのライフサイクル][lnk-lifecycle]も同じです。 ファイルのアップロード通知エンドポイントから取得した各メッセージは、次のプロパティを持つ JSON レコードです。

| プロパティ | 説明 |
| --- | --- |
| EnqueuedTimeUtc |通知が作成された日時を示すタイムスタンプ。 |
| deviceId |**DeviceId** 。 |
| BlobUri |アップロードされたファイルの URI。 |
| BlobName |アップロードされたファイルの名前。 |
| LastUpdatedTime |ファイルが最後に更新された日時を示すタイムスタンプ。 |
| BlobSizeInBytes |アップロードされたファイルのサイズ。 |

**例**。 次の例は、ファイルのアップロード通知メッセージの本文を示しています。

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>ファイルのアップロード通知の構成オプション

各 IoT Hub では、ファイルのアップロード通知用に次の構成オプションを公開しています。

| プロパティ | 説明 | 範囲と既定値 |
| --- | --- | --- |
| **enableFileUploadNotifications** |ファイルのアップロード通知をファイル通知エンドポイントに書き込むかどうかを制御します。 |ブール値。 既定値: True。 |
| **fileNotifications.ttlAsIso8601** |ファイルのアップロード通知の既定の TTL。 |最大 48 時間の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| **fileNotifications.lockDuration** |ファイルのアップロード通知キューのロック期間。 |5 ～ 300 秒 (最小 5 秒)。 既定値: 60 秒。 |
| **fileNotifications.maxDeliveryCount** |ファイルのアップロード通知キューの最大配信数。 |1 ～ 100。 既定値: 100。 |

## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [IoT Hub のツインおよびジョブ向けのクエリ言語][lnk-query]: IoT Hub からデバイス ツインおよびジョブに関する情報を取得する際に使用できる IoT Hub のクエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

IoT Hub を使用してデバイスからファイルをアップロードする方法を理解できたら、次の IoT Hub 開発者ガイドのトピックも参考にしてください。

* [IoT Hub でデバイス ID を管理する][lnk-devguide-identities]
* [IoT Hub へのアクセスの制御][lnk-devguide-security]
* [デバイス ツインを使って状態と構成を同期する][lnk-devguide-device-twins]
* [デバイスでダイレクト メソッドを呼び出す][lnk-devguide-directmethods]
* [複数デバイスでのジョブをスケジュール設定する][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [IoT Hub でデバイスからクラウドにファイルをアップロードするには][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

