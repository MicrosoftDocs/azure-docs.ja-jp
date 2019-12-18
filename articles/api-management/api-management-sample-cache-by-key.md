---
title: Azure API Management のカスタム キャッシュ
description: Azure API Management のキーによって項目をキャッシュする方法を説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60780823"
---
# <a name="custom-caching-in-azure-api-management"></a>Azure API Management のカスタム キャッシュ
Azure API Management サービスは、リソース URL をキーとして使用する [HTTP 応答のキャッシュ](api-management-howto-cache.md) を標準でサポートしています。 このキーは、要求ヘッダーで `vary-by` プロパティを使用して変更できます。 この変更操作は、HTTP 応答全体 ("表現" とも呼ばれます) をキャッシュする際だけでなく、表現の一部をキャッシュする際にも役立つことがあります。 新しい [cache-lookup-value](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) ポリシーと [cache-store-value](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) ポリシーを使用すると、ポリシー定義内からデータの任意の部分の格納と取得を実行できます。 また、外部サービスからの応答をキャッシュできるようになるため、以前に導入された [send-request](/azure/api-management/api-management-advanced-policies#SendRequest) ポリシーに値を追加することもできます。

## <a name="architecture"></a>アーキテクチャ
API Management サービスでは、テナント単位の共有データ キャッシュが使用されるため、複数のユニットにスケールアップしても、同じキャッシュ データにアクセスできます。 ただし、複数リージョンのデプロイを使用する場合、キャッシュはリージョンごとに独立しています。 キャッシュを、データの唯一の格納場所となるデータ ストアとして扱わないようにすることが重要です。 データ ストアとして扱い、後で複数リージョンのデプロイを使用することにした場合、ユーザーが移動すると、キャッシュされたデータにアクセスできなくなる可能性があります。

## <a name="fragment-caching"></a>フラグメント キャッシュ
特定するのにコストがかかるため手付かずのまま長期間放置されているデータが、返される応答の中に含まれている場合があります。 例として、航空会社が飛行機の予約やフライト状況などの関連情報を提供するサービスを構築した場合を考えてみましょう。ユーザーが航空会社のポイント プログラムのメンバーである場合、ユーザーには現在の状態や累積マイレージ数に関する情報があります。 これらのユーザー関連情報が別のシステムに格納されていて、その情報をフライト状況や予約についての応答の中に含めたい場合、 フラグメント キャッシュと呼ばれるプロセスを使用して実現できます。 ユーザー関連情報を挿入する位置を示す一種のトークンを使用して、プライマリ表現を配信元サーバーから返すことができます。 

バックエンド API から返される JSON 応答は次のようになります。

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

`/userprofile/{userid}` のセカンダリ リソースは次のようになります。

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

追加する適切なユーザー情報を特定するために、API Management はエンド ユーザーを識別する必要があります。 このしくみは、実装によって異なります。 この例では、`JWT` トークンの `Subject` 要求を使用しています。 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management では、`enduserid` 値は、後で使用できるようにコンテキスト変数に格納されます。 次の手順では、前の要求でユーザー情報が既に取得されてキャッシュに格納されているかどうかを特定します。 そのために、API Management では、`cache-lookup-value` ポリシーを使用します。

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

キー値に対応するエントリがキャッシュ内にない場合、`userprofile` コンテキスト変数は作成されません。 API Management は、`choose` 制御フロー ポリシーを使用して、検索が成功したかどうかを確認します。

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

`userprofile` コンテキスト変数が存在しない場合、API Management は、それを取得するための HTTP 要求を作成する必要があります。

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management は、`enduserid` を使用して、ユーザー プロファイル リソースの URL を作成します。 API Management に応答が返されたら、API Management は、応答から本文テキストを取得し、コンテキスト変数に格納します。

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

同じユーザーが別の要求を行ったときに、API Management でこの HTTP 要求を再作成しなくてもすむように、ユーザー プロファイルはキャッシュに格納できます。

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management が値をキャッシュに格納するときに使用するのは、API Management が最初の取得で使用したキーと同じものです。 API Management が値を格納する期間は、情報が変更される頻度と、古くなった情報をユーザーがどの程度許容できるかに基づいて決定する必要があります。 

キャッシュからの取得はプロセス外のネットワーク要求であり、要求にかかる時間が数十ミリ秒増える可能性がある点に注意が必要です。 ユーザー プロファイル情報を特定する際に、データベース クエリの実行や複数のバックエンドからの情報集計が必要になるために長い時間がかかる場合は、キャッシュから取得する方がメリットがあります。

プロセスの最後の手順では、返される応答をユーザー プロファイル情報で更新します。

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

ここでは、トークンに引用符を含めることができ、置換が実行されなかった場合も、応答が引き続き有効な JSON となります。  

ここまでのすべての手順を 1 つにすると、最終的には次のようなポリシーが完成します。

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

このようなキャッシュ アプローチは、主に、1 つのページとして表示できるように HTML がサーバー側で構成されている Web サイトで使用されます。 これは、クライアントがクライアント側での HTTP キャッシュを実行できない API や、クライアントにその役割を負わせたくない場合にも役立ちます。

同種のフラグメント キャッシュは、Redis キャッシュ サーバーを使用してバックエンド Web サーバーで実行することもできます。ただし、キャッシュされたフラグメントがプライマリ応答とは別のバックエンドから取得される場合は、API Management サービスを使用して実施する方が便利です。

## <a name="transparent-versioning"></a>透過的なバージョン管理
API の複数の実装バージョンを同時にサポートすることはよくあります。 その目的は、たとえば、さまざまな環境 (開発、テスト、運用など) をサポートすることだったり、API コンシューマーが新しいバージョンに移行するための期間を設けて、以前のバージョンの API をサポートすることだったりします。 

クライアントの開発者が URL を `/v1/customers` から `/v2/customers` に変更せずに、この状況に対処する方法の 1 つとして使用したい API バージョンをコンシューマーのプロファイル データに格納し、適切なバックエンド URL を呼び出すというアプローチがあります。 特定のクライアントを呼び出すための適切なバックエンド URL を調べるには、いくつかの構成データに対してクエリを実行する必要があります。 この構成データをキャッシュすることで、API Management は、検索を実行する際のパフォーマンスの低下を最小限に抑えることができます。

最初の手順では、目的のバージョンを構成するために使用される識別子を特定します。 この例では、バージョンを製品のサブスクリプション キーに関連付けることにしました。 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management は、キャッシュの検索を実行し、目的のクライアント バージンが既に取得されているかどうかを確認します。

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

次に、API Management は、それがキャッシュで見つからなかったかどうかを確認します。

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

見つからなかった場合、API Management はそれを取得します。

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

応答から応答本文テキストを抽出します。

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

将来使用できるようにキャッシュに格納します。

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

最後に、クライアントが必要とするサービスのバージョンを選択してバックエンド URL を更新します。

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

完成したポリシーは次のようになります。

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

クライアントを更新して再デプロイしなくても、クライアントがアクセスしているバックエンドのバージョンを API コンシューマーが透過的に制御できれば、さまざまな API バージョン管理の問題に効果的に対処できます。

## <a name="tenant-isolation"></a>テナントの分離
大規模なマルチテナント デプロイの場合、企業によっては、バックエンド ハードウェアのデプロイごとにテナントのグループを個別に作成します。 こうすることで、バックエンドでハードウェアの問題が発生した場合に、影響を受ける顧客の数を最小限に抑えられます。 また、新しいソフトウェア バージョンを段階的にロールアウトすることもできます。 理想としては、このバックエンド アーキテクチャは、API コンシューマーに対して透過的である必要があります。 これは、透過的なバージョン管理で説明したのと同様の方法で実現できます。API キーごとに構成状態を使用してバックエンド URL を操作するという同じ手法がベースになるためです。  

サブスクリプション キーごとに API の推奨バージョンを返すのではなく、割り当てられたハードウェア グループにテナントを関連付ける識別子を返します。 この識別子を使用して、適切なバックエンド URL を作成できます。

## <a name="summary"></a>まとめ
Azure API Management のキャッシュを使用すると、あらゆる種類のデータを格納できるため、受信要求の処理方法に影響を与える可能性のある構成データに効率よくアクセスできます。 また、データ フラグメントを格納し、バックエンド API から返された応答を補強することもできます。
