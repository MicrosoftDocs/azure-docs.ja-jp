---
title: IoT Edge ログを取得する - Azure IoT Edge
description: IoT Edge モジュール ログの取得と Azure Blob Storage へのアップロードを行います。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: c06120d1a2e8aa6aa0c006c6f40fed6fab44c5b7
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200697"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>IoT Edge の展開からログを取得する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge エージェント モジュールに含まれているダイレクト メソッドを使用して、デバイスへの物理的または SSH アクセスを必要とせずに、IoT Edge の展開からログを取得します。 ダイレクト メソッドはデバイスに実装され、その後クラウドから呼び出すことができます。 IoT Edge エージェントには、IoT Edge デバイスをリモートで監視および管理できるようにするダイレクト メソッドが含まれています。 この記事で説明するダイレクト メソッドは、1.0.10 リリースで一般提供されています。

ダイレクト メソッドの詳細、使用方法、独自のモジュールに実装する方法については、「[IoT Hub からのダイレクト メソッドの呼び出しについて](../iot-hub/iot-hub-devguide-direct-methods.md)」を参照してください。

これらのダイレクト メソッドの名前は、大文字と小文字を区別して処理されます。

## <a name="recommended-logging-format"></a>推奨されるログ形式

必須ではありませんが、この機能との最適な互換性のために、推奨されているログ形式を次に示します。

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Timestamp}` は `yyyy-MM-dd hh:mm:ss.fff zzz` のように書式設定する必要があり、`{Log Level}` は次の表に従って指定する必要があります。この表にある重大度レベルは、[Syslog 標準の重大度コード](https://wikipedia.org/wiki/Syslog#Severity_level)に基づいたものです。

| 値 | Severity |
|-|-|
| 0 | 緊急 |
| 1 | アラート: |
| 2 | Critical |
| 3 | エラー |
| 4 | 警告 |
| 5 | 注意事項 |
| 6 | Informational |
| 7 | デバッグ |

[IoT Edge の Logger クラス](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs)は、canonical 実装として機能します。

## <a name="retrieve-module-logs"></a>モジュール ログを取得する

**GetModuleLogs** ダイレクト メソッドを使用して、IoT Edge モジュールのログを取得します。

このメソッドは、次のスキーマを持つ JSON ペイロードを受け取ります。

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名前 | 種類 | 説明 |
|-|-|-|
| schemaVersion | string | `1.0` |
| items | JSON 配列 | `id` および `filter` の組を含む配列。 |
| id | string | モジュール名を指定する正規表現。 エッジ デバイス上の複数のモジュールと一致させることができます。 [.NET の正規表現](/dotnet/standard/base-types/regular-expressions)の形式が必要です。 |
| filter | JSON セクション | 組になった `id` 正規表現と一致するモジュールに適用されるログ フィルター。 |
| tail | 整数 (integer) | 最新から始めて取得される過去のログ行の数。 省略可能。 |
| since | string | 期間 (1 日、90 分、2 日 3 時間 2 分)、rfc3339 タイムスタンプ、または UNIX タイムスタンプとして、この時点以降のログのみを返します。  `tail` と `since` の両方が指定された場合、最初に `since` 値を使用してログが取得されます。 次に、`tail` の値が結果に適用され、最終的な結果が返されます。 省略可能。 |
| until | string | rfc3339 タイムスタンプ、UNIX タイムスタンプ、または期間 (1 日、90 分、2 日 3 時間 2 分) として、指定された時点より前のログのみを返します。 省略可能。 |
| ログ レベル | 整数 (integer) | 指定されたログ レベル以下のログ行をフィルター処理します。 ログ行は推奨されるログ形式に従い、[Syslog の重大度レベル](https://en.wikipedia.org/wiki/Syslog#Severity_level)の標準を使用する必要があります。 省略可能。 |
| regex | string | [.NET の正規表現](/dotnet/standard/base-types/regular-expressions)の形式を使用して、指定された正規表現と一致するコンテンツを含むログ行をフィルター処理します。 省略可能。 |
| encoding | string | `gzip` または `none` のいずれかです。 既定値は `none`です。 |
| contentType | string | `json` または `text` のいずれかです。 既定値は `text`です。 |

> [!NOTE]
> ログ コンテンツが、現在 128 KB のダイレクト メソッドの応答サイズの制限を超えた場合、応答はエラーを返します。

ログを正常に取得すると、 **"status":200** が返され、要求で指定した設定によってフィルター処理された、モジュールから取得されたログを含むペイロードが後に続きます。

次に例を示します。

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Azure portal で、メソッド名 `GetModuleLogs` と次の JSON ペイロードを指定してメソッドを呼び出します。

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Azure portal でダイレクト メソッド 'GetModuleLogs' を呼び出す](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

また、CLI 出力を [gzip](https://en.wikipedia.org/wiki/Gzip) のような Linux ユーティリティにパイプして、圧縮された応答を処理することもできます。 次に例を示します。

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>モジュール ログをアップロードする

**UploadModuleLogs** ダイレクト メソッドを使用して、指定された Azure Blob Storage コンテナーに要求されたログを送信します。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> ゲートウェイ デバイスの背後にあるデバイスからログをアップロードする場合は、[API プロキシと Blob Storage モジュール](how-to-configure-api-proxy-module.md)が最上位層デバイスに構成されている必要があります。 これらのモジュールは、ゲートウェイ デバイスを介して下位層のデバイスからクラウド内のストレージにログをルーティングします。

::: moniker-end

このメソッドは、"sasUrl" キーを追加することで、**GetModuleLogs** に似た JSON ペイロードを受け入れます。

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名前 | 種類 | 説明 |
|-|-|-|
| sasURL | string (URI) | [Azure Blob Storage コンテナーへの書き込みアクセスを含む共有アクセス署名 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer)。 |

ログのアップロード要求が成功すると、 **"status":200** が返され、次のスキーマを持つペイロードが後に続きます。

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名前 | 種類 | 説明 |
|-|-|-|
| status | string | `NotStarted`、`Running`、`Completed`、`Failed`、または `Unknown`の 1 つ。 |
| message | string | エラーの場合はメッセージ、それ以外の場合は空の文字列。 |
| correlationId | string   | アップロード要求の状態を照会するための ID。 |

次に例を示します。

次の呼び出しによって、すべてのモジュールから最新の100 のログ行を、圧縮された JSON 形式でアップロードします。

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

次の呼び出しによって、edgeAgent および edgeHub からの最新の 100 のログ行と、tempSensor モジュールからの最新の 1000 のログ行が、圧縮されていないテキスト形式でアップロードします。

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

Azure portal で、次の情報を含めた sasURL を指定した後に、メソッド名 `UploadModuleLogs` と次の JSON ペイロードを指定してメソッドを呼び出します。

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Azure portal でダイレクト メソッド 'UploadModuleLogs' を呼び出す](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>サポート バンドル診断をアップロードする

**UploadSupportBundle** ダイレクト メソッドを使用して、IoT Edge モジュール ログの zip ファイルをバンドルし、使用可能な Azure Blob Storage コンテナーにアップロードします。 このダイレクト メソッドによって、IoT Edge デバイス上で [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) コマンドが実行されて、ログが取得されます。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> ゲートウェイ デバイスの背後にあるデバイスからログをアップロードする場合は、[API プロキシと Blob Storage モジュール](how-to-configure-api-proxy-module.md)が最上位層デバイスに構成されている必要があります。 これらのモジュールは、ゲートウェイ デバイスを介して下位層のデバイスからクラウド内のストレージにログをルーティングします。

::: moniker-end

このメソッドは、次のスキーマを持つ JSON ペイロードを受け取ります。

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| 名前 | 種類 | 説明 |
|-|-|-|
| schemaVersion | string | `1.0` |
| sasURL | string (URI) | [Azure Blob Storage コンテナーへの書き込みアクセスを含む共有アクセス署名 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| since | string | 期間 (1 日、90 分、2 日 3 時間 2 分)、rfc3339 タイムスタンプ、または UNIX タイムスタンプとして、この時点以降のログのみを返します。 省略可能。 |
| until | string | rfc3339 タイムスタンプ、UNIX タイムスタンプ、または期間 (1 日、90 分、2 日 3 時間 2 分) として、指定された時点より前のログのみを返します。 省略可能。 |
| edgeRuntimeOnly | boolean | true の場合、Edge Agent、Edge Hub、および Edge Security Daemon からのログのみを返します。 既定値は false です。  省略可能。 |

> [!IMPORTANT]
> IoT Edge サポート バンドルには、個人を特定できる情報が含まれている場合があります。

ログのアップロード要求が成功すると、 **"status":200** が返され、**UploadModuleLogs** 応答と同じスキーマを持つペイロードが後に続きます。

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名前 | 種類 | 説明 |
|-|-|-|
| status | string | `NotStarted`、`Running`、`Completed`、`Failed`、または `Unknown`の 1 つ。 |
| message | string | エラーの場合はメッセージ、それ以外の場合は空の文字列。 |
| correlationId | string   | アップロード要求の状態を照会するための ID。 |

次に例を示します。

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

Azure portal で、次の情報を含めた sasURL を指定した後に、メソッド名 `UploadSupportBundle` と次の JSON ペイロードを指定してメソッドを呼び出します。

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Azure portal でダイレクト メソッド 'UploadSupportBundle' を呼び出す](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>アップロード要求の状態を取得する

**GetTaskStatus** ダイレクト メソッドを使用して、アップロード ログ要求の状態を照会します。 **GetTaskStatus** 要求ペイロードは、アップロード ログ要求の `correlationId` を使用して、タスクの状態を取得します。 `correlationId` は、**UploadModuleLogs** ダイレクト メソッド呼び出しの応答内で返されます。

このメソッドは、次のスキーマを持つ JSON ペイロードを受け取ります。

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

ログのアップロード要求が成功すると、 **"status":200** が返され、**UploadModuleLogs** 応答と同じスキーマを持つペイロードが後に続きます。

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名前 | 種類 | 説明 |
|-|-|-|
| status | string | `NotStarted`、`Running`、`Completed`、`Failed`、または `Unknown`の 1 つ。 |
| message | string | エラーの場合はメッセージ、それ以外の場合は空の文字列。 |
| correlationId | string   | アップロード要求の状態を照会するための ID。 |

次に例を示します。

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

Azure portal で、次の情報を含めた GUID を指定した後に、メソッド名 `GetTaskStatus` と次の JSON ペイロードを指定してメソッドを呼び出します。

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Azure portal でダイレクト メソッド 'GetTaskStatus' を呼び出す](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>次のステップ

[IoT Edge エージェントと IoT Edge ハブのモジュール ツインのプロパティ](module-edgeagent-edgehub.md)
