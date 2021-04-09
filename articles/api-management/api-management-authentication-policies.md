---
title: Azure API Management の認証ポリシー | Microsoft Docs
description: Azure API Management で使用できる認証ポリシーについて説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492605"
---
# <a name="api-management-authentication-policies"></a>API Management の認証ポリシー
このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](./api-management-policies.md)」をご覧ください。

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> 認証ポリシー

-   [基本認証](api-management-authentication-policies.md#Basic) -基本認証を使用してバックエンド サービスで認証します。

-   [クライアント証明書による認証](api-management-authentication-policies.md#ClientCertificate) -クライアント証明書を使用してバックエンド サービスで認証します。

-   [マネージド ID による認証](api-management-authentication-policies.md#ManagedIdentity) - API Management サービスに対する[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) による認証

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> 基本認証
 `authentication-basic` ポリシーを使用し、基本認証を使用してバックエンド サービスで認証します。 このポリシーでは、HTTP 承認ヘッダーが、ポリシーに指定された資格情報に対応する値に効率よく設定されます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>例

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|authentication-basic|ルート要素。|はい|

### <a name="attributes"></a>属性

|名前|説明|必須|Default|
|----------|-----------------|--------------|-------------|
|username|基本認証の資格情報のユーザー名を指定します。|はい|該当なし|
|password|基本認証の資格情報のパスワードを指定します。|はい|該当なし|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> クライアント証明書による認証
 `authentication-certificate` ポリシーを使用し、クライアント証明書を使用してバックエンド サービスで認証します。 証明書は最初に [API Management にインストール](./api-management-howto-mutual-certificates.md)する必要があります。サムプリントまたは証明書 ID (リソース名) によって識別されます。 

> [!CAUTION]
> 証明書が Azure Key Vault に格納されている証明書を参照している場合は、証明書 ID を使用して識別します。 キー コンテナー証明書がローテーションされると、API Management のサムプリントが変更され、サムプリントによって識別された場合、新しい証明書はポリシーによって解決されません。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>例

この例では、クライアント証明書は証明書 ID によって識別されます。

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

この例では、クライアント証明書はサムプリントによって識別されます。

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
この例では、クライアント証明書は、組み込みの証明書ストアから取得されるのではなく、ポリシーで設定されています。

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>要素  
  
|名前|説明|必須|  
|----------|-----------------|--------------|  
|authentication-certificate|ルート要素。|はい|  
  
### <a name="attributes"></a>属性  
  
|名前|説明|必須|Default|  
|----------|-----------------|--------------|-------------|  
|thumbprint|クライアント証明書のサムプリント。|`thumbprint` または `certificate-id` のいずれかが存在しなければなりません。|該当なし|
|証明書 ID|証明書リソースの名前。|`thumbprint` または `certificate-id` のいずれかが存在しなければなりません。|該当なし|
|body|バイト配列としてのクライアント証明書。|いいえ|該当なし|
|password|クライアント証明書のパスワード。|`body` で指定された証明書がパスワードで保護されている場合に使用されます。|該当なし|
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
  
-   **ポリシー スコープ:** すべてのスコープ  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> マネージド ID による認証  
 `authentication-managed-identity` ポリシーを使用して、マネージド ID を利用するバックエンド サービスで認証します。 このポリシーでは、指定されたリソースにアクセスするためのアクセス トークンを Azure Active Directory から取得するために、基本的にマネージド ID を利用します。 トークンが正常に取得されると、ポリシーは、`Bearer` スキームを使用してトークンの値を `Authorization` ヘッダーに設定します。

システムによって割り当てられた ID と、ユーザーが割り当てた複数の ID のいずれかの、どちらを使用してもトークンを要求できます。 `client-id` が指定されていない場合、システムによって割り当てられた ID が指定されたと見なされます。 `client-id` 変数が指定されている場合、そのユーザー割り当て ID のトークンが Azure Active Directory に要求されます
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>例  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>マネージド ID を使用してバックエンド サービスで認証する
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>マネージド ID を使用し、ヘッダーを手動で設定する

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>send-request ポリシーでマネージド ID を使用する
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>要素  
  
|名前|説明|必須|  
|----------|-----------------|--------------|  
|authentication-managed-identity |ルート要素。|はい|  
  
### <a name="attributes"></a>属性  
  
|名前|説明|必須|Default|  
|----------|-----------------|--------------|-------------|  
|resource|文字列 をオンにします。 Azure Active Directory におけるターゲット Web API のアプリ ID (セキュリティで保護されたリソース)。|はい|該当なし|
|client-id|文字列 をオンにします。 Azure Active Directory のユーザー割り当て ID のアプリ ID。|いいえ|システム割り当て ID|
|output-token-variable-name|文字列 をオンにします。 オブジェクトの種類 `string` としてトークン値を受け取るコンテキスト変数の名前。 |いいえ|該当なし|  
|ignore-error|Boolean です。 `true` に設定された場合、アクセス トークンが取得されなかったとしても、ポリシー パイプラインは引き続き実行されます。|いいえ|false|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
  
-   **ポリシー スコープ:** すべてのスコープ

## <a name="next-steps"></a>次のステップ
ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
+ [ポリシーのサンプル](./policy-reference.md)