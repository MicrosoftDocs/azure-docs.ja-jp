---
title: Azure Monitor HTTP データ コレクター API | Microsoft Docs
description: Azure Monitor HTTP データ コレクター API を使用すると、REST API を呼び出すことのできる任意のクライアントから POST JSON データを Log Analytics ワークスペースに追加できます。 この記事では、この API の使用方法について説明し、さまざまなプログラミング言語を使用してデータを発行する方法の例を紹介します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: e32bf95ef52fdd081eeaa476f44bf5dab99657d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452120"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>HTTP データ コレクター API を使用した Azure Monitor へのログ データの送信 (パブリック プレビュー)
この記事では、HTTP データ コレクター API を使用して REST API クライアントから Azure Monitor にログ データを送信する方法を示します。  ここでは、スクリプトまたはアプリケーションによって収集されたデータの形式を設定して要求に含め、その要求を Azure Monitor に承認させる方法を説明します。  PowerShell、C#、および Python の例を示します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Azure Monitor HTTP データ コレクター API は、パブリック プレビュー段階にあります。

## <a name="concepts"></a>概念
HTTP データ コレクター API を使用すると、REST API を呼び出すことのできる任意のクライアントから Azure Monitor 内の Log Analytics ワークスペースにログ データを送信できます。  これは、Azure または別のクラウドから管理データを収集する Azure Automation での Runbook や、Azure Monitor を使用してログ データを統合して分析する他の管理システムが考えられます。

Log Analytics ワークスペース内のすべてのデータは、特定のレコード型のレコードとして保存されます。  HTTP データ コレクター API に送信するデータを、JSON 形式の複数のレコードとして設定します。  データを送信すると、要求ペイロード内の各レコードに対応する個別のレコードがリポジトリ内に作成されます。


![HTTP データ コレクターの概要](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>要求を作成する
HTTP データ コレクター API を使用するには、JavaScript Object Notation (JSON) で送信するデータを含む POST 要求を作成します。  次の 3 つの表に、各要求で必要な属性の一覧を示します。 この記事の後半で、各属性についてより詳しく説明します。

### <a name="request-uri"></a>要求 URI
| 属性 | プロパティ |
|:--- |:--- |
| Method |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Content type |application/json |

### <a name="request-uri-parameters"></a>要求 URI のパラメーター
| パラメーター | 説明 |
|:--- |:--- |
| CustomerID |Log Analytics ワークスペースの一意識別子です。 |
| リソース |API のリソース名は "/api/logs" です。 |
| API Version |この要求で使用する API のバージョン。 現時点では "2016-04-01" です。 |

### <a name="request-headers"></a>要求ヘッダー
| ヘッダー | 説明 |
|:--- |:--- |
| 承認 |承認の署名。 HMAC-SHA256 ヘッダーの作成方法については、この記事の後半で説明します。 |
| Log-Type |送信中のデータのレコード型を指定します。 文字、数字、アンダースコア (_) のみを含めることができ、100 文字を超えることはできません。 |
| x-ms-date |RFC 1123 形式による、要求が処理された日付。 |
| x-ms-AzureResourceId | データを関連付ける必要がある Azure リソースのリソース ID。 これにより、[_ResourceId](./log-standard-columns.md#_resourceid) プロパティに値が設定され、[リソース コンテキスト](./design-logs-deployment.md#access-mode) クエリにデータを含めることができます。 このフィールドが指定されない場合、リソース コンテキスト クエリにデータは含まれません。 |
| time-generated-field | データ項目のタイムスタンプを含む、データ内のフィールドの名前。 フィールドを指定すると、フィールドのコンテンツは **TimeGenerated** の値に使用されます。 このフィールドを指定しない場合、**TimeGenerated** の既定値は、メッセージが取り込まれた時刻になります。 メッセージ フィールドのコンテンツは、ISO 8601 形式 (YYYY-MM-DDThh:mm:ssZ) である必要があります。 |

## <a name="authorization"></a>承認
Azure Monitor HTTP データ コレクター API への要求には、Authorization ヘッダーを含める必要があります。 要求を認証するには、その要求を行っているワークスペースの主キーまたはセカンダリ キーのどちらかを使用して、要求に署名する必要があります。 次に、その署名を要求の一部として渡します。   

承認ヘッダーの形式を次に示します。

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* は、Log Analytics ワークスペースの一意識別子です。 *Signature* は、要求で構築されてから、[SHA256 アルゴリズム](/dotnet/api/system.security.cryptography.sha256)を使用して計算される [ハッシュベース メッセージ認証コード (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256) です。 このコードを Base64 エンコーディングを使用してエンコードします。

**SharedKey** 署名文字列をエンコードするには、次の形式を使用します。

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

署名文字列の例を次に示します。

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

署名文字列がある場合、UTF-8 でエンコードされた文字列で HMAC-SHA256 アルゴリズムを使用してエンコードしてから、その結果を Base64 としてエンコードします。 次の形式を使用します。

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

次のセクションのサンプルには、承認ヘッダーを作成するのに役立つサンプル コードが含まれています。

## <a name="request-body"></a>要求本文
メッセージの本文は JSON 形式である必要があります。 ここには、プロパティ名と値がペアになったレコードを、次の形式で 1 つ以上含める必要があります。 プロパティ名には、文字、数字、アンダースコア (_) のみを使用できます。

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

次の形式を使用して複数のレコードを 1 つの要求にまとめることができます。 すべてのレコードは同じレコード型である必要があります。

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>レコードの型とプロパティ
Azure Monitor HTTP データ コレクター API 経由でデータを送信する場合、カスタム レコード型を定義します。 現時点では、他のデータ型およびソリューションによって作成された既存のレコード型にデータを書き込むことはできません。 Azure Monitor では受信データを読み取り、入力した値のデータ型に一致するプロパティを作成します。

データ コレクター API への各要求には、レコード型の名前が付いた **Log-Type** ヘッダーを含める必要があります。 サフィックス **_CL** は、カスタム ログと他のログの種類を区別するために、入力した名前の最後に自動的に追加されます。 たとえば、名前を「**MyNewRecordType**」と入力した場合、Azure Monitor では **MyNewRecordType_CL** という型のレコードが作成されます。 これにより、ユーザーが作成した型名と現在または将来の Microsoft ソリューションで提供される型名が競合することはありません。

プロパティのデータ型を識別するために、Azure Monitor では、プロパティ名にサフィックスを追加します。 プロパティに null 値が含まれる場合、そのプロパティはレコードには含まれません。 次の表に、プロパティのデータ型と対応するサフィックスの一覧を示します。

| プロパティのデータ型 | サフィックス |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Date/time |_t |
| GUID (文字列として格納) |_g |

> [!NOTE]
> GUID として表示される文字列値は、受信した値にダッシュが含まれていない場合でも、_g サフィックスが付加され、GUID として書式設定されます。 たとえば、"8145d822-13a7-44ad-859c-36f31a84f6dd" と "8145d82213a744ad859c36f31a84f6dd" はどちらも、"8145d822-13a7-44ad-859c-36f31a84f6dd" として格納されます。 これと別の文字列の唯一の違いは、名前の _g と、入力で指定されていない場合のダッシュの挿入です。 

Azure Monitor において各プロパティに対して使用されるデータ型は、新しいレコードのレコード型が既に存在するかどうかによって異なります。

* レコード型が存在しない場合、Azure Monitor では、新しいレコードの各プロパティのデータ型を判定するために JSON 型の推定を利用して、新しく作成します。
* レコード型が存在する場合、Azure Monitor では、既存のプロパティに基づいて新しいレコードの作成を試行します。 新しいレコードにおいてプロパティのデータ型が既存の型と一致せず、既存の型に変換することもできない場合や、存在しないプロパティがレコードに含まれる場合、Azure Monitor によって関連性のあるサフィックスを持つ新しいプロパティが作成されます。

たとえば、次のような送信エントリには、**number_d**、**boolean_b**、**string_s** の 3 つのプロパティを持つレコードが作成されます。

![サンプル レコード 1](media/data-collector-api/record-01.png)

次に、すべての値が文字列で記述された下記のようなエントリを送信する場合、プロパティは変更されません。 これらの値は、既存のデータ型に変換されます。

![サンプル レコード 2](media/data-collector-api/record-02.png)

しかし、下記のような送信を行った場合、Azure Monitor では、**boolean_d** および **string_d** という新しいプロパティを作成します。 これらの値は、既存のデータ型に変換できません。

![サンプル レコード 3](media/data-collector-api/record-03.png)

次に、レコード型が作成される前に、下記のようなエントリを送信した場合、Azure Monitor では **number_s**、**boolean_s**、および **string_s** という 3 つのプロパティを含むレコードが作成されます。 このエントリでは、初期の値はそれぞれ文字列の形式で指定されています。

![サンプル レコード 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>予約済みプロパティ
次のプロパティは予約済みであり、カスタム レコードの種類で使用することはできません。 これらのプロパティ名のいずれかがペイロードに含まれている場合、エラーが表示されます。

- tenant

## <a name="data-limits"></a>データ制限
Azure Monitor データ収集の API に送信するデータに関しては、いくつかの制約があります。

* Azure Monitor データ コレクター API に対する送信ごとの上限は 30 MB です。 これは 1 回の送信のサイズ制限です。 1 回の送信のデータ サイズが 30 MB を超える場合は、データを小さなサイズのチャンクに分割し、それらを同時に送信する必要があります。
* フィールド値の上限は 32 KB です。 フィールド値が 32 KB を超えた場合、データは切り捨てられます。
* 特定の種類のフィールドの推奨される最大数は 50 個です。 これは、使いやすさと検索エクスペリエンスの観点からの実質的な制限です。  
* Log Analytics ワークスペース内のテーブルでは、最大で 500 列のみがサポートされます (この記事では、フィールドとして参照されます)。 
* 列名の最大文字数は 500 です。

## <a name="return-codes"></a>リターン コード
HTTP 状態コード 200 は、要求が処理するために受信されたことを意味します。 これは、操作が正常に完了したことを示します。

次の表に、サービスから返される可能性のあるすべての状態コードの一覧を示します。

| コード | Status | エラー コード | 説明 |
|:--- |:--- |:--- |:--- |
| 200 |[OK] | |要求は正常に受け入れられました。 |
| 400 |正しくない要求 |InactiveCustomer |このワークスペースは閉じられています。 |
| 400 |正しくない要求 |InvalidApiVersion |指定した API のバージョンがサービスに認識されませんでした。 |
| 400 |正しくない要求 |InvalidCustomerId |指定したワークスペース ID が無効です。 |
| 400 |正しくない要求 |InvalidDataFormat |無効な JSON が送信されました。 応答本文に、このエラーを解決する方法に関する詳細が含まれていることがあります。 |
| 400 |正しくない要求 |InvalidLogType |指定したログの種類に、特殊文字または数字が含まれていました。 |
| 400 |正しくない要求 |MissingApiVersion |API のバージョンが指定されていませんでした。 |
| 400 |正しくない要求 |MissingContentType |コンテンツの種類が指定されていませんでした。 |
| 400 |正しくない要求 |MissingLogType |必須の値であるログの種類が指定されていませんでした。 |
| 400 |正しくない要求 |UnsupportedContentType |コンテンツの種類が **application/json** に設定されていませんでした。 |
| 403 |Forbidden |InvalidAuthorization |サービスで要求を認証できませんでした。 ワークスペース ID と接続キーが有効であるかどうかを確認してください。 |
| 404 |見つかりません | | 指定された URL が正しくないか、要求が大きすぎます。 |
| 429 |要求が多すぎます | | サービスはアカウントの大量のデータを処理しています。 後で要求を再試行してください。 |
| 500 |内部サーバー エラー |UnspecifiedError |サービスで内部エラーが発生しました。 要求を再試行してください。 |
| 503 |サービス利用不可 |ServiceUnavailable |サービスは現在、要求を受信できません。 要求を再試行してください。 |

## <a name="query-data"></a>クエリ データ
Azure Monitor HTTP データ コレクター API によって送信されたデータを照会するには、**Type** (指定した **LogType** の値の末尾に **_CL** を追加したものと同じ) でレコードを検索します。 たとえば、**MyCustomLog** を使用した場合、`MyCustomLog_CL` があるすべてのレコードが返されます。

## <a name="sample-requests"></a>サンプルの要求
以降のセクションでは、さまざまなプログラミング言語を使用して Azure Monitor HTTP データ コレクター API にデータを送信する方法のサンプルを示します。

サンプルごとに、次の手順を実行して承認ヘッダーの変数を設定します。

1. Azure Portal で、Log Analytics ワークスペースを検索します。
2. **[エージェント管理]** を選択します。
2. **[ワークスペース ID]** の右側にあるコピー アイコンを選択し、**Customer ID** 変数の値としてその ID を貼り付けます。
3. **[主キー]** の右側にあるコピー アイコンを選択し、**Shared Key** 変数の値としてその ID を貼り付けます。

別の方法として、ログの種類および JSON データの変数を変更することもできます。

### <a name="powershell-sample"></a>PowerShell のサンプル
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C# のサンプル
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2 のサンプル
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Python 3 のサンプル
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Java サンプル

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC254(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC254(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha254HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha254HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha254HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>代替手段と考慮事項
Data Collector API は、自由形式のデータを Azure ログに収集する際のほとんどのニーズに対応しますが、この API の一部の制限を克服するために代替手段が必要になる場合があります。 主な考慮事項を含め、すべてのオプションを以下に示します。

| 代替手段 | 説明 | 最も適しているデータ |
|---|---|---|
| [カスタム イベント](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Application Insights でのネイティブ SDK ベースのインジェスト | 通常はアプリケーション内で SDK を使用してインストルメント化される Application Insights では、カスタム イベントを使用してカスタム データを送信できます。 | <ul><li> アプリケーション内で生成されるが、SDK によって既定のデータの種類 (要求、依存関係、例外など) のいずれかとして取得されないデータ。</li><li> Application Insights で他のアプリケーション データに最も頻繁に関連付けられるデータ。 </li></ul> |
| Azure Monitor ログの Data Collector API | Azure Monitor ログの Data Collector API は、データを取り込むための完全に拡張可能な方法です。 JSON オブジェクト形式のデータはすべてここで送信できます。 送信されたデータが処理され、ログ内の他のデータや他の Application Insights データと関連付けるためにログで使用できるようになります。 <br/><br/> データは、ファイルとして Azure BLOB に簡単にアップロードできます。これらのファイルは Azure BLOB で処理され、Log Analytics にアップロードされます。 このようなパイプラインの実装例については、[こちら](./create-pipeline-datacollector-api.md)の記事をご覧ください。 | <ul><li> Application Insights 内でインストルメント化されたアプリケーション内で必ずしも生成されるわけではないデータ。</li><li> 例として、ルックアップ テーブル、ファクト テーブル、参照データ、事前に集計された統計などがあります。 </li><li> 他の Azure Monitor データ (Application Insights、他のログ データの種類、Security Center、Container insights と VM など) と相互参照されるデータ。 </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | Azure Data Explorer (ADX) は、Application Insights Analytics と Azure Monitor ログを強化するデータ プラットフォームです。 一般提供 ("GA") が開始されたこのデータ プラットフォームを raw 形式で使用すると、クラスターに対する完全な柔軟性 (Kubernetes RBAC、リテンション率、スキーマなど) が得られます (ただし、管理オーバーヘッドが必要になります)。 ADX には、[CSV、TSV、JSON](/azure/kusto/management/mappings) の各ファイルを含め、多数の[インジェスト オプション](/azure/data-explorer/ingest-data-overview#ingestion-methods)が用意されています。 | <ul><li> Application Insights またはログで他のどのデータにも関連付けられないデータ。 </li><li> Azure Monitor ログで現在提供されていない高度なインジェスト機能や処理機能を必要とするデータ。 </li></ul> |


## <a name="next-steps"></a>次のステップ
- [Log Search API](./log-query-overview.md) を使用して Log Analytics ワークスペースからデータを取得する

- Azure Monitor への Logic Apps ワークフローを使用して[データ コレクター API によってデータ パイプラインを作成する](create-pipeline-datacollector-api.md)方法を学ぶ