---
title: 仮想マシン上でマネージド ID を使用してアクセス トークンを取得する - Azure AD
description: 仮想マシン上で Azure リソースのマネージド ID を使用して OAuth アクセス トークンを取得する手順と例について説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541f76ad825f492679530902c571096ca4b01902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726233"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Azure VM 上で Azure リソースのマネージド ID を使用してアクセス トークンを取得する方法 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、トークンの取得に使用する各種コードとスクリプトの例を提供するほか、トークンの有効期限や HTTP エラーの処理などの重要なトピックに関するガイダンスも提供します。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

この記事の Azure PowerShell の例を使用する場合は、[Azure PowerShell](/powershell/azure/install-az-ps) の最新バージョンをインストールする必要があります。


> [!IMPORTANT]
> - この記事のすべてのサンプル コード/スクリプトは、Azure リソースのマネージド ID を使用する仮想マシン上でクライアントが実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure portal で仮想マシンへの "接続" 機能を使用します。 VM で Azure リソースのマネージド ID を有効にする方法の詳細については、「[Azure Portal を使用して VM 上に Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」、または関連する記事 (PowerShell、CLI、テンプレート、または Azure SDK の使用) のいずれかを参照してください。 

> [!IMPORTANT]
> - Azure リソースのマネージド ID のセキュリティ境界は、使用されているリソースです。 仮想マシン上で実行されるすべてのコード/スクリプトは、そこで使用できる任意のマネージド ID のトークンを要求して取得できます。 

## <a name="overview"></a>概要

クライアント アプリケーションは、特定のリソースにアクセスするために、Azure リソースの[アプリ専用アクセス トークン](../develop/developer-glossary.md#access-token)に対してマネージド ID を要求できます。 トークンは、[Azure リソース サービス プリンシパルのマネージド ID に基づいています](overview.md#managed-identity-types)。 そのため、独自のサービス プリンシパルでアクセス トークンを取得するために、クライアントそのものを登録する必要がありません。 トークンは、[クライアント資格情報を必要とするサービス間の呼び出し](../develop/v2-oauth2-client-creds-grant-flow.md)のベアラー トークンとしての使用に適しています。

| Link | 説明 |
| -------------- | -------------------- |
| [HTTP を使用してトークンを取得する](#get-a-token-using-http) | Azure リソース トークン エンドポイントのマネージド ID に関するプロトコルの詳細 |
| [.NET 用の Microsoft.Azure.Services.AppAuthentication ライブラリを使用してトークンを取得する](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | .NET クライアントからの Microsoft.Azure.Services.AppAuthentication ライブラリの使用例
| [C# を使用してトークンを取得する](#get-a-token-using-c) | C# クライアントから Azure リソース REST エンドポイントのマネージド ID を使用する例 |
| [Java を使用してトークンを取得する](#get-a-token-using-java) | Java クライアントから Azure リソース REST エンドポイントのマネージド ID を使用する例 |
| [Go を使用してトークンを取得する](#get-a-token-using-go) | Go クライアントから Azure リソース REST エンドポイントのマネージド ID を使用する例 |
| [Azure PowerShell を使用してトークンを取得する](#get-a-token-using-azure-powershell) | PowerShell クライアントから Azure リソース REST エンドポイントのマネージド ID を使用する例 |
| [CURL を使用してトークンを取得する](#get-a-token-using-curl) | Bash/CURL クライアントから Azure リソース REST エンドポイントのマネージド ID を使用する例 |
| トークンのキャッシュの処理 | 有効期限が切れたアクセス トークンの処理に関するガイダンス |
| [エラー処理](#error-handling) | Azure リソース トークン エンドポイントのマネージド ID から返される HTTP エラーを処理するためのガイダンス |
| [Azure サービスのリソース ID](#resource-ids-for-azure-services) | サポートされている Azure サービスのリソース ID を取得する場所 |

## <a name="get-a-token-using-http"></a>HTTP を使用してトークンを取得する 

アクセス トークンの取得に使用する基本的なインターフェイスは REST に基づいているため、HTTP REST の呼び出しを行える VM 上で実行されている、すべてのクライアント アプリケーションにアクセスできます。 これは、クライアントが仮想マシン上のエンドポイントを使用する点以外は、Azure AD のプログラミング モデルと同じです (Azure AD のプログラミング モデルでは、Azure AD エンドポイントを使用)。

Azure Instance Metadata Service (IMDS) エンドポイントを使用するサンプル要求 *(推奨)* :

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| 要素 | 説明 |
| ------- | ----------- |
| `GET` | HTTP 動詞。エンドポイントからデータを取得する必要があることを示します。 この例では、OAuth アクセス トークンです。 | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Instance Metadata Service 用の Azure リソース エンドポイントのマネージド ID。 |
| `api-version`  | クエリ文字列パラメーター。IMDS エンドポイントの API バージョンです。 API バージョン `2018-02-01` 以上を使用してください。 |
| `resource` | クエリ文字列パラメーター。ターゲット リソースのアプリ ID URI です。 発行されたトークンの `aud` (audience) 要求にも表示されます。 この例では、アプリ ID URI が `https://management.azure.com/` の Azure Resource Manager にアクセスするためのトークンを要求しています。 |
| `Metadata` | HTTP 要求ヘッダー フィールド。サーバー側のリクエスト フォージェリ (SSRF) 攻撃に対する軽減策として Azure リソースのマネージド ID に必要です。 この値は、"true" に設定し、すべて小文字にする必要があります。 |
| `object_id` | (省略可能) クエリの文字列パラメーター。トークン用の管理対象 ID の object_id を示します。 VM に複数のユーザーが割り当てたマネージド ID がある場合は必須です。|
| `client_id` | (省略可能) クエリの文字列パラメーター。トークン用の管理対象 ID の client_id を示します。 VM に複数のユーザーが割り当てたマネージド ID がある場合は必須です。|
| `mi_res_id` | (省略可能) クエリの文字列パラメーター。トークン用の管理対象 ID の mi_res_id (Azure リソース ID) を示します。 VM に複数のユーザーが割り当てたマネージド ID がある場合は必須です。 |

Azure リソース VM 拡張機能エンドポイントにマネージド ID を使用するサンプル要求 *(2019 年 1 月に非推奨となる予定)* :

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 要素 | 説明 |
| ------- | ----------- |
| `GET` | HTTP 動詞。エンドポイントからデータを取得する必要があることを示します。 この例では、OAuth アクセス トークンです。 | 
| `http://localhost:50342/oauth2/token` | Azure リソース エンドポイントのマネージド ID (50342 は既定のポートであり、構成可能です)。 |
| `resource` | クエリ文字列パラメーター。ターゲット リソースのアプリ ID URI です。 発行されたトークンの `aud` (audience) 要求にも表示されます。 この例では、アプリ ID URI が `https://management.azure.com/` の Azure Resource Manager にアクセスするためのトークンを要求しています。 |
| `Metadata` | HTTP 要求ヘッダー フィールド。サーバー側のリクエスト フォージェリ (SSRF) 攻撃に対する軽減策として Azure リソースのマネージド ID に必要です。 この値は、"true" に設定し、すべて小文字にする必要があります。|
| `object_id` | (省略可能) クエリの文字列パラメーター。トークン用の管理対象 ID の object_id を示します。 VM に複数のユーザーが割り当てたマネージド ID がある場合は必須です。|
| `client_id` | (省略可能) クエリの文字列パラメーター。トークン用の管理対象 ID の client_id を示します。 VM に複数のユーザーが割り当てたマネージド ID がある場合は必須です。|

応答のサンプル:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 要素 | 説明 |
| ------- | ----------- |
| `access_token` | 要求されたアクセス トークン。 セキュリティで保護された REST API を呼び出すとき、トークンは `Authorization` 要求ヘッダー フィールドに "ベアラー" トークンとして埋め込まれ、API が呼び出し元を認証できるようにします。 | 
| `refresh_token` | Azure リソースのマネージド ID には使用されません。 |
| `expires_in` | アクセス トークンが発行されてから期限切れになるまでの有効継続時間 (秒単位)。 発行時刻はトークンの `iat` 要求で確認できます。 |
| `expires_on` | アクセス トークンが期限切れになるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `exp` 要求に対応)。 |
| `not_before` | アクセス トークンが有効になり、承認されるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `nbf` 要求に対応)。 |
| `resource` | アクセス トークンの要求対象リソース。要求の `resource` クエリ文字列パラメーターと一致します。 |
| `token_type` | トークンの種類。つまり "ベアラー" アクセス トークン。リソースが、このトークンのベアラーへのアクセスを提供できることを意味します。 |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>.NET 用の Microsoft.Azure.Services.AppAuthentication ライブラリを使用してトークンを取得する

.NET アプリケーションと Functions の場合、Azure リソースのマネージド ID を使用する最も簡単な方法は、Microsoft.Azure.Services.AppAuthentication パッケージを利用することです。 このライブラリを使うと、Visual Studio、[Azure CLI](/cli/azure)、または Active Directory 統合認証のユーザー アカウントを使って、開発用コンピューターでローカルにコードをテストすることもできます。 このライブラリでのローカル開発オプションについて詳しくは、[Microsoft.Azure.Services.AppAuthentication のリファレンス](/dotnet/api/overview/azure/service-to-service-authentication)に関するページをご覧ください。 このセクションでは、コードでライブラリを使い始める方法を示します。

1. [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) および [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet パッケージに対する参照をアプリケーションに追加します。

2.  次のコードをアプリケーションに追加します。

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Microsoft.Azure.Services.AppAuthentication およびそれによって公開される操作の詳細については、[Microsoft.Azure.Services.AppAuthentication リファレンス](/dotnet/api/overview/azure/service-to-service-authentication)と [Azure リソースのマネージド ID を使用する App Service および KeyVault の .NET サンプル](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)を参照してください。

## <a name="get-a-token-using-c"></a>C# を使用してトークンを取得する

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Java を使用してトークンを取得する

Java を使用してトークンを取得するには、この [JSON ライブラリ](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4)を使用します。

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Go を使用してトークンを取得する

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Azure PowerShell を使用してトークンを取得する

次の例では、PowerShell クライアントから Azure リソース REST エンドポイントのマネージド ID を使用して、以下を実行する方法を示します。

1. アクセス トークンを取得します。
2. アクセス トークンを使用して Azure Resource Manager の REST API を呼び出し、VM に関する情報を取得します。 `<SUBSCRIPTION-ID>`、`<RESOURCE-GROUP>`、および `<VM-NAME>` の代わりに、ご自分のサブスクリプション ID、リソース グループ名、および仮想マシン名をそれぞれ使用する必要があります。

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

応答からアクセス トークンを解析する方法の例を次に示します。
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>CURL を使用してトークンを取得する

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


応答からアクセス トークンを解析する方法の例を次に示します。

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>トークンのキャッシュ

Azure リソース サブシステムのマネージド ID (Azure リソース VM 拡張機能の IMDS/マネージド ID) はキャッシュ トークンを実行しますが、コードにトークン キャッシングを実装することもお勧めします。 そのため、リソースがトークンの有効期限切れを示すシナリオに備える必要があります。 

ネットワークを介した Azure AD の呼び出しは、次の場合にのみ行われます。
- Azure リソース サブシステム キャッシュのマネージド ID にトークンがないためキャッシュ ミスが発生する
- キャッシュのトークンの有効期限が切れている

## <a name="error-handling"></a>エラー処理

Azure リソース エンドポイントのマネージド ID は、HTTP 応答メッセージのヘッダーに含まれる状態コード フィールドを通じて、4xx エラーまたは 5xx エラーのいずれかとして、エラーを通知します。

| 状態コード | エラーの理由 | 処理方法 |
| ----------- | ------------ | ------------- |
| 404 見つかりません。 | IMDS エンドポイントが更新されています。 | 指数バックオフを使用して再試行してください。 以下のガイダンスを参照してください。 |
| 429 要求が多すぎます。 |  IMDS スロットルの上限に達しました。 | 指数バックオフを使用して再試行してください。 以下のガイダンスを参照してください。 |
| 要求の 4xx エラーです。 | 1 つ以上の要求パラメーターが正しくありませんでした。 | 再試行はしないでください。  詳しくは、エラーの詳細を確認します。  4xx エラーは、デザイン時のエラーです。|
| サービスからの 5xx の一時的なエラーです。 | Azure リソース サブシステムまたは Azure Active Directory のマネージド ID から一時的なエラーが返されました。 | 少なくとも 1 秒間待機した後に、安全に再試行できます。  再試行が早すぎる場合や再試行の回数が多すぎる場合は、IMDS および Azure AD からレート制限エラー (429) が返されることがあります。|
| timeout | IMDS エンドポイントが更新されています。 | 指数バックオフを使用して再試行してください。 以下のガイダンスを参照してください。 |

エラーが発生すると、対応する HTTP 応答本文に、JSON とエラーの詳細が含まれます。

| 要素 | 説明 |
| ------- | ----------- |
| error   | エラー識別子。 |
| error_description | エラーの詳細な説明。 **エラーの説明は、予告なく変更になる場合があります。エラーの説明に含まれる値に基づいて分岐するコードを作成しないでください。**|

### <a name="http-response-reference"></a>HTTP 応答リファレンス

このセクションでは、想定されるエラー応答について説明します。 "200 OK" の状態は成功応答であり、access_token 要素内の応答本文の JSON にアクセス トークンが含まれています。

| status code | エラー | エラーの説明 | 解決策 |
| ----------- | ----- | ----------------- | -------- |
| 400 Bad Request | invalid_resource | AADSTS50001: *\<URI\>* という名前のアプリケーションが *\<TENANT-ID\>* という名前のテナントに見つかりませんでした。 このエラーは、アプリケーションがテナントの管理者によってインストールされていない場合や、アプリケーションがテナント内のいずれのユーザーによっても同意されていない場合に発生することがあります。 間違ったテナントに認証要求を送信した可能性があります。\ | (Linux のみ) |
| 400 Bad Request | bad_request_102 | 必要なメタデータ ヘッダーが指定されていません | 要求で `Metadata` 要求ヘッダー フィールドが見つからないか、形式が正しくありません。 値は `true` として指定し、すべて小文字にする必要があります。 例については、上記の「REST」セクションの「要求のサンプル」を参照してください。|
| 401 権限がありません | unknown_source | 不明なソース *\<URI\>* | HTTP GET 要求の URI の形式が正しいことを確認します。 `scheme:host/resource-path` 部分は、`http://localhost:50342/oauth2/token` として指定する必要があります。 例については、上記の「REST」セクションの「要求のサンプル」を参照してください。|
|           | invalid_request | 要求に必要なパラメーターが含まれていないか、要求に無効なパラメーター値が含まれているか、要求に複数回パラメーターが含まれているか、要求の形式が正しくないかのいずれかです。 |  |
|           | unauthorized_client | クライアントには、このメソッドを使用してアクセス トークンを要求する権限がありません。 | 拡張機能の呼び出しにローカル ループバックを使用しなかった要求や、Azure リソースのマネージド ID が正しく構成されていない VM が原因です。 VM の構成についてサポートが必要な場合は、「[Azure portal を使用して VM 上に Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」を参照してください。 |
|           | access_denied | リソース所有者または承認サーバーによって、要求が拒否されました。 |  |
|           | unsupported_response_type | このメソッドを使用したアクセス トークンの取得は、承認サーバーによってサポートされていません。 |  |
|           | invalid_scope | 要求されたスコープが無効、不明、または形式が正しくありません。 |  |
| 500 内部サーバー エラー | unknown | Active Directory からのトークンの取得に失敗しました。 詳細については、 *\<file path\>* のログを参照してください | Azure リソースのマネージド ID が VM 上で有効なことを確認します。 VM の構成についてサポートが必要な場合は、「[Azure portal を使用して VM 上に Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」を参照してください。<br><br>また、HTTP GET 要求の URI、特にクエリ文字列で指定されたリソース URI の形式が正しいかどうかを確認します。 例については、上記の「REST」セクションの「要求のサンプル」を参照してください。または、「[Azure AD 認証をサポートしている Azure サービス](./services-support-managed-identities.md)」で、サービスの一覧と、そのリソース ID を参照してください。

## <a name="retry-guidance"></a>再試行のガイダンス 

404、429、または 5xx のエラー コードが表示される場合、再試行することをお勧めします (前の 「[エラー処理](#error-handling)」を参照)。

スロットル制限は、IMDS エンドポイントに対して行われる呼び出し回数に適用されます。 スロットルのしきい値を超えた場合、IMDS エンドポイントは、スロットルが有効な状態にあっても、それ以降の要求を制限します。 この期間中、IMDS エンドポイントは HTTP 状態コード 429 ("要求が多すぎます") を返し、要求は失敗します。 

再試行については、次の方法をお勧めします。 

| **再試行戦略** | **設定** | **値** | **動作のしくみ** |
| --- | --- | --- | --- |
|ExponentialBackoff |再試行回数<br />最小バックオフ<br />最大バックオフ<br />差分バックオフ<br />最初の高速再試行 |5<br />0 秒<br />60 秒<br />2 秒<br />false |試行 1 - 0 秒の遅延<br />試行 2 - 最大 2 秒の遅延<br />試行 3 - 最大 6 秒の遅延<br />試行 4 - 最大 14 秒の遅延<br />試行 5 - 最大 30 秒の遅延 |

## <a name="resource-ids-for-azure-services"></a>Azure サービスのリソース ID

Azure AD をサポートするリソースで、Azure リソースのマネージド ID をテスト済みのリソースとそれぞれのリソース ID の一覧については、「[Azure AD 認証をサポートしている Azure サービス](./services-support-managed-identities.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

- Azure VM 上で Azure リソースのマネージド ID を有効にするには、「[Configure managed identities for Azure resources on a VM using the Azure portal](qs-configure-portal-windows-vm.md)」 (Azure portal を使用して VM 上で Azure リソースのマネージド ID を構成する) を参照してください。