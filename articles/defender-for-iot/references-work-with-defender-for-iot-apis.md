---
title: Defender for IoT の API を操作する
description: 外部 REST API を使用してセンサーおよび管理コンソールによって検出されたデータにアクセスし、そのデータに対してアクションを実行します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 73c5d1f31d9e0651ee710593aa4e1b68fe972560
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222140"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender for IoT センサーと管理コンソール API

外部 REST API を使用してセンサーおよび管理コンソールによって検出されたデータにアクセスし、そのデータに対してアクションを実行します。

接続は SSL 経由で保護されます。

## <a name="getting-started"></a>作業の開始

一般に、Azure Defender for IoT センサーまたはオンプレミス管理コンソールで外部 API を使用している場合は、アクセス トークンを生成する必要があります。 トークンは、センサーとオンプレミスの管理コンソールで使用する認証 API には必要ありません。

トークンを生成するには:

1. **[システム設定]** ウィンドウで、 **[アクセス トークン]** を選択します。
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="[アクセス トークン] ボタンが強調表示されている [システム設定] ウィンドウのスクリーンショット。":::

2. **[新しいトークンの生成]** を選択します。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="新しいトークンを生成するには、このボタンを選択します。":::

3. 新しいトークンの目的を記述し、 **[次へ]** を選択します。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="新しいトークンを生成し、それに関連付けられている統合の名前を入力します。":::

4. アクセス トークンが表示されます。 これは二度と表示されないため、コピーしておきます。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="統合用のアクセス トークンをコピーします。":::

5. **[完了]** を選択します。 作成したトークンは、 **[アクセス トークン]** ダイアログ ボックスに表示されます。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="入力されたトークンを含む [デバイス トークン] ダイアログ ボックスのスクリーンショット":::

   **[Used]\(使用日\)** は、このトークンを持つ外部呼び出しが最後に受信された日時を示します。

   このトークンの **[Used]\(使用日\)** フィールドに **N/A** と表示されている場合、センサーと接続されているサーバー間の接続が機能していません。

6. **Authorization** という名前の HTTP ヘッダーを要求に追加し、その値を生成したトークンに設定します。

## <a name="sensor-api-specifications"></a>Sensor API 仕様

このセクションでは、次の Sensor API について説明します。

- [デバイス情報を取得する - /api/v1/devices](#retrieve-device-information---apiv1devices)

- [デバイスの接続情報を取得する - /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [CVE に関する情報を取得する - /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [アラート情報を取得する - /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [タイムライン イベントを取得する - /api/v1/events](#retrieve-timeline-events---apiv1events)

- [脆弱性情報を取得する - /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [セキュリティの脆弱性を取得する - /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [動作の脆弱性を取得する - /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [ユーザーの資格情報を検証する - /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [パスワードを変更する - /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [システム管理者によるユーザー パスワードの更新 - /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>デバイス情報を取得する - /api/v1/devices

Defender for IoT センサーによって検出されたすべてのデバイスの一覧を要求するには、この API を使用します。

#### <a name="method"></a>メソッド

**GET**

Defender for IoT センサーによって検出されたすべてのデバイスの一覧を要求します。

#### <a name="query-parameters"></a>クエリ パラメーター

- **authorized**:承認されたデバイスのみ、または承認されていないデバイスのみをフィルター処理します。

  **例**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

デバイスを表す JSON オブジェクトの配列。

#### <a name="device-fields"></a>デバイス フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **id** | 数値 | いいえ | - |
| **ipAddresses** | JSON 配列 | はい | IP アドレス (インターネット アドレスまたはデュアル NIC を搭載したデバイスの場合は、複数のアドレスを指定できます) |
| **name** | String | いいえ | - |
| **type** | String | いいえ | Unknown、Engineering Station、PLC、HMI、Historian、Domain Controller、DB Server、Wireless Access Point、Router、Switch、Server、Workstation、IP Camera、Printer、Firewall、Terminal station、VPN Gateway、Internet、または Multicast and Broadcast |
| **macAddresses** | JSON 配列 | はい | MAC アドレス (デュアル NIC を搭載したデバイスの場合は、複数のアドレスを指定できます) |
| **operatingSystem** | String | はい | - |
| **engineeringStation** | ブール型 | いいえ | ｔrue または false |
| **scanner** | ブール型 | いいえ | ｔrue または false |
| **承認済み** | ブール型 | いいえ | ｔrue または false |
| **vendor** | String | はい | - |
| **protocols** | JSON 配列 | はい | プロトコル オブジェクト |
| **firmware** | JSON 配列 | はい | ファームウェア オブジェクト |

#### <a name="protocol-fields"></a>プロトコル フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **名前** | String | いいえ |  |
| **アドレス** | JSON 配列 | はい | マスター、または数値 |

#### <a name="firmware-fields"></a>ファームウェアのフィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **serial** | String | いいえ | N/A、または実際の値 |
| **model** | String | いいえ | N/A、または実際の値 |
| **firmwareVersion** | Double | いいえ | N/A、または実際の値 |
| **additionalData** | String | いいえ | N/A、または実際の値 |
| **moduleAddress** | String | いいえ | N/A、または実際の値 |
| **rack** | String | いいえ | N/A、または実際の値 |
| **slot** | String | いいえ | N/A、または実際の値 |
| **address** | String | いいえ | N/A、または実際の値 |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:<span>//127<span>.0.0.1/api/v1/devices?authorized=true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>デバイスの接続情報を取得する - /api/v1/devices/connections

デバイスごとのすべての接続の一覧を要求するには、この API を使用します。

#### <a name="method"></a>メソッド

**GET**

#### <a name="query-parameters"></a>クエリ パラメーター

クエリ パラメーターを設定しない場合は、すべてのデバイス接続が返されます。

**例**:

`/api/v1/devices/connections`

- **deviceId**:特定のデバイス ID でフィルター処理して、その接続を表示します。

  **例**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**:現時点から遡った、接続がアクティブになっている概算時間 (分単位)。

  **例**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**:特定の時刻 (ミリ秒、UTC) より前に検出された接続のみをフィルター処理します。

  **例**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**:特定の時刻 (ミリ秒、UTC) より後に検出された接続のみをフィルター処理します。

  **例**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

デバイス接続を表す JSON オブジェクトの配列。

#### <a name="fields"></a>フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **firstDeviceId** | 数値 | いいえ | - |
| **secondDeviceId** | 数値 | いいえ | - |
| **lastSeen** | 数値 | いいえ | エポック (UTC) |
| **discovered** | 数値 | いいえ | エポック (UTC) |
| **ports** | 数値の配列 | いいえ | - |
| **protocols** | JSON 配列 | いいえ | プロトコル フィールド |

#### <a name="protocol-field"></a>プロトコル フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **name** | String | いいえ | - |
| **commands** | 文字列配列 | いいえ | - |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl コマンド

> [!div class="mx-tdBreakAll"]
> | Type | API | 例 |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/connections |
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/<deviceId>/connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter=' | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>CVE に関する情報を取得する - /api/v1/devices/cves

ネットワーク内のデバイスで検出されたすべての既知の CVE の一覧を要求するには、この API を使用します。

#### <a name="method"></a>メソッド

**GET**

#### <a name="query-parameters"></a>クエリ パラメーター

既定では、この API はすべてのデバイス IP の一覧を、IP アドレスごとに最大 100 件のスコア上位の CVE 付きで取得します。

**例**:

`/api/v1/devices/cves`

- **deviceId**:特定のデバイスの IP アドレスでフィルター処理して、その特定のデバイスで検出されたスコア上位の CVE を最大 100 件取得します。

  **例**:

  `/api/v1/devices/<ipAddress>/cves`

- **top**:デバイスの IP アドレスごとに取得する、スコア上位の CVE の数。

  **例**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

IP アドレスで識別された CVE を表す JSON オブジェクトの配列。

#### <a name="fields"></a>フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **cveId** | String | いいえ | - |
| **ipAddress** | String | いいえ | IP アドレス |
| **スコア** | String | いいえ | 0.0 - 10.0 |
| **attackVector** | String | いいえ | Network、Adjacent Network、Local、または Physical |
| **description** | String | いいえ | - |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/cves |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/<deviceIpAddress>/cves?top= | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>アラート情報を取得する - /api/v1/alerts

この API を使用して、Defender for IoT センサーによって検出されたすべてのアラートの一覧を要求します。

#### <a name="method"></a>メソッド

**GET**

#### <a name="query-parameters"></a>クエリ パラメーター

- **state**: 処理済みまたは未処理の警告のみをフィルター処理します。

  **例**:

  `/api/v1/alerts?state=handled`

- **fromTime**:特定の時刻 (ミリ秒、UTC) 以降に作成されたアラートのみをフィルター処理します。

  **例**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**:特定の時刻 (ミリ秒、UTC) の前に作成されたアラートのみをフィルター処理します。

  **例**:

  `/api/v1/alerts?toTime=<epoch>`

- **type**: 特定の種類のアラートをフィルター処理します。 フィルター処理できる既存の種類: 予期しない新しいデバイス、切断。

  **例**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

アラートを表す JSON オブジェクトの配列。

#### <a name="alert-fields"></a>アラート フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **ID** | 数値 | いいえ | - |
| **time** | 数値 | いいえ | エポック (UTC) |
| **title** | String | いいえ | - |
| **message** | String | いいえ | - |
| **severity** | String | いいえ | Warning、Minor、Major、または Critical |
| **engine** | String | いいえ | Protocol Violation、Policy Violation、Malware、Anomaly、または Operational |
| **sourceDevice** | 数値 | はい | デバイス ID |
| **destinationDevice** | 数値 | はい | デバイス ID |
| **additionalInformation** | 追加情報オブジェクト | はい | - |

#### <a name="additional-information-fields"></a>追加情報フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **description** | String | いいえ | - |
| **information** | JSON 配列 | いいえ | String |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl コマンド

> [!div class="mx-tdBreakAll"]
> | Type | API | 例 |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type=' | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections' |

### <a name="retrieve-timeline-events---apiv1events"></a>タイムライン イベントを取得する - /api/v1/events

イベント タイムラインに報告されたイベントの一覧を要求するには、この API を使用します。

#### <a name="method"></a>メソッド

**GET**

#### <a name="query-parameters"></a>クエリ パラメーター

- **minutesTimeFrame**:現時点から遡った、イベントが報告されてからの概算時間 (分単位)。

  **例**:

  `/api/v1/events?minutesTimeFrame=20`

- **type**: 特定の種類のイベントの一覧をフィルター処理します。

  **例**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

アラートを表す JSON オブジェクトの配列。

#### <a name="event-fields"></a>イベント フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|--|
| **timestamp** | 数値 | いいえ | エポック (UTC) |
| **title** | String | いいえ | - |
| **severity** | String | いいえ | INFO、NOTICE、または ALERT |
| **所有者** | String | はい | イベントが手動で作成された場合、このフィールドにはイベントを作成したユーザー名が含まれます |
| **content** | String | いいえ | - |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>脆弱性情報を取得する - /api/v1/reports/vulnerabilities/devices

各デバイスの脆弱性評価の結果を要求するには、この API を使用します。

#### <a name="method"></a>メソッド

**GET**

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

評価されたデバイスを表す JSON オブジェクトの配列。

デバイス オブジェクトには次のものが含まれます。

- 一般的なデータ

- 評価スコア

- 脆弱性

#### <a name="device-fields"></a>デバイス フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **name** | String | いいえ | - |
| **ipAddresses** | JSON 配列 | いいえ | - |
| **securityScore** | 数値 | いいえ | - |
| **vendor** | String | はい |  |
| **firmwareVersion** | String | はい | - |
| **model** | String | はい | - |
| **isWirelessAccessPoint** | ブール型 | いいえ | ｔrue または false |
| **operatingSystem** | オペレーティング システム オブジェクト | はい | - |
| **vulnerabilities** | 脆弱性オブジェクト | はい | - |

#### <a name="operating-system-fields"></a>オペレーティング システム フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **名前** | String | はい | - |
| **Type** | String | はい | - |
| **Version** | String | はい | - |
| **latestVersion** | String | はい | - |

#### <a name="vulnerabilities-fields"></a>脆弱性フィールド
 
| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **antiViruses** | JSON 配列 | はい | ウイルス対策の名前 |
| **plainTextPasswords** | JSON 配列 | はい | パスワード オブジェクト |
| **remoteAccess** | JSON 配列 | はい | リモート アクセス オブジェクト |
| **isBackupServer** | ブール型 | いいえ | ｔrue または false |
| **openedPorts** | JSON 配列 | はい | オープンなポート オブジェクト |
| **isEngineeringStation** | ブール型 | いいえ | ｔrue または false |
| **isKnownScanner** | ブール型 | いいえ | ｔrue または false |
| **cves** | JSON 配列 | はい | CVE オブジェクト |
| **isUnauthorized** | ブール型 | いいえ | ｔrue または false |
| **malwareIndicationsDetected** | ブール型 | いいえ | ｔrue または false |
| **weakAuthentication** | JSON 配列 | はい | 脆弱な認証を使用していることが検出されたアプリケーション |

#### <a name="password-fields"></a>パスワード フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **password** | String | いいえ | - |
| **protocol** | String | いいえ | - |
| **strength** | String | いいえ | Very weak、Weak、Medium、または Strong |

#### <a name="remote-access-fields"></a>リモート アクセス フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **port** | 数値 | いいえ | - |
| **transport** | String | いいえ | TCP または UDP |
| **client** | String | いいえ | IP アドレス |
| **clientSoftware** | String | いいえ | SSH、VNC、Remote desktop、または Team viewer |

#### <a name="open-port-fields"></a>オープンなポート フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **port** | 数値 | いいえ | - |
| **transport** | String | いいえ | TCP または UDP |
| **protocol** | String | はい | - |
| **isConflictingWithFirewall** | ブール型 | いいえ | ｔrue または false |

#### <a name="cve-fields"></a>CVE フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **ID** | String | いいえ | - |
| **スコア** | 数値 | いいえ | Double |
| **description** | String | いいえ | - |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>セキュリティの脆弱性を取得する - /api/v1/reports/vulnerabilities/security

一般的な脆弱性評価の結果を要求するには、この API を使用します。 この評価は、システムのセキュリティ レベルに関する分析情報を提供します。

この評価は、特定のデバイスの評価ではなく、一般的なネットワーク情報とシステム情報に基づいて行われます。

#### <a name="method"></a>メソッド

**GET**

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

評価結果を表す JSON オブジェクト。 各キーは null 許容にすることができます。 それ以外の場合は、null 非許容キーを持つ JSON オブジェクトが含まれます。

### <a name="result-fields"></a>結果フィールド

**[キー]**

**unauthorizedDevices**

| フィールド名 | Type | ［値の一覧］ |
| ---------- | ---- | -------------- |
| **address** | String | IP アドレス |
| **name** | String | - |
| **firstDetectionTime** | 数値 | エポック (UTC) |
| lastSeen | 数値 | エポック (UTC) |

**illegalTrafficByFirewallRules**

| フィールド名 | Type | ［値の一覧］ |
| ---------- | ---- | -------------- |
| **server** | String | IP アドレス |
| **client** | String | IP アドレス |
| **port** | 数値 | - |
| **transport** | String | TCP、UDP、または ICMP |

**weakFirewallRules**

| フィールド名 | Type | ［値の一覧］ |
| ---------- | ---- | -------------- |
| **sources** | ソースの JSON 配列。 各ソースには、4 つの形式を使用できます。 | "Any"、"IP アドレス (ホスト)"、"IP から IP (範囲)"、"IP アドレス, サブネット マスク (ネットワーク)" |
| **destinations** | 宛先の JSON 配列。 各宛先には、4 つの形式を使用できます。 | "Any"、"IP アドレス (ホスト)"、"IP から IP (範囲)"、"IP アドレス, サブネット マスク (ネットワーク)" |
| **ports** | 3 つの形式のいずれかのポートの JSON 配列 | "Any"、"ポート (検出されている場合はプロトコル)"、"ポート - ポート (検出されている場合はプロトコル)" |

**accessPoints**

| フィールド名 | Type | ［値の一覧］ |
| ---------- | ---- | -------------- |
| **macAddress** | String | MAC アドレス |
| **vendor** | String | ベンダー名 |
| **ipAddress** | String | IP アドレス、または N/A |
| **name** | String | デバイス名、または N/A |
| **wireless** | String | No、Suspected、または Yes |

**connectionsBetweenSubnets**

| フィールド名 | Type | ［値の一覧］ |
| ---------- | ---- | -------------- |
| **server** | String | IP アドレス |
| **client** | String | IP アドレス |

**industrialMalwareIndicators**

| フィールド名 | Type | ［値の一覧］ |
| ---------- | ---- | -------------- |
| **detectionTime** | 数値 | エポック (UTC) |
| **alertMessage** | String | - |
| **description** | String | - |
| **devices** | JSON 配列 | デバイス名 | 

**internetConnections**

| フィールド名 | Type | ［値の一覧］ |
| ---------- | ---- | -------------- |
| **internalAddress** | String | IP アドレス |
| **承認済み** | ブール型 | はい、いいえ | 
| **externalAddresses** | JSON 配列 | IP アドレス |

#### <a name="response-example"></a>応答の例

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>動作の脆弱性を取得する - /api/v1/reports/vulnerabilities/operational

一般的な脆弱性評価の結果を要求するには、この API を使用します。 この評価により、ネットワークの動作状態に関する分析情報が得られます。 これは、特定のデバイスの評価ではなく、一般的なネットワーク情報とシステム情報に基づいて行われます。

#### <a name="method"></a>メソッド

**GET**

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

評価結果を表す JSON オブジェクト。 各キーには、結果の JSON 配列が含まれています。

#### <a name="result-fields"></a>結果フィールド

**[キー]**

**backupServer**

| フィールド名 | Type | ［値の一覧］ |
|--|--|--|
| **source** | String | IP アドレス |
| **destination** | String | IP アドレス |
| **port** | 数値 | - |
| **transport** | String | TCP または UDP |
| **backupMaximalInterval** | String | - |
| **lastSeenBackup** | 数値 | エポック (UTC) |

**ipNetworks**

| フィールド名 | Type | ［値の一覧］ |
|--|--|--|
| **addresse** s | 数値 | - |
| **network** | String | IP アドレス |
| **mask** | String | サブネット マスク |

**protocolProblems**

| フィールド名 | Type | ［値の一覧］ |
|--|--|--|
| **protocol** | String | - |
| **addresses** | JSON 配列 | IP アドレス |
| **alert** | String | - |
| **reportTime** | 数値 | エポック (UTC) |

**protocolDataVolumes**

| フィールド名 | Type | ［値の一覧］ |
|--|--|--|
| protocol | String | - |
| ボリューム | String | "ボリューム量 MB" |

**disconnections**

| フィールド名 | Type | ［値の一覧］ |
|--|--|--|
| **assetAddress** | String | IP アドレス |
| **assetName** | String | - |
| **lastDetectionTime** | 数値 | エポック (UTC) |
| **backToNormalTime** | 数値 | エポック (UTC) |     

#### <a name="response-example"></a>応答の例

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>ユーザーの資格情報を検証する - /api/external/authentication/validation

Defender for IoT のユーザー名とパスワードを検証するには、この API を使用します。 すべての Defender for IoT ユーザー ロールは、API で使用できます。

この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

#### <a name="method"></a>メソッド

**POST**

#### <a name="request-type"></a>要求の種類

**JSON**

#### <a name="query-parameters"></a>クエリ パラメーター

| **名前** | **Type** | **NULL 値の使用** |
|--|--|--|
| **username** | String | いいえ |
| **password** | String | いいえ |

#### <a name="request-example"></a>要求の例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

動作状態の詳細を含むメッセージ文字列:

- **成功 - msg**:Authentication succeeded (認証に成功しました)

- **失敗 - error**:Credentials Validation Failed (資格情報の検証に失敗しました)

#### <a name="response-example"></a>応答の例

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>パスワードを変更する - /external/authentication/set_password

ユーザーが自分のパスワードを変更できるようにするには、この API を使用します。 すべての Defender for IoT ユーザー ロールは、API で使用できます。 この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

#### <a name="method"></a>メソッド

**POST**

#### <a name="request-type"></a>要求の種類

**JSON**

#### <a name="request-example"></a>要求の例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

動作状態の詳細を含むメッセージ文字列:

- **成功 – msg**:Password has been replaced (パスワードが置き換えられました)

- **失敗 – error**:User authentication failure (ユーザー認証エラー)

- **失敗 – error**:Password does not match security policy (パスワードがセキュリティ ポリシーと一致しません)

#### <a name="response-example"></a>応答の例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>デバイス フィールド

| **名前** | **Type** | **NULL 値の使用** |
|--|--|--|
| **username** | String | いいえ |
| **password** | String | いいえ |
| **new_password** | String | いいえ |

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password | curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>システム管理者によるユーザー パスワードの更新 - /external/authentication/set_password_by_admin

システム管理者が指定されたユーザーのパスワードを変更できるようにするには、この API を使用します。 Defender for IoT 管理者ユーザー ロールは、API で使用できます。 この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

#### <a name="method"></a>メソッド

**POST**

#### <a name="request-type"></a>要求の種類

**JSON**

#### <a name="request-example"></a>要求の例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

動作状態の詳細を含むメッセージ文字列:

- **成功 – msg**:Password has been replaced (パスワードが置き換えられました)

- **失敗 – error**:User authentication failure (ユーザー認証エラー)

- **失敗 – error**:User does not exist (ユーザーが存在しません)

- **失敗 – error**:Password doesn't match security policy (パスワードがセキュリティ ポリシーと一致しません)

- **失敗 – error**:User does not have the permissions to change password (ユーザーにパスワードを変更する権限がありません)

#### <a name="response-example"></a>応答の例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>デバイス フィールド

| **名前** | **Type** | **NULL 値の使用** |
|--|--|--|
| **admin_username** | String | いいえ |
| **admin_password** | String | いいえ |
| **username** | String | いいえ |
| **new_password** | String | いいえ |

#### <a name="curl-command"></a>Curl コマンド

> [!div class="mx-tdBreakAll"]
> | Type | API | 例 |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>オンプレミス管理コンソール API の仕様

このセクションでは、次のオンプレミス管理コンソール API について説明します。

- **/external/v1/alerts/<UUID>**

- **アラートの除外 (メンテナンス期間)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="アラートの除外ウィンドウ。アクティブなルールが表示されています。":::

アラートを送信しない条件を定義します。 たとえば、停止時刻と開始時刻、アラートをトリガーするときに除外するデバイスまたはサブネット、または除外する必要がある Defender for IoT エンジンを定義して更新します。 たとえば、メンテナンス期間中は、重要なデバイスのマルウェア アラートを除き、すべてのアラートの配信を停止したいとします。

ここで定義した API は、オンプレミスの管理コンソールの **[アラートの除外]** ウィンドウに読み取り専用の除外ルールとして表示されます。

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **応答の例**

- **応答:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>パスワードを変更する - /external/authentication/set_password

ユーザーが自分のパスワードを変更できるようにするには、この API を使用します。 すべての Defender for IoT ユーザー ロールは、API で使用できます。 この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>システム管理者によるユーザー パスワードの更新 - /external/authentication/set_password_by_admin

システム管理者が特定のユーザーのパスワードを変更できるようにするには、この API を使用します。 Defender for IoT 管理者ユーザー ロールは、API で使用できます。 この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

### <a name="retrieve-device-information---externalv1devices"></a>デバイス情報を取得する - /external/v1/devices

この API は、オンプレミスの管理コンソールに接続されている Defender for IoT sensors センサーによって検出されたすべてのデバイスの一覧を要求します。

#### <a name="method"></a>メソッド

**GET**

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

デバイスを表す JSON オブジェクトの配列。

#### <a name="query-parameters"></a>クエリ パラメーター

- **authorized**:承認されたデバイスのみ、または承認されていないデバイスのみをフィルター処理します。

- **siteId**:特定のサイトに関連するデバイスのみをフィルター処理します。

- **zoneId**:特定のゾーンに関連付けられているデバイスのみをフィルター処理します。 [1](#1)

- **sensorId**:特定のセンサーによって検出されたデバイスのみをフィルター処理します。 [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *サイトとゾーン ID がない可能性があります。この場合は、すべてのデバイスに対してクエリを実行し、サイトとゾーン ID を取得します。*

#### <a name="query-parameters-example"></a>クエリ パラメーターの例

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>デバイス フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **sensorId** | 数値 | いいえ | - |
| **zoneId** | 数値 | はい | - |
| **siteId** | 数値 | はい | - |
| **ipAddresses** | JSON 配列 | はい | IP アドレス (インターネット アドレスまたはデュアル NIC を搭載したデバイスの場合は、複数のアドレスを指定できます) |
| **name** | String | いいえ | - |
| **type** | String | いいえ | Unknown、Engineering Station、PLC、HMI、Historian、Domain Controller、DB Server、Wireless Access Point、Router、Switch、Server、Workstation、IP Camera、Printer、Firewall、Terminal station、VPN Gateway、Internet、または Multicast and Broadcast |
| **macAddresses** | JSON 配列 | はい | MAC アドレス (デュアル NIC を搭載したデバイスの場合は、複数のアドレスを指定できます) |
| **operatingSystem** | String | はい | - |
| **engineeringStation** | ブール型 | いいえ | ｔrue または false |
| **scanner** | ブール型 | いいえ | ｔrue または false |
| **承認済み** | ブール型 | いいえ | ｔrue または false |
| **vendor** | String | はい | - |
| **プロトコル** | JSON 配列 | はい | プロトコル オブジェクト |
| **firmware** | JSON 配列 | はい | ファームウェア オブジェクト |

#### <a name="protocol-fields"></a>プロトコル フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| 名前 | String | いいえ | - |
| アドレス | JSON 配列 | はい | マスター、または数値 |

#### <a name="firmware-fields"></a>ファームウェアのフィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **serial** | String | いいえ | N/A、または実際の値 |
| **model** | String | いいえ | N/A、または実際の値 |
| **firmwareVersion** | Double | いいえ | N/A、または実際の値 |
| **additionalData** | String | いいえ | N/A、または実際の値 |
| **moduleAddress** | String | いいえ | N/A、または実際の値 |
| **rack** | String | いいえ | N/A、または実際の値 |
| **slot** | String | いいえ | N/A、または実際の値 |
| **address** | String | いいえ | N/A、または実際の値 |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true' |

### <a name="retrieve-alert-information---externalv1alerts"></a>アラート情報を取得する - /external/v1/alerts

オンプレミスの管理コンソールからすべてまたはフィルター処理されたアラートを取得するには、この API を使用します。

#### <a name="method"></a>メソッド

**GET**

#### <a name="query-parameters"></a>クエリ パラメーター

- **state**: 処理されたアラートのみ、または未処理のアラートのみをフィルター処理します。

  **例**:

  `/api/v1/alerts?state=handled`

- **fromTime**:特定の時刻 (ミリ秒、UTC) 以降に作成されたアラートのみをフィルター処理します。

  **例**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**:特定の時刻 (ミリ秒、UTC) の前に作成されたアラートのみをフィルター処理します。

  **例**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**:アラートが検出されたサイト。 [2](#2)

- **zoneId**:アラートが検出されたゾーン。 [2](#2)

- **sensor**:アラートが検出されたセンサー。

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *サイトとゾーン ID がない可能性があります。この場合は、すべてのデバイスに対してクエリを実行し、サイトとゾーン ID を取得します。*

#### <a name="alert-fields"></a>アラート フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **ID** | 数値 | いいえ | - |
| **time** | 数値 | いいえ | エポック (UTC) |
| **title** | String | いいえ | - |
| **message** | String | いいえ | - |
| **severity** | String | いいえ | Warning、Minor、Major、または Critical |
| **engine** | String | いいえ | Protocol Violation、Policy Violation、Malware、Anomaly、または Operational |
| **sourceDevice** | 数値 | はい | デバイス ID |
| **destinationDevice** | 数値 | はい | デバイス ID |
| **additionalInformation** | 追加情報オブジェクト | はい | - |

#### <a name="additional-information-fields"></a>追加情報フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **description** | String | いいえ | - |
| **information** | JSON 配列 | いいえ | String |

#### <a name="response-example"></a>応答の例

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Curl コマンド

> [!div class="mx-tdBreakAll"]
> | Type | API | 例 |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor=' | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1' |

### <a name="qradar-alerts"></a>QRadar アラート

Defender for IoT への QRadar の統合は、Defender for IoT によって生成されたアラートを識別し、これらのアラートに対してアクションを実行する上で役立ちます。 データは Defender for IoT から QRadar へ送信された後、パブリック API のオンプレミス管理コンソール コンポーネントに接続されます。

Defender for IoT によって検出されたデータを QRadar に送信するには、Defender for IoT システムで転送ルールを定義し、 **[Remote support alert handling]\(アラート処理のリモート サポート\)** オプションを選択します。

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="必要に応じて、転送ルールを編集します。":::

転送ルールを構成するプロセス中にこのオプションを選択すると、QRadar に次の追加フィールドが表示されます。

- **UUID**:一意のアラート識別子 (1-1555245116250 など)。

- **サイト**:アラートが検出されたサイト。

- **Zone**:アラートが検出されたゾーン。

QRadar に送信されるペイロードの例を次に示します。

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/&lt;UUID&gt;

#### <a name="method"></a>メソッド

**PUT**

#### <a name="request-type"></a>要求の種類

**JSON**

#### <a name="request-content"></a>要求内容

UUID を含むアラートに対して実行するアクションを表す JSON オブジェクト。

#### <a name="action-fields"></a>アクション フィールド

| 名前 | Type | Nullable | ［値の一覧］ |
|--|--|--|--|
| **action** | String | いいえ | handle または handleAndLearn |

#### <a name="request-example"></a>要求の例

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

デバイスを表す JSON オブジェクトの配列。

#### <a name="response-fields"></a>応答フィールド


| 名前 | Type | Nullable | 説明 |
|--|--|--|--|
| **content / error** | String | いいえ | 要求が成功すると、content プロパティが表示されます。 それ以外の場合は、error プロパティが表示されます。 |

#### <a name="possible-content-values"></a>考えられるコンテンツ値

| status code | content の値 | 説明 |
|--|--|--|
| 200 | Alert update request finished successfully. (アラートの更新要求が正常に完了しました。) | 更新要求が正常に完了しました。 コメントはありません。 |
| 200 | Alert was already handled (**handle**). (アラートは既に処理されています (**handle**)。) | このアラートは、アラートの処理要求を受信した時には既に処理されていました。<br />アラートは、**handled** のままになります。 |
| 200 | Alert was already handled and learned. (**handleAndLearn**) (アラートは既に処理および学習されています (**handleAndLearn**)。) | このアラートは、**handleAndLearn** への要求を受信したときにはすでに処理され、学習されていました。<br />アラートは **handledAndLearn** 状態のままになります。 |
| 200 | Alert was already handled. (**handled**) (アラートはすでに処理されています (**handled**)。)<br />Handle and learn (**handleAndLearn**) was performed on the alert. (アラートに対して処理と学習 (**handleAndLearn**) が実行されました。) | このアラートは、**handleAndLearn** への要求を受信したときにはすでに処理されていました。<br />このアラートは、**handleAndLearn** になります。 |
| 200 | Alert was already handled and learned. (**handleAndLearn**) (アラートは既に処理および学習されています (**handleAndLearn**)。) Ignored handle request. (ハンドル要求は無視されました。) | このアラートは、アラートを処理する要求を受信したときにはすでに **handleAndLearn** でした。 このアラートは **handleAndLearn** のままになります。 |
| 500 | Invalid action. (無効なアクション。) | 送信されたアクションは、アラートで実行する有効なアクションではありません。 |
| 500 | Unexpected error occurred. (予期しないエラーが発生しました。) | 予期しないエラーが発生しました。 この問題を解決するには、テクニカル サポートにお問い合わせください。 |
| 500 | Couldn't execute request because no alert was found for this UUID. (この UUID のアラートが見つからなかったため、要求を実行できませんでした。) | 指定されたアラート UUID がシステムで見つかりませんでした。 |

#### <a name="response-example"></a>応答の例

**成功**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**失敗**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| PUT | curl -k -X PUT -d '{"action": "<ACTION>"}' -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID> | curl -k -X PUT -d '{"action": "handle"}' -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>アラートの除外 (メンテナンス期間) - /external/v1/maintenanceWindow

アラートを送信しない条件を定義します。 たとえば、停止時刻と開始時刻、アラートをトリガーするときに除外するデバイスまたはサブネット、または除外する必要がある Defender for IoT エンジンを定義して更新します。 例としては、メンテナンス期間中は、重要なデバイスのマルウェア アラートを除き、すべてのアラートの配信を停止するなどです。

ここで定義した API は、オンプレミスの管理コンソールの **[アラートの除外]** ウィンドウに読み取り専用の除外ルールとして表示されます。

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="[アラートの除外] ウィンドウ。すべての除外ルールの一覧が表示されています。":::

#### <a name="method---post"></a>メソッド - POST

#### <a name="query-parameters"></a>クエリ パラメーター

- **ticketId**:ユーザーのシステムのメンテナンス チケット ID を定義します。

- **ttl**:TTL (Time to Live) を定義します。これは、分単位のメンテナンス期間です。 このパラメーターで定義した時間が経過すると、システムによって自動的にアラートの送信が開始されます。

- **engines**:メンテナンス プロセス中にどのセキュリティ エンジンからアラートを抑制するかを定義します。

   - ANOMALY

   - MALWARE

   - OPERATIONAL

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**:メンテナンス プロセス中にどの Defender for IoT センサーからアラートを抑制するかを定義します。 これは、/api/v1/appliances (GET) から取得したものと同じ ID です。

- **subnets**:メンテナンス プロセス中にどのサブネットからアラートを抑制するかを定義します。 サブネットは次の形式で送信されます:192.168.0.0/16。

#### <a name="error-codes"></a>エラー コード

- **201 (Created)** :アクションは正常に完了しました。

- **400 (Bad Request)** :次の場合に表示されます。

   - **ttl** パラメーターが数値ではないか、正の値ではない。

   - **subnets** パラメーターが間違った形式で定義さている。

   - **ticketId** パラメーターがない。

   - **engine** パラメーターが既存のセキュリティ エンジンと一致しない。

- **404 (Not Found)** :センサーの 1 つが存在しません。

- **409 (Conflict)** :チケット ID は、別のオープン メンテナンス期間にリンクされています。

- **500 (Internal Server Error)** :その他の予期しないエラー。

> [!NOTE]
> チケット ID が既存のオープン期間にリンクされていないことを確認します。 次の除外ルールが生成されます:メンテナンス-{トークン名}-{チケット ID}。

#### <a name="method---put"></a>メソッド - PUT

**ttl** パラメーターを変更することで、メンテナンス プロセスを開始した後にメンテナンス期間の継続時間を更新することができます。 新しい持続時間の定義は、以前の定義を上書きします。

このメソッドは、現在構成されている期間よりも長い期間を設定する場合に便利です。

#### <a name="query-parameters"></a>クエリ パラメーター

- **ticketId**:ユーザーのシステムのメンテナンス チケット ID を定義します。

- **ttl**:期間を分単位で定義します。

#### <a name="error-code"></a>エラー コード

- **200 (OK)** :アクションは正常に完了しました。

- **400 (Bad Request)** :次の場合に表示されます。

   - **ttl** パラメーターが数値ではないか、正の値ではない。

   - **ticketId** パラメーターがない。

   - **ttl** パラメーターがない。

- **404 (Not Found)** :チケット ID は、オープン メンテナンス期間にリンクされていません。

- **500 (Internal Server Error)** :その他の予期しないエラー。

> [!NOTE]
> チケット ID が既存のオープン期間にリンクされていることを確認します。

#### <a name="method---delete"></a>メソッド - DELETE

既存のメンテナンス期間を閉じます。

#### <a name="query-parameters"></a>クエリ パラメーター

- **ticketId**:ユーザーのシステムのメンテナンス チケット ID をログします。

#### <a name="error-code"></a>エラー コード

- **200 (OK)** :アクションは正常に完了しました。

- **400 (Bad Request)** :**ticketId** パラメーターがありません。

- **404 (Not Found)** :チケット ID は、オープン メンテナンス期間にリンクされていません。

- **500 (Internal Server Error)** :その他の予期しないエラー。

> [!NOTE]
> チケット ID が既存のオープン期間にリンクされていることを確認します。

#### <a name="method---get"></a>メソッド - GET

メンテナンス中にシステムで実行されたすべてのオープン、クローズ、および更新アクションのログを取得します。 ログを取得できるのは、過去にアクティブだったメンテナンス期間がクローズされている場合のみです。

#### <a name="query-parameters"></a>クエリ パラメーター

- **fromDate**:定義済みの日付以降のログをフィルター処理します。 形式は 2019-12-30 です。

- **toDate**:定義されている日付までのログをフィルター処理します。 形式は 2019-12-30 です。

- **ticketId**:特定のチケット ID に関連するログをフィルター処理します。

- **tokenName**:特定のトークン名に関連するログをフィルター処理します。

#### <a name="error-code"></a>エラー コード

- **200 (OK)** :アクションは正常に完了しました。

- **400 (Bad Request)** :日付の形式が間違っています。

- **204 (No Content)** :表示するデータがありません。

- **500 (Internal Server Error)** :その他の予期しないエラー。

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

メンテナンス期間の操作を表す JSON オブジェクトの配列。

#### <a name="response-structure"></a>応答の構造

| 名前 | Type | 解説 | Nullable |
|--|--|--|--|
| **dateTime** | String | 例:"2012-04-23T18:25:43.511Z" | no |
| **ticketId** | String | 例:"9a5fe99c-d914-4bda-9332-307384fe40bf" | no |
| **tokenName** | String | - | no |
| **engines** | 文字列の配列 | - | はい |
| **sensorIds** | 文字列の配列 | - | はい |
| **サブネット** | 文字列の配列 | - | はい |
| **ttl** | 数値 | - | はい |
| **operationType** | String | 値は "OPEN"、"UPDATE"、および "CLOSE" です。 | no |

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| POST | curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2...ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2...SENSOR_IDn>],"subnets": [<SUBNET1, SUBNET2....SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow | curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl":"20","engines": ["ANOMALY"],"sensorIds": ["5","3"],"subnets": ["10.0.0.3"]}' -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow |
| PUT | curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Authorization: <AUTH_TOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow | curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl":"20"}' -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow |
| DELETE | curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Authorization: <AUTH_TOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow | curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName=' | curl -k -H "Authorization:1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-01-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>ユーザーの資格情報を認証する - /external/authentication/validation

ユーザーの資格情報を検証するには、この API を使用します。 すべての Defender for IoT ユーザー ロールは、API で使用できます。 この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

#### <a name="method"></a>メソッド

**POST**

#### <a name="request-type"></a>要求の種類

**JSON**

#### <a name="request-example"></a>要求の例

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

動作状態の詳細を含むメッセージ文字列:

- **成功 – msg**:Authentication succeeded (認証に成功しました)

- **失敗 – error**:Credentials Validation Failed (資格情報の検証に失敗しました)

#### <a name="device-fields"></a>デバイス フィールド

| **名前** | **Type** | **NULL 値の使用** |
|--|--|--|
| **username** | String | いいえ |
| **password** | String | いいえ |

#### <a name="response-example"></a>応答の例

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| POST | curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation' | curl -k -d '{"username":"myUser","password":"1234@abcd"}' 'https:/<span>/127.0.0.1/external/authentication/validation' |

### <a name="change-password---externalauthenticationset_password"></a>パスワードを変更する - /external/authentication/set_password

ユーザーが自分のパスワードを変更できるようにするには、この API を使用します。 すべての Defender for IoT ユーザー ロールは、API で使用できます。 この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

#### <a name="method"></a>メソッド

**POST**

#### <a name="request-type"></a>要求の種類

**JSON**

#### <a name="request-example"></a>要求の例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

動作状態の詳細を含むメッセージ文字列:

- **成功 – msg**:Password has been replaced (パスワードが置き換えられました)

- **失敗 – error**:User authentication failure (ユーザー認証エラー)

- **失敗 – error**:Password does not match security policy (パスワードがセキュリティ ポリシーと一致しません)

#### <a name="response-example"></a>応答の例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>デバイス フィールド

| **名前** | **Type** | **NULL 値の使用** |
|--|--|--|
| **username** | String | いいえ |
| **password** | String | いいえ |
| **new_password** | String | いいえ |

#### <a name="curl-command"></a>Curl コマンド

| Type | API | 例 |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password | curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>システム管理者によるユーザー パスワードの更新 - /external/authentication/set_password_by_admin

システム管理者が指定されたユーザーのパスワードを変更できるようにするには、この API を使用します。 Defender for IoT 管理者ユーザー ロールは、API で使用できます。 この API を使用するために Defender for IoT のアクセス トークンは必要ありません。

#### <a name="method"></a>メソッド

**POST**

#### <a name="request-type"></a>要求の種類

**JSON**

#### <a name="request-example"></a>要求の例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>応答の種類

**JSON**

#### <a name="response-content"></a>応答コンテンツ

動作状態の詳細を含むメッセージ文字列:

- **成功 – msg**:Password has been replaced (パスワードが置き換えられました)

- **失敗 – error**:User authentication failure (ユーザー認証エラー)

- **失敗 – error**:User does not exist (ユーザーが存在しません)

- **失敗 – error**:Password doesn't match security policy (パスワードがセキュリティ ポリシーと一致しません)

- **失敗 – error**:User does not have the permissions to change password (ユーザーにパスワードを変更する権限がありません)

#### <a name="response-example"></a>応答の例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>デバイス フィールド

| **名前** | **Type** | **NULL 値の使用** |
|--|--|--|
| **admin_username** | String | いいえ |
| **admin_password** | String | いいえ |
| **username** | String | いいえ |
| **new_password** | String | いいえ |

#### <a name="curl-command"></a>Curl コマンド

> [!div class="mx-tdBreakAll"]
> | Type | API | 例 |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/external/authentication/set_password_by_admin |

## <a name="next-steps"></a>次のステップ

- [デバイス インベントリのセンサー検出を調査する](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [デバイス インベントリのすべてのエンタープライズ センサー検出を調査する](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
