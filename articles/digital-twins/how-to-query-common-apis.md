---
title: Azure Digital Twins の一般的なクエリのパターン |Microsoft Docs
description: Azure Digital Twins Management API にクエリを実行する一般的なパターンについて説明します。
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: v-adgera
ms.openlocfilehash: eca355a51b516311d060df09905c1df769037ec5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722843"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>一般的なタスクについて Azure Digital Twins API をクエリする方法

この記事では、Azure Digital Twins インスタンスに対する一般的なシナリオを実行するために役立つクエリ パターンを示します。 ここでは、Digital Twins インスタンスが既に実行されていることを前提とします。 Postman などの任意の REST クライアントを使用することができます。 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>スペースおよび種類に対するクエリ

このセクションでは、プロビジョニングされたスペースについての詳細情報を取得するサンプル クエリを示します。 サンプル クエリを使用して認証済みの GET HTTP 要求を行い、プレースホルダーをセットアップからの値に置き換えます。 

- ルート ノードであるスペースを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- 名前によってスペースを取得し、デバイス、センサー、計算値、およびセンサー値を含めます。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- 親が指定されたスペース ID であり、[指定されたスペースに対する相関関係](how-to-navigate-apis.md#api-navigation)がレベル 2 から 5 となるスペースとそのデバイス/センサー情報を取得します。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- 指定の ID を持つスペースを取得し、計算値とセンサー値を含めます。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- 特定のスペースについてのプロパティ キーを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- *AreaInSqMeters* という名前のプロパティ キーを持ち、値が 30 であるスペースを取得します。 文字列操作も行うことができ、たとえば、`name = X contains Y` のプロパティ キーを含むスペースを取得できます。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- 名前 *Temperature* および関連する依存関係とオントロジーを持つすべての名前を取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>ロールとロール割り当てに対するクエリ

このセクションでは、ロールとその割り当てに関する詳細情報を取得するいくつかのクエリを示します。 

- Azure Digital Twins でサポートされているすべてのロールを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Digital Twins インスタンス内のすべてのロール割り当てを取得します。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- 特定パスにあるロール割り当てを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>デバイスに関するクエリ

このセクションでは、デバイスに関する特定の情報を取得するために、Management API を使用する方法の例をいくつか示します。 すべての API 呼び出しは、認証された GET HTTP 要求である必要があります。

- すべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- すべてのデバイスの状態を検索します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- 特定のデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- ルート スペースに接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- レベル 2 ～ 4 のスペースに接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- 特定のスペース ID に直接接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- 特定のスペースとその子孫に接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- あるスペースの子孫 (そのスペースを除く) に接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- あるスペースの直接の子に接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- あるスペースの先祖の 1 つに接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- あるスペースの子孫のうち、レベル 5 以下の子孫に接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- ID *YOUR_SPACE_ID* を持つスペースと同じレベルのスペースに接続されているすべてのデバイスを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- お使いのデバイスの IoT Hub 接続文字列を取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- 特定のハードウェア ID を持つデバイスを、接続されたセンサーと一緒に取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- 特定のデータ型のセンサーを取得します。この場合は *Motion* および *Temperature* です。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>マッチャーおよびユーザー定義関数に対するクエリ 

- プロビジョニング済みのすべてのマッチャーおよびその ID を取得します。

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- スペースおよび関連付けられているユーザー定義関数など、特定のマッチャーの詳細を取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- センサーに対するマッチャーを評価し、デバッグのためのログ記録を有効にします。 この HTTP GET メッセージの戻り値は、マッチャーとセンサーがデータ型に属しているかどうかを示します。 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- ユーザー定義関数の ID を取得します。 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- 特定のユーザー定義関数の内容を取得します。 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>ユーザーに対するクエリ

このセクションでは、Azure Digital Twins 内のユーザーを管理するためのいくつかのサンプル API クエリを示します。 プレース ホルダーをセットアップからの値に置き換えて HTTP GET 要求を行います。 

- すべてのユーザーを取得します。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- 特定のユーザーを取得します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>次の手順

Management API を使用して認証を行う方法については、[API を使用した認証](./security-authenticating-apis.md)に関するページを参照してください。

API エンドポイントの詳細については、「[Digital Twins Swagger の使用方法](./how-to-use-swagger.md)」に関するページをご覧ください。
