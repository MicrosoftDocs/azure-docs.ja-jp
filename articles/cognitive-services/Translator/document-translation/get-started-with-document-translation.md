---
title: ドキュメント変換を使ってみる
description: C#、Go、Java、Node.js、または Python のプログラミング言語とプラットフォームを使用してドキュメント翻訳サービスを作成する方法
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: 21df853d9b1c7250e9a6eea37a68835a180f610d
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773047"
---
# <a name="get-started-with-document-translation-preview"></a>ドキュメント翻訳を使ってみる (プレビュー)

 この記事では、HTTP REST API メソッドでドキュメント翻訳を使用する方法について説明します。 ドキュメント翻訳は、[Azure Translator](../translator-info-overview.md) サービスのクラウドベースの機能です。  ドキュメント翻訳 API は、ソース ドキュメントの構造とテキストの書式設定を保持しながら、ドキュメント全体を翻訳できるようにします。

## <a name="prerequisites"></a>前提条件

> [!NOTE]
>
> 1. 通常、Azure portal で Cognitive Service リソースを作成するときは、マルチサービスのサブスクリプション キーまたは単一サービスのサブスクリプション キーを作成するという選択肢があります。 ただし、ドキュメント翻訳は現在 Translator (単一サービス) リソースでのみサポートされているため、Cognitive Services (マルチサービス) リソースには含まれて **いません**。
> 2. ドキュメント翻訳は、現在、**S1 Standard サービス プラン** でご利用いただけます。 「[Cognitive Services の価格 - Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator/)」を _参照_ してください。
>

作業を開始するには、以下が必要です。

* アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)。  アカウントがない場合は、[**無料アカウントを作成**](https://azure.microsoft.com/free/)できます。

* [**Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) サービス リソース (Cognitive Services リソース **ではない**)。

* [**Azure Blob Storage アカウント**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 ストレージ アカウント内に BLOB データを格納して整理するためのコンテナーを作成します。

## <a name="get-your-custom-domain-name-and-subscription-key"></a>カスタム ドメイン名とサブスクリプション キーを取得する

> [!IMPORTANT]
>
> * ドキュメント翻訳への HTTP 要求を行うために、Azure portal リソースの _[キーとエンドポイント]_ ページのエンドポイントも、グローバル Translator エンドポイント `api.cognitive.microsofttranslator.com` も使用することはありません。
> * **ドキュメント翻訳サービスへのすべての API 要求には、カスタム ドメイン エンドポイントが必要です**。

### <a name="what-is-the-custom-domain-endpoint"></a>カスタム ドメイン エンドポイントとは

カスタム ドメイン エンドポイントは、リソース名、ホスト名、Translator サブディレクトリを指定した形式の URL です。

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>カスタム ドメイン名を確認する

**NAME-OF-YOUR-RESOURCE** ("*カスタム ドメイン名*" とも呼ばれます) パラメーターは、Translator リソースの作成時に **[Name]\(名前\)** フィールドに入力した値です。

:::image type="content" source="../media/instance-details.png" alt-text="Azure portal、リソースの作成、インスタンスの詳細、名前のフィールドの画像。":::

### <a name="get-your-subscription-key"></a>サブスクリプション キーを取得する

Translator サービスへの要求には、アクセス認証を受けるための読み取り専用キーが必要です。

1. 新しいリソースを作成した場合は、そのデプロイ後に **[リソースに移動]** を選択します。 既存のドキュメント翻訳リソースがある場合は、リソース ページに直接移動します。
1. 左側のレールの *[リソース管理]* で、 **[キーとエンドポイント]** を選択します。
1. 自分のサブスクリプション キーをコピーして、使いやすい場所 ("*Microsoft メモ帳*" など) に貼り付けます。
1. それを、ドキュメント翻訳サービスへの要求の認証のための次のコード内に貼り付けます。

:::image type="content" source="../media/translator-keys.png" alt-text="Azure portal 内のサブスクリプション キーの取得のフィールドの画像。":::

## <a name="create-your-azure-blob-storage-containers"></a>Azure Blob Storage コンテナーを作成する

[**Azure Blob Storage アカウント**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)内に、ソース、ターゲット、省略可能な用語集のファイル用の [**コンテナーを作成**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)する必要があります。

* **ソースのコンテナー**。 このコンテナーは、翻訳対象のファイルをアップロードする場所です (必須)。
* **ターゲットのコンテナー**。 このコンテナーは、翻訳されたファイルを格納する場所です (必須)。  
* **用語集のコンテナー**。 このコンテナーは、用語集ファイルをアップロードする場所です (省略可能)。  

### <a name="create-sas-access-tokens-for-document-translation"></a>**ドキュメント翻訳用の SAS アクセス トークンを作成する**

`sourceUrl`、`targetUrl`、省略可能な `glossaryUrl` には Shared Access Signature (SAS) トークンを含める必要があり、クエリ文字列として追加します。 トークンは、コンテナーまたは特定の BLOB に割り当てることができます。 「[**ドキュメント翻訳プロセスの SAS トークンを作成する**](create-sas-tokens.md)」を "*参照してください*"。

* **ソース** のコンテナーまたは BLOB には、**読み取り** と **一覧表示** のアクセス権が指定されている必要があります。
* **ターゲット** のコンテナーまたは BLOB には、**書き込み** と **一覧表示** のアクセス権が指定されている必要があります。
* **用語集** のコンテナーまたは BLOB には、**読み取り** と **一覧表示** のアクセス権が指定されている必要があります。

> [!TIP]
>
> * 操作で **複数** のファイル (BLOB) を翻訳する場合は、**コンテナー レベルで SAS アクセス権を委任** します。  
> * 操作で **1 つ** のファイル (BLOB) を翻訳する場合は、**BLOB レベルで SAS アクセス権を委任** します。  
>

## <a name="set-up-your-coding-platform"></a>コーディング プラットフォームを設定する

### <a name="c"></a>[C#](#tab/csharp)

* 新しいプロジェクトを作成します。
* Program.cs を以下の C# コードで置き換えます。
* エンドポイント、 サブスクリプション キー、コンテナー URL の値を Program.cs で設定します。
* JSON データを処理するには、[.NET CLI を使用して Newtonsoft.Json パッケージ](https://www.nuget.org/packages/Newtonsoft.Json/)を追加します。
* プロジェクト ディレクトリからプログラムを実行します。

### <a name="nodejs"></a>"[Node.js](#tab/javascript)"

* 新しい Node.js プロジェクトを作成します。
* `npm i axios` を使用して Axios ライブラリをインストールします。
* 次のコードをコピーして、プロジェクトに貼り付けます。
* エンドポイント、サブスクリプション キー、コンテナー URL の値を設定します。
* プログラムを実行します。

### <a name="python"></a>[Python](#tab/python)  

* 新しいプロジェクトを作成します。
* サンプルのいずれかからコードをコピーし、プロジェクトに貼り付けます。
* エンドポイント、サブスクリプション キー、コンテナー URL の値を設定します。
* プログラムを実行します。 (例: `python translate.py`)。

### <a name="java"></a>[Java](#tab/java)

* 自分のプロジェクト用に作業ディレクトリを作成します 次に例を示します。

```powershell
mkdir sample-project
```

* プロジェクト ディレクトリで、次のサブディレクトリ構造を作成します。  

  src</br>
&emsp; └ main</br>
&emsp;&emsp;&emsp;└ java

```powershell
mkdir -p src/main/java/
```

**注**: Java ソース ファイル (たとえば、_sample.java_) は src/main/**java** にあります。

* ルート ディレクトリ (たとえば、*sample-project*) で、Gradle を使用してプロジェクトを初期化します。

```powershell
gradle init --type basic
```

* **DSL** を選択するよう求められたら、**Kotlin** を選択します。

* `build.gradle.kts` ファイルを更新します。 サンプルに応じて `mainClassName` を更新する必要があることに注意してください。

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* **java** ディレクトリで Java ファイルを作成し、提供されたサンプルからコードをコピー/貼り付けします。 忘れずに自分のサブスクリプション キーとエンドポイントを追加してください。

* **ルート ディレクトリからサンプルをビルドして実行します**。

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)  

* 新しい Go プロジェクトを作成します。
* 次に示すコードを追加します。
* エンドポイント、サブスクリプション キー、コンテナー URL の値を設定します。
* "go" という拡張子でファイルを保存します。
* Go がインストールされているコンピューターのコマンド プロンプトを開きます。
* ファイルをビルドします (例: "go build example-code.go")。
* ファイルを実行します (例: "example-code")。

 ---

## <a name="make-document-translation-requests"></a>ドキュメント翻訳要求を行う

バッチのドキュメント翻訳要求は、POST 要求を通じて Translator サービス エンドポイントに送信されます。 成功した場合、POST メソッドから `202 Accepted` 応答コードが返され、バッチ要求が作成されます。

### <a name="http-headers"></a>HTTP ヘッダー

各 Document Translator API 要求には、次のヘッダーが含まれています。

|HTTP ヘッダー|説明|
|---|--|
|Ocp-Apim-Subscription-Key|**必須**: 値は、Translator または Cognitive Services リソースの Azure サブスクリプション キーです。|
|Content-Type|**必須**: ペイロードのコンテンツ タイプを指定します。 許容される値は、application/json または charset=UTF-8 です。|
|Content-Length|**必須**: 要求本文の長さです。|

### <a name="post-request-body-properties"></a>POST 要求本文のプロパティ

* POST 要求本文は、`inputs` という名前の JSON オブジェクトです。
* `inputs` オブジェクトには、ソースとターゲットの言語ペア用の `sourceURL` と `targetURL` のコンテナー アドレスが両方とも含まれていて、必要に応じて `glossaryURL` のコンテナー アドレスを含めることができます。
* `prefix` と `suffix` のフィールド (省略可能) は、フォルダーを格納しているコンテナー内のドキュメントをフィルター処理するために使用されます。
* `glossaries` フィールド (省略可能) の値は、ドキュメントが翻訳されるときに適用されます。
* 各ターゲット言語の `targetUrl` は一意でなければなりません。

>[!NOTE]
> 同じ名前のファイルが宛先に既に存在する場合は、上書きされます。

## <a name="post-a-translation-request"></a>翻訳要求を POST する

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>省略可能な glossaryURL を使用せずに要求本文を POST する

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>省略可能な glossaryURL を使用して要求本文を POST する

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

> [!IMPORTANT]
>
> 下のコード サンプルでは、示されている場所でキーとエンドポイントをハードコーディングします。作業終了後は、コードからキーを削除し、公開しないように注意してください。  資格情報を安全に格納してアクセスする方法については、「[Azure Cognitive Services のセキュリティ](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp)」を参照してください。
>
> 操作によっては、次のフィールドの更新が必要になる場合があります。
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id` (ジョブ ID)
>>

#### <a name="locating--the-id-value"></a>`id` 値の検索

* ジョブ `id` は、POST メソッドの応答ヘッダー `Operation-Location` の URL 値で確認します。 この URL の最後のパラメーターが操作のジョブ **`id`** です。

|**応答ヘッダー**|**結果の URL**|
|-----------------------|----------------|
Operation-Location   | https://<<span>NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/9dce0aa9-78dc-41ba-8cae-2e2f3c2ff8ec</span>

* **GET Jobs** 要求を使用して、ドキュメント翻訳ジョブ `id` を取得することもできます。

>

## <a name="_post-document-translation_-request"></a>_ドキュメント翻訳要求を POST する_

バッチのドキュメント翻訳要求を翻訳サービスに送信します。

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_"></a>_ファイル形式を取得する_ 

サポートされているファイル形式の一覧を取得します。 成功した場合、このメソッドから `200 OK` 応答コードが返されます。

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_"></a>_ジョブの状態を取得する_ 

1 つのジョブの現在の状態と、ドキュメント翻訳要求に含まれるすべてのジョブの概要を取得します。 成功した場合、このメソッドから `200 OK` 応答コードが返されます。
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_"></a>_ドキュメントの状態を取得する_

### <a name="brief-overview"></a>概要

ドキュメント翻訳要求内の特定のドキュメントの状態を取得します。 成功した場合、このメソッドから `200 OK` 応答コードが返されます。

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_"></a>_ジョブを削除する_ 

### <a name="brief-overview"></a>概要

現在処理中またはキューに入っているジョブをキャンセルします。 翻訳が開始されていないドキュメントだけがキャンセルされます。

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>コンテンツの制限

次の表に、ドキュメント翻訳に送信するデータの制限を示します。

|属性 | 制限|
|---|---|
|ドキュメント サイズ| ≤ 40 MB |
|ファイルの総数。|≤ 1000 |
|バッチ内のコンテンツ サイズの合計 | ≤ 250 MB|
|バッチ内のターゲット言語の数| ≤ 10 |
|翻訳メモリ ファイルのサイズ| ≤ 10 MB|

> [!NOTE]
> 上記のコンテンツ制限は、パブリック リリースの前に変更される可能性があります。

## <a name="learn-more"></a>詳細情報

* [Translator v3 API リファレンス](../reference/v3-0-reference.md)
* [言語サポート](../language-support.md)
* [Azure API Management のサブスクリプション](../../../api-management/api-management-subscriptions.md)。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム翻訳ツールを使用してカスタマイズされた言語システムを作成する](../custom-translator/overview.md)
>
>
