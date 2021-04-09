---
title: Azure API Management のクロス ドメイン ポリシー | Microsoft Docs
description: Azure API Management で使用できるクロス ドメイン ポリシーについて説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2021
ms.author: apimpm
ms.openlocfilehash: 85abf30d792b24b92685e191f5b460a42dc29142
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688418"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (API Management のクロス ドメイン ポリシー)
このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](./api-management-policies.md)」をご覧ください。

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> クロス ドメイン ポリシー

- [クロスドメイン呼び出しを許可](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Adobe Flash や Microsoft Silverlight ブラウザーベースのクライアントから API を利用できるようにします。
- [CORS](api-management-cross-domain-policies.md#CORS) - クロス オリジン リソース共有 (CORS) のサポートを操作または API に追加して、ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。
- [JSONP](api-management-cross-domain-policies.md#JSONP) - JSON with padding (JSONP) のサポートを操作または API に追加して、JavaScript ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> クロスドメイン呼び出しを許可
`cross-domain`ポリシーを使用して、Adobe Flash や Microsoft Silverlight ブラウザーベースのクライアントから API を利用できるようにします。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>例

```xml
<cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
</cross-domain>
```

### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|cross-domain|ルート要素。 子要素は、[Adobe cross-domain ポリシー ファイルの仕様](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)に従う必要があります。|はい|

### <a name="usage"></a>使用法
このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound
- **ポリシー スコープ:** すべてのスコープ

## <a name="cors"></a><a name="CORS"></a> CORS
`cors`ポリシーは、クロス オリジン リソース共有 (CORS) のサポートを操作または API に追加して、ブラウザーベースのクライアントからのドメイン間呼び出しを可能にします。 

> [!NOTE]
> 要求が、API で定義されている OPTIONS メソッドでの操作と一致する場合、CORS ポリシーに関連付けられている事前要求処理ロジックは実行されません。 したがって、そのような操作は、カスタムの事前処理ロジックを実装するために使用できます。

CORS を使用すると、ブラウザーとサーバーは、やり取りを介して、特定のクロス オリジン要求 (たとえば、他のドメインに対して Web ページの JavaScript で実行された XMLHttpRequests 呼び出し) を許可するかどうかを決めることができます。 これにより、単に同一オリジンの要求を許可するよりも高い柔軟性が得られる一方、すべてのクロス オリジン要求を許可するよりも高いセキュリティを実現できます。

開発者ポータルで対話型コンソールを有効にするには、CORS ポリシーを適用する必要があります。 詳細については、[開発者ポータルのドキュメント](./api-management-howto-developer-portal.md#cors)を参照してください。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<cors allow-credentials="false|true" terminate-unmatched-request="true|false">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>例
この例では、カスタム ヘッダーまたは GET や POST 以外のメソッドを含む事前要求に対応する方法を示します。 カスタム ヘッダーやその他の HTTP 動詞をサポートするには、次の例に示すように `allowed-methods` および `allowed-headers` セクションを使用します。

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>要素

|名前|説明|必須|Default|
|----------|-----------------|--------------|-------------|
|cors|ルート要素。|はい|該当なし|
|allowed-origins|クロス ドメイン要求で許可される配信元を示す `origin` 要素を含みます。 `allowed-origins` に含めることができるのは、すべての配信元を許可する `*`を含む 1 つの `origin` 要素か、URI を含む 1 つ以上の `origin` 要素です。|はい|該当なし|
|origin|値として使用できるのは、すべての配信元を許可する `*` か、1 つの配信元を指定する URI です。 URI には、スキーム、ホスト、およびポートを含める必要があります。|はい|URI でポートが省略されると、HTTP ではポート 80、HTTPS ではポート 443 が使用されます。|
|allowed-methods|この要素は、GET または POST 以外のメソッドが許可される場合に必須です。 サポートされる HTTP 動詞を指定する `method` 要素が含まれます。 `*` 値は、すべてのメソッドを示します。|いいえ|このセクションが存在しない場合、GET と POST がサポートされます。|
|method|HTTP 動詞を指定します。|`allowed-methods` セクションが存在する場合、少なくとも 1 つの `method` 要素が必要です。|該当なし|
|allowed-headers|この要素には、要求に組み込むことができるヘッダーの名前を指定する `header` 要素が含まれます。|いいえ|該当なし|
|expose-headers|この要素には、クライアントがアクセスできるヘッダーの名前を指定する `header` 要素が含まれます。|いいえ|該当なし|
|header|ヘッダー名を指定します。|セクションが存在する場合、少なくとも 1 つの `header` 要素が `allowed-headers` または `expose-headers` に必要です。|該当なし|

### <a name="attributes"></a>属性

|名前|説明|必須|Default|
|----------|-----------------|--------------|-------------|
|allow-credentials|事前応答内の `Access-Control-Allow-Credentials` ヘッダーが、この属性の値に設定されます。これは、クライアントがクロス ドメイン要求で資格情報を送信できるかどうかに影響します。|いいえ|false|
|terminate-unmatched-request|この属性では、CORS ポリシー設定と一致しないクロスオリジン要求の処理を制御します。 OPTIONS 要求が事前要求として処理され、CORS ポリシー設定と一致しないとき: 属性が `true` に設定されている場合は、空の 200 OK 応答で要求を直ちに終了します。属性が `false` に設定されている場合は、inbound 要素の直接の子である他のスコープ内 CORS ポリシーがないか inbound を調べて、それらを適用します。  CORS ポリシーが見つからなければ、空の 200 OK 応答で要求を終了します。 GET または HEAD 要求に Origin ヘッダーが含まれていて (したがって、クロスオリジン要求として処理される)、CORS ポリシー設定と一致しないとき: 属性が `true` に設定されている場合は、空の 200 OK 応答で要求を直ちに終了します。属性が `false` に設定されている場合、要求の通常どおりの続行が許可され、応答に CORS ヘッダーは追加されません。|いいえ|true|
|preflight-result-max-age|事前応答内の `Access-Control-Max-Age` ヘッダーが、この属性の値に設定されます。これは、ユーザー エージェントが事前応答をキャッシュできるかどうかに影響します。|いいえ|0|

### <a name="usage"></a>使用法
このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound
- **ポリシー スコープ:** すべてのスコープ

## <a name="jsonp"></a><a name="JSONP"></a> JSONP
`jsonp`ポリシーは、JSON with padding (JSONP) のサポートを操作または API に追加して、JavaScript ブラウザーベースのクライアントからのクロス ドメイン呼び出しを可能にします。 JSONP は、異なるドメインのサーバーのデータを要求するために JavaScript プログラムで使用される方法です。 JSONP を使用すると、Web ページへのアクセスが同じドメイン内である必要があるという、ほとんどの Web ブラウザーで適用される制限を回避できます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>例

```xml
<jsonp callback-parameter-name="cb" />
```

コールバック パラメーター ?cb=XXX なしでメソッドを呼び出した場合、(関数呼び出しラッパーのない) プレーン JSON が返されます。

コールバック パラメーター `?cb=XXX` を追加した場合、元の JSON 結果をコールバック関数でラップして結果が返されます (例: `XYZ('<json result goes here>');`)。

### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|jsonp|ルート要素。|はい|

### <a name="attributes"></a>属性

|名前|説明|必須|Default|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|関数が含まれる完全修飾ドメイン名をプレフィックスとするクロスドメイン JavaScript 関数呼び出し。|はい|なし|

### <a name="usage"></a>使用法
このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** outbound
- **ポリシー スコープ:** すべてのスコープ

## <a name="next-steps"></a>次のステップ

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
+ [ポリシーのサンプル](./policy-reference.md)
