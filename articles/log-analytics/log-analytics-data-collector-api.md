---
title: "Log Analytics HTTP データ コレクター API | Microsoft Docs"
description: "Log Analytics HTTP データ コレクター API を使用すると、REST API を呼び出すことのできるクライアントから POST JSON データを Log Analytics リポジトリに追加できます。 この記事では、この API の使用方法について説明し、さまざまなプログラミング言語を使用してデータを発行する方法の例を紹介します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2b5899ba43f651ae6f5fdf84d7aa5ee35d81b738
ms.openlocfilehash: be27695cd1d998eedff0ca76f6ae9d4ff69bb97b
ms.lasthandoff: 01/05/2017


---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api"></a>HTTP データ コレクター API を使用した Log Analytics へのデータの送信
この記事では、HTTP データ コレクター API を使用して REST API クライアントから Log Analytics にデータを送信する方法を示します。  ここでは、スクリプトまたはアプリケーションによって収集されたデータの形式を設定して要求に含め、その要求を Log Analytics に承認させる方法を説明します。  PowerShell、C#、および Python の例を示します。

## <a name="concepts"></a>概念
REST API を呼び出すことができる任意のクライアントから、HTTP データ コレクター API を使用して Log Analytics にデータを送信できます。  これは、Azure または別のクラウドから管理データを収集する Azure Automation の Runbookや、Log Analytics を使用してデータを統合して分析するログ分析を使用する他の管理システムが考えられます。

Log Analytics リポジトリ内のすべてのデータは、特定の種類のレコードとして保存されます。  HTTP データ コレクター API に送信するデータを、JSON 形式の複数のレコードとして設定します。  データを送信すると、要求ペイロード内の各レコードに対応する個別のレコードがリポジトリ内に作成されます。


![HTTP データ コレクターの概要](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>要求を作成する
HTTP データ コレクター API を使用するには、JavaScript Object Notation (JSON) で送信するデータを含む POST 要求を作成します。  次の&3; つの表に、各要求で必要な属性の一覧を示します。 この記事の後半で、各属性についてより詳しく説明します。

### <a name="request-uri"></a>要求 URI
| 属性 | プロパティ |
|:--- |:--- |
| メソッド |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| コンテンツの種類 |application/json |

### <a name="request-uri-parameters"></a>要求 URI のパラメーター
| パラメーター | 説明 |
|:--- |:--- |
| CustomerID |Microsoft Operations Management Suite ワークスペースの一意の識別子。 |
| リソース |API のリソース名は "/api/logs" です。 |
| API バージョン |この要求で使用する API のバージョン。 現時点では "2016-04-01" です。 |

### <a name="request-headers"></a>要求ヘッダー
| ヘッダー | 説明 |
|:--- |:--- |
| 承認 |承認の署名。 HMAC-SHA256 ヘッダーの作成方法については、この記事の後半で説明します。 |
| Log-Type |送信中のデータのレコード型を指定します。 現在、ログの種類でサポートされているのは、アルファベットのみです。 数字や特殊文字はサポートされていません。 |
| x-ms-date |RFC 1123 形式による、要求が処理された日付。 |
| time-generated-field |データ項目のタイムスタンプを含む、データ内のフィールドの名前。 フィールドを指定すると、フィールドのコンテンツは **TimeGenerated** の値に使用されます。 このフィールドを指定しない場合、**TimeGenerated** の既定値は、メッセージが取り込まれた時刻になります。 メッセージ フィールドのコンテンツは、ISO 8601 形式 (YYYY-MM-DDThh:mm:ssZ) である必要があります。 |

## <a name="authorization"></a>承認
Log Analytics HTTP データ コレクター API への要求には、承認ヘッダーを含める必要があります。 要求を認証するには、その要求を行っているワークスペースの主キーまたはセカンダリ キーのどちらかを使用して、要求に署名する必要があります。 次に、その署名を要求の一部として渡します。   

承認ヘッダーの形式を次に示します。

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* は Operations Management Suite ワークスペースの一意の識別子です。 *Signature* は、要求で構築されてから、[SHA256 アルゴリズム](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx)を使用して計算される[ハッシュベース メッセージ認証コード (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) です。 このコードを Base64 エンコーディングを使用してエンコードします。

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
メッセージの本文は JSON 形式である必要があります。 次の形式でプロパティ名と値をペアにして、レコードを&1; つ以上含める必要があります。

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

次の形式を使用して複数のレコードを&1; つの要求にまとめることができます。 すべてのレコードは同じレコード型である必要があります。

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>レコードの型とプロパティ
Log Analytics HTTP データ コレクター API 経由でデータを送信する場合、カスタム レコード型を定義します。 現時点では、他のデータ型およびソリューションによって作成された既存のレコード型にデータを書き込むことはできません。 Log Analytics は受信データを読み取り、入力した値のデータ型に一致するプロパティを作成します。

Log Analytics API への各要求には、レコード型の名前が付いた **Log-Type** ヘッダーを含める必要があります。 サフィックス **_CL** は、カスタム ログと他のログの種類を区別するために、入力した名前の最後に自動的に追加されます。 たとえば、「**MyNewRecordType**」と入力した場合、Log Analytics によって **MyNewRecordType_CL** という型のレコードが作成されます。 これにより、ユーザーが作成した型名と現在または将来の Microsoft ソリューションで提供される型名が競合することはありません。

プロパティのデータ型を識別するために、Log Analytics によってプロパティ名にサフィックスが追加されます。 プロパティに null 値が含まれる場合、そのプロパティはレコードには含まれません。 次の表に、プロパティのデータ型と対応するサフィックスの一覧を示します。

| プロパティのデータ型 | サフィックス |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Date/time |_t |
| GUID |_g |

各プロパティに対して Log Analytics が使用するデータ型は、新しいレコードのレコード型が既に存在するかどうかによって異なります。

* レコード型が存在しない場合、Log Analytics によって新しいレコード型が作成されます。 新しいレコードの各プロパティに対してデータ型を決定するために、Log Analytics では JSON 型推論が使用されます。
* レコード型が存在する場合、Log Analytics によって、既存のプロパティに基づいて新しいレコードの作成が試みられます。 新しいレコードのプロパティのデータ型が既存の型と一致せず、変換することもできない場合や、存在しないプロパティを含むレコードの場合、Log Analytics によって関連性のあるサフィックスを持つ新しいプロパティが作成されます。

たとえば、次のような送信エントリには、**number_d**、**boolean_b**、**string_s** の&3; つのプロパティを持つレコードが作成されます。

![サンプル レコード 1](media/log-analytics-data-collector-api/record-01.png)

次に、すべての値が文字列で記述された下記のようなエントリを送信する場合、プロパティは変更されません。 これらの値は、既存のデータ型に変換されます。

![サンプル レコード 2](media/log-analytics-data-collector-api/record-02.png)

一方、下記のようなエントリを送信する場合、Log Analytics によって、**boolean_d** および **string_d** という新しいプロパティが作成されます。 これらの値は、既存のデータ型に変換できません。

![サンプル レコード 3](media/log-analytics-data-collector-api/record-03.png)

次に、レコード型が作成される前に、下記のようなエントリを送信する場合、Log Analytics によって **number_s**、**boolean_s**、**string_s** という&3; つのプロパティを含むレコードが作成されます。 このエントリでは、初期の値はそれぞれ文字列の形式で指定されています。

![サンプル レコード 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>データ制限
Log Analytics データ収集 API に送信するデータに関して制約がいくつかあります。

* Log Analytics データ コレクター API に送信するデータの上限は 30 MB です。 これは&1; 回の送信のサイズ制限です。 1 回の送信のデータ サイズが 30 MB を超える場合は、データを小さなサイズのチャンクに分割し、それらを同時に送信する必要があります。 
* フィールド値の上限は 32 KB です。 フィールド値が 32 KB を超えた場合、データは切り捨てられます。 
* 特定の種類のフィールドの推奨される最大数は 50 個です。 これは、使いやすさと検索エクスペリエンスの観点からの実質的な制限です。  

## <a name="return-codes"></a>リターン コード
HTTP 状態コード 200 は、要求が処理するために受信されたことを意味します。 これは、操作が正常に完了したことを示します。

次の表に、サービスから返される可能性のあるすべての状態コードの一覧を示します。

| コード | 状態 | エラー コード | Description |
|:--- |:--- |:--- |:--- |
| 200 |OK | |要求は正常に受け入れられました。 |
| 400 |正しくない要求 |InactiveCustomer |このワークスペースは閉じられています。 |
| 400 |正しくない要求 |InvalidApiVersion |指定した API のバージョンがサービスに認識されませんでした。 |
| 400 |正しくない要求 |InvalidCustomerId |指定したワークスペース ID が無効です。 |
| 400 |正しくない要求 |InvalidDataFormat |無効な JSON が送信されました。 応答本文に、このエラーを解決する方法に関する詳細が含まれていることがあります。 |
| 400 |正しくない要求 |InvalidLogType |指定したログの種類に、特殊文字または数字が含まれていました。 |
| 400 |正しくない要求 |MissingApiVersion |API のバージョンが指定されていませんでした。 |
| 400 |正しくない要求 |MissingContentType |コンテンツの種類が指定されていませんでした。 |
| 400 |正しくない要求 |MissingLogType |必須の値であるログの種類が指定されていませんでした。 |
| 400 |正しくない要求 |UnsupportedContentType |コンテンツの種類が **application/json** に設定されていませんでした。 |
| 403 |許可されていません |InvalidAuthorization |サービスで要求を認証できませんでした。 ワークスペース ID と接続キーが有効であるかどうかを確認してください。 |
| 404 |見つかりません | | 指定された URL が正しくないか、要求が大きすぎます。 |
| 429 |要求が多すぎます | | サービスはアカウントの大量のデータを処理しています。 後で要求を再試行してください。 |
| 500 |内部サーバー エラー |UnspecifiedError |サービスで内部エラーが発生しました。 要求を再試行してください。 |
| 503 |サービス利用不可 |ServiceUnavailable |サービスは現在、要求を受信できません。 要求を再試行してください。 |

## <a name="query-data"></a>データのクエリを実行する
Log Analytics HTTP データ コレクター API によって送信されたデータを照会するには、**Type** (指定した **LogType** の値の末尾に **_CL** を追加したもの) でレコードを検索します。 たとえば、**MyCustomLog** を使用した場合、**Type=MyCustomLog_CL** のすべてのレコードが返されます。

## <a name="sample-requests"></a>サンプルの要求
次のセクションでは、さまざまなプログラミング言語を使用して Log Analytics HTTP データ コレクター API にデータを送信する方法のサンプルを示します。

サンプルごとに、次の手順を実行して承認ヘッダーの変数を設定します。

1. Operations Management Suite ポータルで、**[設定]** タイル、**[接続されたソース]** タブの順に選択します。
2. **[ワークスペース ID]** の右側にあるコピー アイコンを選択し、**Customer ID** 変数の値としてその ID を貼り付けます。
3. **[主キー]** の右側にあるコピー アイコンを選択し、**Shared Key** 変数の値としてその ID を貼り付けます。

別の方法として、ログの種類および JSON データの変数を変更することもできます。

### <a name="powershell-sample"></a>PowerShell のサンプル
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
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
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
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
        -fileName $fileName `
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
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C# のサンプル
```
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

        // Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
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

### <a name="python-sample"></a>Python のサンプル
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
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

## <a name="next-steps"></a>次のステップ
- Log Analytics リポジトリから [Log Search API](log-analytics-log-search-api.md) を使用してデータを取得する

