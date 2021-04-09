---
title: モジュール ツインの JSON スキーマ - Azure
description: このトピックでは、Live Video Analytics on IoT Edge のモジュール ツイン JSON スキーマについて説明します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8bd86bdc2c8de9ee586e785db2074fa772100420
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87053068"
---
# <a name="module-twin-json-schema"></a>モジュール ツインの JSON スキーマ

デバイス ツインは、デバイスに関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 Azure IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 詳細については、「[IoT Hub のモジュール ツインの理解と使用](../../iot-hub/iot-hub-devguide-module-twins.md)」を参照してください

このトピックでは、Live Video Analytics on IoT Edge のモジュール ツイン JSON スキーマについて説明します。

> [!NOTE]
> Media Services リソースと Media Services API へのアクセスが承認されるには、まず認証を受ける必要があります。 詳細については、「 [Azure Media Services API へのアクセス](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api)」を参照してください。

## <a name="module-twin-properties"></a>モジュール ツイン プロパティ

Live Video Analytics on IoT Edge では、次のモジュール ツイン プロパティが公開されます。 

|プロパティ |必須 |動的 |説明 |
|---|---|---|---|
|applicationDataDirectory |はい |いいえ |構成を永続化するためのマウントされたボリュームへのパス。 |
|azureMediaServicesArmId |はい |いいえ |Media Services アカウントの一意の Azure Resource Manager 識別子。|
|aadTenantId |はい |いいえ |顧客の Azure AD テナント ID。|
|aadServicePrincipalAppId |はい |はい |顧客によって作成された Azure AD AppId。|
|aadServicePrincipalCertificate |はい<sup>*</sup>  |はい |顧客によって作成された Azure AD AppId 証明書。|
|aadServicePrincipalPassword |はい<sup>*</sup>  |はい |顧客によって作成された Azure AD AppId パスワード。|
|aadEndpoint |いいえ |いいえ |クラウドに固有の Azure AD エンドポイント。 <br/>既定値: `https://login.microsoftonline.com` |
|aadResourceId |いいえ |いいえ |クラウドに固有の Azure AD 対象ユーザーまたはリソース ID <br/>既定値: `https://management.core.windows.net/` |
|armEndpoint |いいえ |いいえ |クラウドに固有の Azure Resource Manager エンドポイント。 <br/>既定値: `https://management.azure.com/` |
|diagnosticsLevel |いいえ |はい |イベントの詳細度: <br/>情報 &#x02758; 警告 &#x02758; エラー &#x02758; 重大 &#x02758; なし |
|diagnosticsEventsOutputName |いいえ |はい |診断イベントのハブ出力。 <br/>(空白は診断が公開されていないことを意味します)|
|operationalEventsOutputName|いいえ|はい|操作イベントのハブ出力。<br/>(空白は操作イベントが公開されていないことを意味します)
|logLevel|いいえ|はい|次のいずれか: <br/>&#x000B7; 詳細<br/>&#x000B7; 情報 (既定)<br/>&#x000B7; 警告<br/>&#x000B7; エラー<br/>&#x000B7; なし|
|logCategories|いいえ|はい|以下のコンマ区切りリスト: Application、MediaPipeline、Events <br/>既定値はApplication、Events|
|debugLogsDirectory|いいえ|はい|デバッグ ログのディレクトリ。 存在する場合、ログが生成されます。存在しない場合、デバッグ ログは無効になります。

<sup>*</sup>サービス プリンシパル証明書またはパスワードを指定する必要があります。 

動的プロパティは、モジュールを再起動しなくても更新できます。 これらのプロパティのいくつかの値は、[Media Services API へのアクセスの取得](../latest/access-api-howto.md)に関する記事の手順に従って取得できます。 

オプションの診断設定の役割の詳細については、「[監視とログ](monitoring-logging.md)」を参照してください。

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>次のステップ

[ダイレクト メソッド](direct-methods.md)
