---
title: Azure IoT Hub ファイルのアップロードについて | Microsoft Docs
description: 開発者ガイド - IoT Hub のファイルのアップロード機能を使用して、デバイスから Azure Storage Blob コンテナーへのファイルのアップロードを管理します。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 3ae87523e66ae49d17f198a1f70b0f449ca0a713
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080417"
---
# <a name="upload-files-with-iot-hub"></a>IoT Hub を使用したファイルのアップロード

[IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)に関する記事で詳しく説明したように、デバイスは、デバイス向けエンドポイント ( **/devices/{deviceId}/files**) を介して通知を送信することで、ファイルのアップロードを開始できます。 アップロードが完了したことをデバイスが IoT Hub に通知すると、IoT Hub はサービス向けエンドポイント ( **/messages/servicebound/filenotifications**) を介してファイル アップロード通知メッセージを送信します。

IoT Hub 自体を介してメッセージをやり取りする代わりに、IoT Hub が、関連付けられている Azure ストレージ アカウントへのディスパッチャーとして機能します。 デバイスは、アップロードするファイルに固有のストレージ トークンを IoT Hub に要求します。 SAS URI を使用して、ファイルをストレージにアップロードし、アップロードが完了すると、IoT Hub に完了の通知を送信します。 IoT Hub は、ファイル アップロードが完了したことを確認してから、サービス向けファイル通知エンドポイントにファイル アップロード通知メッセージを追加します。

デバイスから IoT Hub にファイルをアップロードするには、[Azure Storage アカウントを関連付ける](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub)ことによってハブを構成する必要があります。

その後、デバイスは、[アップロードを開始](iot-hub-devguide-file-upload.md#initialize-a-file-upload)し、アップロードの完了時に [IoT Hub に通知](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)できます。 必要に応じて、デバイスがアップロードの完了を IoT Hub に通知するときに、サービスによって[通知メッセージ](iot-hub-devguide-file-upload.md#file-upload-notifications)を生成できます。

### <a name="when-to-use"></a>いつ使用するか

ファイルのアップロードを使用して、断続的に接続されたデバイスでアップロードされた、または帯域幅を節約するために圧縮されたメディア ファイルや大容量のテレメトリ バッチを送信します。

報告プロパティ、Device-to-cloud メッセージ、またはファイルのアップロードのどれを使用するべきか不明な場合は、[デバイスからクラウドへの通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)を参照してください。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage アカウントと IoT Hub の関連付け

ファイルのアップロード機能を使用するには、最初に Azure ストレージ アカウントを IoT Hub にリンクする必要があります。 このタスクは、Azure portal から、または [IoT Hub のリソース プロバイダー REST API](/rest/api/iothub/iothubresource) からプログラムで完了することができます。 Azure ストレージ アカウントと IoT ハブを関連付けると、デバイスがファイルのアップロード要求を開始したときに、サービスがデバイスに SAS URI を返します。

[IoT Hub でデバイスからクラウドにファイルをアップロードする方法](iot-hub-csharp-csharp-file-upload.md)に関する操作ガイドに、ファイルのアップロード プロセスの完全な手順が示されています。 これらの操作ガイドには、Azure portal を使用してストレージ アカウントを IoT ハブに関連付ける方法が説明されています。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) では、SAS URI の取得、ファイルのアップロード、および IoT Hub へのアップロード完了の通知を自動的に処理します。

## <a name="initialize-a-file-upload"></a>ファイルのアップロードの初期化
IoT Hub には、ファイルをアップロードするためのストレージの SAS URI を要求する、特にデバイス向けのエンドポイントがあります。 ファイルのアップロード プロセスを開始するために、デバイスは以下の JSON 本文を含む POST 要求を `{iot hub}.azure-devices.net/devices/{deviceId}/files` に送信します。

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub は次のデータを返します。デバイスはこのデータを使用してファイルをアップロードします。

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>非推奨: ファイルのアップロードの初期化

> [!NOTE]
> このセクションでは、IoT Hub から SAS URI を 受信するための非推奨の機能について説明しています。 前述の POST メソッドを使用します。

IoT Hub には、ファイルのアップロードをサポートする 2 つの REST エンドポイントがあります。1 つは、ストレージの SAS URI を取得します。もう 1 つは、アップロードの完了を IoT Hub に通知します。 デバイスは、`{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` の IoT ハブに GET を送信して、ファイルのアップロード プロセスを開始します。 IoT Hub は以下を返します。

* アップロードするファイルに固有の SAS URI

* アップロードの完了後に使用される関連付け ID

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>IoT Hub へのファイルのアップロード完了の通知

デバイスは、Azure Storage SDK を使用してストレージにファイルをアップロードします。 アップロードが完了すると、デバイスは、次の JSON 本文を含む POST 要求を `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` に送信します。

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

必要に応じて、デバイスがアップロードの完了を IoT Hub に通知すると、IoT Hub によって通知メッセージが生成されます。 このメッセージには、ファイルの名前とストレージの場所が含まれています。

「[エンドポイント](iot-hub-devguide-endpoints.md)」で説明したように、IoT Hub はサービス向けエンドポイント ( **/messages/servicebound/fileuploadnotifications**) 経由でメッセージとしてファイルのアップロード通知を配信します。 ファイルのアップロード通知の受信セマンティクスは cloud-to-device メッセージの場合と同様であり、[メッセージのライフ サイクル](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)も同じです。 ファイルのアップロード通知エンドポイントから取得した各メッセージは、次のプロパティを持つ JSON レコードです。

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

各 IoT ハブには、ファイルのアップロード通知用に次の構成オプションが用意されています。

| プロパティ | 説明 | 範囲と既定値 |
| --- | --- | --- |
| **enableFileUploadNotifications** |ファイルのアップロード通知をファイル通知エンドポイントに書き込むかどうかを制御します。 |ブール値。 既定値はTrue。 |
| **fileNotifications.ttlAsIso8601** |ファイルのアップロード通知の既定の TTL。 |最大 48 時間の ISO_8601 書式による間隔 (最小 1 分)。 既定値は1 時間。 |
| **fileNotifications.lockDuration** |ファイルのアップロード通知キューのロック期間。 |5 ～ 300 秒 (最小 5 秒)。 既定値は60 秒。 |
| **fileNotifications.maxDeliveryCount** |ファイルのアップロード通知キューの最大配信数。 |1 ～ 100。 既定値は100。 |

## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md): 実行時および管理操作用のさまざまな IoT Hub エンドポイントについて説明します。

* [スロットルとクォータ](iot-hub-devguide-quotas-throttling.md): IoT Hub サービスに適用されるクォータとスロットルの動作について説明します。

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。

* [IoT Hub のクエリ言語](iot-hub-devguide-query-language.md): IoT Hub からデバイス ツインとジョブに関する情報を取得する際に使用できるクエリ言語について説明します。

* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md): IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次の手順

IoT Hub を使用してデバイスからファイルをアップロードする方法を理解できたら、次の IoT Hub 開発者ガイドのトピックも参考にしてください。

* [IoT Hub でデバイス ID を管理する](iot-hub-devguide-identity-registry.md)

* [IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)

* [デバイス ツインを使って状態と構成を同期する](iot-hub-devguide-device-twins.md)

* [デバイスでダイレクト メソッドを呼び出す](iot-hub-devguide-direct-methods.md)

* [複数デバイスでのジョブをスケジュール設定する](iot-hub-devguide-jobs.md)

この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [IoT Hub でデバイスからクラウドにファイルをアップロードする方法](iot-hub-csharp-csharp-file-upload.md)