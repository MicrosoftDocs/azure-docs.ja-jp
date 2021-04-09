---
title: カスタム ヘッダーを使用して監査ログにデータを追加する - Azure API for FHIR
description: この記事では、Azure API for FHIR でカスタム HTTP ヘッダーを使用して、監査ログにデータを追加する方法について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018490"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>カスタム HTTP ヘッダーを使用してデータを監査ログに追加する

Azure 高速ヘルスケア相互運用性リソース (FHIR) API では、ユーザーがログに、呼び出し元のシステムからの追加情報を含めたい場合があります。

たとえば、API のユーザーが外部システムによって認証されると、そのシステムは FHIR API への呼び出しを転送します。 FHIR API レイヤーでは、呼び出しが転送されたため、元のユーザーに関する情報が失われています。 監査または管理の目的で、このユーザー情報を記録して保持する必要がある場合があります。 呼び出し元のシステムは、HTTP ヘッダーで、ユーザー ID、呼び出し元の場所、またはその他の必要な情報を提供できます。これらの情報は、呼び出しが転送されるときに一緒に伝達されます。

このデータ フローを、次の図に示します。

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="カスタム ヘッダーの図":::

カスタム ヘッダーを使用して、いくつかの種類の情報をキャプチャできます。 次に例を示します。

* ID または認証情報
* 呼び出し元
* 元の組織
* クライアント システムの詳細 (電子医療記録、患者ポータル)

> [!IMPORTANT]
> カスタム ヘッダーで送信された情報は、Azure ログ監視で使用可能になってから 30 日間、Microsoft の内部ログ システムに格納されていることに注意してください。 情報をカスタム ヘッダーに追加する前に暗号化することをお勧めします。 カスタム ヘッダーを通じて PHI 情報を渡すことはできません。

HTTP ヘッダーには、次の名前付け規則を使用する必要があります。X-MS-AZUREFHIR-AUDIT-\<name>。

以下の HTTP ヘッダーは、ログに追加されるプロパティ バッグに含まれています。 次に例を示します。

* X-MS-AZUREFHIR-AUDIT-USERID:1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION:XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ:1234

この情報は、ログのプロパティ列に追加されるときに、JSON にシリアル化されます。 次に例を示します。

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
HTTP ヘッダーと同様に、同じヘッダー名を別の値で繰り返すことができます。 次に例を示します。

* X-MS-AZUREFHIR-AUDIT-USERLOCATION:HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION:緊急

ログに追加されるときに、値はコンマ区切りのリストと結合されます。 次に例を示します。

{ "X-MS-AZUREFHIR-AUDIT-USERLOCATION" :"HospitalA, Emergency" }
 
最大で 10 個の一意のヘッダーを追加できます (異なる値を持つ同じヘッダーの繰り返しは、1 個としてカウントされます)。 1 つのヘッダーの値の最大長は、2,048 文字です。

Firefly C# クライアント API ライブラリを使用している場合、コードは次のようになります。

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>次のステップ
この記事では、Azure API for FHIR でカスタム ヘッダーを使用して、監査ログにデータを追加する方法について説明しました。 次に、Azure API for FHIR で構成できるその他の追加設定について説明します。
 
>[!div class="nextstepaction"]
>[[追加設定]](azure-api-for-fhir-additional-settings.md)
