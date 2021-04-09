---
title: Azure IoT Hub モジュール ツインについて | Microsoft Docs
description: 開発者ガイド - モジュール ツインを使用して、IoT Hub とデバイス間で状態と構成を同期する
author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: nehsin
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6e728eaf8335a102e38a3b4b07ab5e504d452294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91996457"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>IoT Hub のモジュール ツインの理解と使用

この記事は、「[IoT Hub のデバイス ツインの理解と使用](iot-hub-devguide-device-twins.md)」をすでに読み、理解していることを前提としています。 IoT Hub では、デバイス ID ごとに最大 50 のモジュール ID を作成できます。 モジュールの ID ごとに、モジュール ツインは暗黙的に生成されます。 モジュール ツインは、デバイス ツインと同様、モジュールの状態に関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 Azure IoT Hub は、IoT Hub に接続したモジュールごとにモジュール ツインを管理します。 

デバイス側では、IoT Hub デバイス SDK を使用して、それぞれが IoT Hub への独立した接続を確立するモジュールを作成できます。 この機能により、デバイスのさまざまなコンポーネントごとに個別の名前空間を使用することができます。 たとえば、3 つの異なるセンサーが付いた自動販売機があるとします。 各センサーは、社内の異なる部署によって管理されています。 センサーごとにモジュールを作成することができます。 これにより、各部門は、管轄するセンサーにのみジョブを送信したり、メソッドを転送できるため、競合やユーザー エラーを回避できます。

 モジュール ID とモジュール ツインは、デバイス ID とデバイス ツインと同様の機能を、より細かい粒度で提供します。 このより細かい粒度により、オペレーティング システム ベースのデバイスやファームウェア デバイスなどの複数のコンポーネントで構成され、この機能をサポートするデバイスの各コンポーネントの構成と状態を特定できます。 モジュール形式のソフトウェア コンポーネントを含む IoT デバイスを使用する場合、モジュール ID とモジュール ツインを使用することで、コンポーネントの管理を分割できます。 マイクロソフトは、モジュール ツインの一般可用性により、モジュール ツイン レベルですべてのデバイス ツイン機能をサポートすることを目指しています。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

この記事では、次の内容について説明します。

* モジュール ツインの構造: *タグ*、*必要なプロパティ*、*報告されるプロパティ*。
* モジュールとバックエンドがモジュール ツイン上で実行できる操作。

報告されるプロパティ、device-to-cloud メッセージ、ファイルのアップロードのどれを使用するべきかの指針については、「[device-to-cloud 通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」を参照してください。

必要なプロパティ、ダイレクト メソッド、cloud-to-device メッセージの使用に関する指針については、「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

## <a name="module-twins"></a>モジュール ツイン

モジュール ツインには、モジュールに関連する次のような情報が格納されます。

* デバイス上のモジュールと IoT Hub がモジュールの状態と構成を同期するために使用できます。

* 実行時間の長い操作にクエリを行い、ターゲットを設定するために使用するソリューション バックエンド。

モジュール ツインのライフサイクルは、対応する[モジュール ID](iot-hub-devguide-identity-registry.md) にリンクされます。 モジュール ツインは、IoT Hub でモジュール ID を作成または削除したときに、暗黙的に作成または削除されます。

モジュール ツインは、次の情報を含む JSON ドキュメントです。

* **タグ**。 ソリューション バックエンドで読み書きできる JSON ドキュメントのセクションです。 タグは、デバイス上のモジュールからは認識されません。 タグは、クエリの目的で設定されます。

* **必要なプロパティ**。 モジュールの構成や状態を同期するために、報告されるプロパティと共に使用します。 ソリューション バックエンドは必要なプロパティを設定でき、モジュール アプリはそれらを読み取れます。 モジュール アプリでは、必要なプロパティに対する変更を知らせる通知を受け取ることもできます。

* **報告されるプロパティ**。 モジュールの構成や状態を同期するために、必要なプロパティと共に使用します。 モジュール アプリは報告されるプロパティを設定でき、ソリューション バックエンドはそれらを読み取りクエリを実行できます。

* **モジュール ID のプロパティ**。 モジュール ツインの JSON ドキュメントのルートには、[ID レジストリ](iot-hub-devguide-identity-registry.md)に格納される対応するモジュール ID の読み取り専用プロパティが含まれます。

![デバイス ツインのアーキテクチャの表現](./media/iot-hub-devguide-device-twins/module-twin.jpg)

次の例は、モジュール ツインの JSON ドキュメントを示しています。

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

ルート オブジェクトには、モジュール ID プロパティと、`tags` のコンテナー オブジェクトと、`reported` プロパティと `desired` プロパティの両方があります。 `properties` コンテナーには、「[モジュール ツインのメタデータ](iot-hub-devguide-module-twins.md#module-twin-metadata)」と「[オプティミスティック コンカレンシー](iot-hub-devguide-device-twins.md#optimistic-concurrency)」セクションで説明されているいくつかの読み取り専用の要素 (`$metadata`、`$etag`、`$version`) が含まれています。

### <a name="reported-property-example"></a>報告されるプロパティの例

前の例では、デバイス アプリによって報告される `batteryLevel` プロパティがモジュール ツインに含まれています。 このプロパティでは、最後に報告されたバッテリ レベルに基づいて、モジュール上でクエリや操作を実行できます。 別の例には、モジュール アプリにモジュールの報告機能や接続オプションが含まれています。

> [!NOTE]
> ソリューション バックエンドにおける関心の対象が、最後に確認されているプロパティの値であるような状況では、報告されたプロパティを使用した方が簡単です。 ソリューション バックエンドでタイムスタンプが付けられた連続するイベントの形式でモジュール テレメトリ (時系列など) を処理する必要がある場合は、[device-to-cloud メッセージ](iot-hub-devguide-messages-d2c.md)を使用します。

### <a name="desired-property-example"></a>必要なプロパティの例

上記の例では、ソリューション バックエンドとモジュール アプリが `telemetryConfig` モジュール ツインの必要なプロパティと報告されたプロパティを使用して、モジュールのテレメトリ構成を同期しています。 次に例を示します。

1. ソリューション バックエンドでは、必要なプロパティが必要な構成値で設定されます。 以下は、ドキュメント内の必要なプロパティ セットを使用した部分です。

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. 最初に再接続されたとき、あるいは接続に変更が加えられたときには、モジュール アプリに直ちに通知されます。 その後、モジュール アプリが更新された構成 (またはエラー条件。`status` プロパティを使用) を報告します。 報告されるプロパティの部分を次に示します。

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. ソリューション バックエンドは、モジュール ツインに[クエリを実行](iot-hub-devguide-query-language.md)することによって、複数のモジュールとの間で行われる構成操作の結果を追跡できます。

> [!NOTE]
> 上記のスニペットは、モジュール構成とその状態をエンコードするための例の 1 つであり、読みやすいように最適化されています。 IoT Hub は、モジュール ツインの必要なプロパティや報告されたプロパティに対して、モジュール ツイン用の特定のスキーマを強制しません。
> 
> 

## <a name="back-end-operations"></a>バック エンドの操作
ソリューション バックエンドは、HTTPS を介して公開される次のアトミック操作を使用して、モジュール ツインを操作します。

* **ID を条件にモジュールを取得する**。 この操作は、タグ、必要なシステム プロパティ、報告されるシステム プロパティを含むモジュール ツインのドキュメントを返します。

* **モジュール ツインを部分的に更新する**。 この操作では、ソリューション バックエンドがモジュール ツインのタグまたは必要なプロパティを部分的に更新できます。 部分的な更新は JSON ドキュメントとして表され、プロパティが追加または更新されます。 `null` に設定されたプロパティが削除されます。 次の例では、`{"newProperty": "newValue"}` の値を持つ新しい必要なプロパティが作成され、`existingProperty` の既存の値が `"otherNewValue"` で上書きされ、`otherOldProperty` が削除されます。 それ以外、既にある必要なプロパティまたはタグは変更されません。

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **必要なプロパティの置換** この操作では、ソリューション バックエンドによって既存の必要なプロパティをすべて完全に上書きし、`properties/desired` の新しい JSON ドキュメントに置き換えられます。

* **タグの置換** この操作では、ソリューション バックエンドによって既存のタグをすべて完全に上書きし、`tags` の新しい JSON ドキュメントに置き換えられます。

* **ツイン通知の受信** この操作は、ソリューション バックエンドでツインが変更されたときに通知できるようにします。 そのためには、IoT ソリューションでルートを作成し、データ ソースの値を *twinChangeEvents* に設定する必要があります。 既定では、ツイン通知は送信されません。つまり、このようなルートは事前に存在しません。 変化率が高すぎる場合、または内部エラーなどの理由のために、IoT Hub はすべての変更を含む 1 つの通知のみを送信する場合があります。 そのため、アプリケーションに信頼性の高い監査とすべての中間状態のログ記録が必要な場合は、device-to-cloud メッセージを使用する必要があります。 ツイン通知メッセージには、プロパティおよび本文が含まれます。

  - Properties

    | 名前 | 値 |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  通知が送信された時刻 |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | デバイスの ID |
    moduleId | モジュールの ID |
    hubName | IoT Hub の名前 |
    operationTimestamp | 操作の [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) タイムスタンプ |
    iothub-message-schema | twinChangeNotification |
    opType | "replaceTwin" または "updateTwin" |

    メッセージのシステム プロパティには、`$` シンボルが付きます。

  - Body
        
    このセクションには、すべてのツイン変更が JSON 形式で含まれています。 修正プログラムと同じ形式を使用しますが、すべてのツイン セクション (タグ、properties.reported、properties.desired) を含めることができ、"$metadata" 要素を含みます。 たとえば、次のように入力します。

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

上述の操作はすべて [オプティミスティック コンカレンシー](iot-hub-devguide-device-twins.md#optimistic-concurrency)をサポートしており、「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」の記事で定義されているとおり、**ServiceConnect** アクセス許可を必要とします。

これらの操作に加えて、ソリューション バックエンドでは SQL に似た [IoT Hub クエリ言語](iot-hub-devguide-query-language.md)を使用してモジュール ツインのクエリを実行できます。

## <a name="module-operations"></a>モジュールの操作

モジュール アプリは、次のアトミック操作を使用して、モジュール ツインを操作します。

* **モジュール ツインを取得する** この操作は、タグ、必要なシステム プロパティ、報告されるシステム プロパティを含む、現在接続されているモジュールのモジュール ツインのドキュメントを返します。

* **報告されるプロパティの部分的な更新** この操作では、現在接続されているモジュールの報告されるプロパティを部分的に更新できます。 この操作には、必要なプロパティを部分的に更新する際にソリューション バックエンドで使用されるものと同じ JSON 更新フォーマットが使用されます。

* **必要なプロパティの監視** 現在接続されているモジュールでは、必要なプロパティの更新が行われたときに通知をするように設定できます。 モジュールは、ソリューション バックエンドによって実行される更新 (部分的または完全な置換) と同じフォームを受け取ります。

「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」の記事に定義されているように、上述の操作にはすべて **ModuleConnect** アクセス許可が必要です。

[Azure IoT device SDK](iot-hub-devguide-sdks.md) を使用すると、多数の言語とプラットフォームで上述の操作を簡単に使用できます。

## <a name="tags-and-properties-format"></a>タグやプロパティの形式

タグ、必要なプロパティ、報告されるプロパティは JSON オブジェクトであり、次のような制限があります。

* **キー**: JSON オブジェクト内のすべてのキーは UTF-8 でエンコードされ、大文字と小文字が区別され、最大 1 KB の長さです。 UNICODE 制御文字列 (セグメント C0 と C1)、`.`、`$`、SP は使用できません。

* **値**:JSON オブジェクトのすべての値には、ブール値、数値、文字列、オブジェクトの JSON 型を使用できます。 配列もサポートされています。

    * 使用できる整数の範囲は、-4503599627370496 (最小値) から 4503599627370495 (最大値) までです。

    * 文字列値は UTF-8 でエンコードされ、最大長は 4 KB です。

* **深さ**: タグ、必要なプロパティ、およびレポートされるプロパティにおける JSON オブジェクトの深さは最大で 10 です。 たとえば、次のオブジェクトは有効です。

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="module-twin-size"></a>モジュール ツインのサイズ

IoT Hub では `tags` の値に 8 KB のサイズ制限が適用され、`properties/desired` と `properties/reported` の値にそれぞれ 32 KB のサイズ制限が適用されます。 これらの合計には、`$etag`、`$version`、`$metadata/$lastUpdated` などの読み取り専用の要素は含まれません。

ツインのサイズは、次のように計算されます。

* IoT Hub では、JSON ドキュメント内のプロパティごとに、プロパティのキーと値の長さを累積的に計算して追加します。

* プロパティ キーは、UTF8 でエンコードされた文字列と見なされます。

* 単純なプロパティ値は、UTF8 でエンコードされた文字列、数値 (8 バイト)、またはブール値 (4 バイト) と見なされます。

* UTF8 でエンコードされた文字列のサイズは、UNICODE 制御文字 (セグメント C0 と C1) を除くすべての文字をカウントして計算されます。

* 複合プロパティ値 (入れ子になったオブジェクト) は、プロパティ キーの合計サイズと、そこに含まれるプロパティ値に基づいて計算されます。

ドキュメントのサイズが上述の制限を超えると、IoT Hub はすべての操作を拒否して、エラーを返します。

## <a name="module-twin-metadata"></a>モジュール ツインのメタデータ

IoT Hub は、各 JSON オブジェクトが最後に更新されたときのタイムスタンプを、モジュール ツインの必要なプロパティと報告されるプロパティに保持します。 タイムスタンプは UTC であり、[ISO8601](https://en.wikipedia.org/wiki/ISO_8601) 形式の `YYYY-MM-DDTHH:MM:SS.mmmZ` でエンコードされます。
次に例を示します。

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

この情報は、オブジェクトのキーによって削除される更新を保持するために、JSON の構造のリーフだけでなくすべてのレベルで保持されます。

## <a name="optimistic-concurrency"></a>オプティミスティック コンカレンシー

タグ、必要なプロパティ、報告されたプロパティはすべて、オプティミスティック コンカレンシーをサポートします。
タグは、[RFC7232](https://tools.ietf.org/html/rfc7232) に従って ETag を持ちます。これは、タグの JSON 表現を表します。 ETag を使用すると、条件付き更新操作をソリューション バックエンドから実行し、一貫性を保持できます。

モジュール ツインの必要なプロパティと報告されるプロパティには ETag はありませんが、`$version` の値によって確実に増分されます。 ETag と同様、更新側がバージョンを使用することで更新プログラムの一貫性を確保することができます。 たとえば、報告されたプロパティ用のモジュール アプリ、または目的のプロパティのソリューション バックエンドです。

バージョンは、監視エージェント (必要なプロパティを監視するモジュール アプリなど) が取得操作に関する結果と更新の通知の間の競合を調整する場合に便利です。 詳細については、「[デバイスの再接続フロー](iot-hub-devguide-device-twins.md#device-reconnection-flow)」セクションを参照してください。 

## <a name="next-steps"></a>次のステップ

この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [.NET バックエンドおよび .NET デバイスを使用した Azure IoT Hub モジュール ID とモジュール ツインの概要](iot-hub-csharp-csharp-module-twin-getstarted.md)
