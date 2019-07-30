---
title: Azure Digital Twins 内でのロールの割り当ての作成と管理 | Microsoft Docs
description: Azure Digital Twins 内でのロールの割り当てを作成および管理します。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyhughes
ms.custom: seodec18
ms.openlocfilehash: a57089eb2cd87b08ba647afed002d90d6f14891a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846656"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Azure Digital Twins 内でのロールの割り当ての作成と管理

Azure Digital Twins は、ロールベースのアクセス制御 ([RBAC](./security-role-based-access-control.md)) を使用してリソースに対するアクセス権を管理します。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>ロールの割り当ての概要

各ロールの割り当ては、次の定義に準拠しています。

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

次の表は、各属性を示しています。

| Attribute | EnableAdfsAuthentication | 必須 | Type | 説明 |
| --- | --- | --- | --- | --- |
| roleId | ロール定義識別子 | はい | string | 必要なロールの割り当ての一意 ID。 ロールの定義とその識別子は、システム API のクエリを実行するか次の表を確認して見つけます。 |
| objectId | オブジェクト識別子 | はい | string | Azure Active Directory ID、サービス プリンシパル オブジェクト ID、またはドメイン名。 ロールの割り当ての割り当て先。 ロールの割り当ては、関連付けられている型に従って書式設定する必要があります。 `DomainName` objectIdType の場合、objectId は `“@”` 文字で始まる必要があります。 |
| objectIdType | オブジェクト識別子の型 | はい | string | 使用するオブジェクト識別子の種類。 下の「**サポートされているオブジェクト識別子の型**」をご覧ください。 |
| path | スペース パス | はい | string | `Space` オブジェクトへの完全アクセス パス。 例: `/{Guid}/{Guid}`。 識別子がグラフ全体のロールの割り当てを必要とする場合は、`"/"` を指定します。 この文字はルートの指定ですが、これを使用することはお勧めできません。 常に最小限の特権の原則に従ってください。 |
| tenantId | テナント識別子 | 多様 | string | ほとんどの場合、Azure Active Directory テナント ID。 `DeviceId` および `TenantId` ObjectIdTypes では許可されません。 `UserId` および `ServicePrincipalId` ObjectIdTypes では必須です。 DomainName ObjectIdType では省略可能です。 |

### <a name="supported-role-definition-identifiers"></a>サポートされているロール定義識別子

各ロールの割り当てでは、ロールの定義が Azure Digital Twins 環境内のエンティティに関連付けられます。

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>サポートされているオブジェクト識別子の型

以前に、**objectIdType** 属性が導入されました。

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>ロールの割り当て操作

Azure Digital Twins では、ロールの割り当てに対して *CREATE*、*READ*、*DELETE* の操作が完全にサポートされます。 *UPDATE* 操作は、ロールの割り当ての追加、ロールの割り当ての削除、またはロールの割り当てによってアクセス権が付与される[空間インテリジェンス グラフ](./concepts-objectmodel-spatialgraph.md) ノードの変更によって処理されます。

![ロールの割り当てのエンドポイント][1]

提供される Swagger リファレンス ドキュメントには、すべての使用可能な API エンドポイント、要求の操作、および定義に関する詳細情報が含まれています。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>サービス プリンシパルにアクセス許可を付与する

サービス プリンシパルへのアクセス許可の付与は、多くの場合、Azure Digital Twins を操作するときに実行する最初の手順の 1 つです。 以下を伴います。

1. PowerShell を使用した Azure インスタンスへのログイン。
1. サービス プリンシパル情報の取得。
1. サービス プリンシパルへの必要なロールの割り当て。

Azure Active Directory 内でアプリケーション ID が提供されます。 Active Directory での Azure Digital Twins の構成とプロビジョニングについて詳しくは、[クイック スタート](./quickstart-view-occupancy-dotnet.md)をご覧ください。

アプリケーション ID を入手したら、次の PowerShell コマンドを実行します。

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

**管理者**ロールを持つユーザーは、URL に認証済みの HTTP POST 要求を実行することによってユーザーにスペース管理者ロールを割り当てることができます。

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

以下の JSON 本文を使用します。

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>すべてのロールの取得

![システム ロール][2]

すべての利用可能なロール (ロールの定義) を一覧表示するには、認証済みの HTTP GET 要求を実行します。

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

要求が成功すると、割り当てることができる各ロールのエントリを含む JSON 配列が返されます。

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>特定のロールの割り当ての確認

特定のロールの割り当てを確認するには、認証済みの HTTP GET 要求を実行します。

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **パラメーター値** | **必須** |  **Type** |  **説明** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | string |   UserId objectIdType の objectId。 |
| YOUR_PATH | True | string |   アクセスを確認する選択済みパス。 |
| YOUR_ACCESS_TYPE |  True | string |   確認するアクセスの種類。 |
| YOUR_RESOURCE_TYPE | True | string |  確認するリソース。 |

要求が成功すると、指定されたパスとリソースについてアクセスの種類がユーザーに割り当てられているかどうかを示すブール値 `true` または `false` が返されます。

### <a name="get-role-assignments-by-path"></a>パスごとのロールの割り当ての取得

1 つのパスに対するすべてのロールの割り当てを取得するには、認証済みの HTTP GET 要求を実行します。

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_PATH | スペースへの完全パス |

要求が成功すると、選択した **path** パラメーターに関連付けられている各ロールの割り当てを含む JSON 配列が返されます。

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>アクセス許可の取り消し

受信者からアクセス許可を取り消すには、認証済みの HTTP DELETE 要求を実行してロールの割り当てを削除します。

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | 削除するロールの割り当ての **id** |

DELETE 要求が成功すると、204 応答状態が返されます。 ロールの割り当てがまだ保持されているかどうかを[チェック](#check)して、ロールの割り当ての削除を確認します。

### <a name="create-a-role-assignment"></a>役割の割り当ての作成

ロールの割り当てを作成するには、URL に対して認証済みの HTTP POST 要求を実行します。

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

JSON 本文が次のスキーマに準拠していることを確認します。

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

要求が成功すると、201 応答状態が新しく作成されたロールの割り当ての **id** と共に返されます。

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>構成の例

次の例は、いくつかの一般的なロールの割り当てシナリオにおいて JSON 本文を構成する方法を示しています。

* **例**:ユーザーには、テナント スペースのフロアへの管理アクセス権が必要です。

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **例**:アプリケーションはデバイスとセンサーのモック作成のテストのシナリオを実行します。

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **例**:ドメインの一部であるすべてのユーザーが、スペース、センサー、およびユーザーの読み取りアクセスを受け取ります。 このアクセスには、対応する関連オブジェクトが含まれます。

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>次の手順

- Azure Digital Twins のロール ベースのアクセス制御を確認するには、[ロール ベースのアクセス制御](./security-authenticating-apis.md)に関するページをご覧ください。

- Azure Digital Twins 認証について詳しくは、[API 認証](./security-authenticating-apis.md)に関するページをご覧ください。

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
