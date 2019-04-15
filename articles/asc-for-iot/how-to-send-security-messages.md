---
title: Azure Security Center for IoT のプレビューにセキュリティ メッセージを送信する | Microsoft Docs
description: Azure Security Center for IoT を使用してセキュリティ メッセージを送信する方法について説明します。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a9974fd15ae9c8c420992c3ae1084feebae0f57d
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862405"
---
# <a name="send-security-messages-sdk"></a>セキュリティ メッセージの送信 SDK

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この攻略ガイドでは、Azure Security Center (ASC) for IoT エージェントを使用せずにデバイス セキュリティ メッセージを収集して送信する場合の ASC for IoT サービスの機能と、それを行う方法を説明します。  

このガイドでは、以下の方法について説明します。 
> [!div class="checklist"]
> * C# 用のセキュリティ メッセージの送信 API を使用する
> * C 用のセキュリティ メッセージの送信 API を使用する

## <a name="asc-for-iot-capabilities"></a>ASC for IoT の機能

ASC for IoT では、送信されるデータが [ASC for IoT スキーマ](https://aka.ms/iot-security-schemas)に準拠していて、メッセージがセキュリティ メッセージとして設定されていれば、すべての種類のセキュリティ メッセージ データを処理および分析できます。

## <a name="security-message"></a>セキュリティ メッセージ

ASC for IoT では、次の条件を使用してセキュリティ メッセージが定義されています。
- メッセージが Azure IoT C/C# SDK で送信された場合
- メッセージが[セキュリティ メッセージ スキーマ](https://aka.ms/iot-security-schemas)に準拠している場合
- メッセージが送信前にセキュリティ メッセージとして設定されている場合

各セキュリティ メッセージには、`AgentId`、`AgentVersion`、`MessageSchemaVersion`、セキュリティ イベントのリストなど、送信者のメタデータが含まれています。
スキーマでは、イベントの種類など、セキュリティ メッセージの有効で必要なプロパティが定義されています。

[!NOTE]
> スキーマに準拠していない、送信されたメッセージは、無視されます。 現在、無視されたメッセージは保存されないため、データの送信を開始する前にスキーマを確認してください。 
> Azure IoT C/C# SDK を使用してセキュリティ メッセージとして設定されずに送信されたメッセージは、ASC for IoT パイプラインにルーティングされません

## <a name="valid-message-example"></a>有効なメッセージの例

次の例では、有効なセキュリティ メッセージ オブジェクトを示します。 この例には、メッセージ メタデータと 1 つの `ProcessCreate` セキュリティ イベントが含まれています。

セキュリティ メッセージとして設定されて送信されると、このメッセージは ASC for IoT によって処理されます。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>セキュリティ メッセージの送信 

[Azure IoT C# デバイス SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) または [Azure IoT C デバイス SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) を使用して、ASC for IoT エージェントを使用せずにセキュリティ メッセージを送信します。

ASC for IoT で処理するためにデバイスからデバイス データを送信するには、以下のいずれかの API を使用して、ASC for IoT 処理パイプラインへの正しいルーティングが行われるようにメッセージをマークします。 このようにして送信されたメッセージは、ASC for IoT のセキュリティ分析情報として処理され、IoT Hub 内および Azure Security Center 内の両方に表示されます。 

送信されるすべてのデータは、正しいヘッダーでマークされている場合でも、[ASC for IoT メッセージ スキーマ](https://aka.ms/iot-security-schemas)にも準拠する必要があります。 

### <a name="send-security-message-api"></a>セキュリティ メッセージの送信 API

**セキュリティ メッセージの送信** API は、現在、C および C# で使用できます。  

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>次の手順
- ASC for IoT サービスの[概要](overview.md)を読みます
- ASC for IoT の[アーキテクチャ](architecture.md)についてさらに学習します
- [サービス](quickstart-onboard-iot-hub.md)を有効にします
- [FAQ](resources-frequently-asked-questions.md) を読みます
- [未加工のセキュリティ データ](how-to-security-data-access.md)にアクセスする方法を学習します
- [レコメンデーション](concept-recommendations.md)について理解します
- [アラート](concept-security-alerts.md)について理解します
