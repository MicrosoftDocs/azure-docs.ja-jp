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
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 5ca9bd4964cf190eaa2be6d66d57c7ada971d675
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442399"
---
# <a name="api-management-authentication-policies"></a>API Management の認証ポリシー
このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](https://go.microsoft.com/fwlink/?LinkID=398186)」をご覧ください。

##  <a name="AuthenticationPolicies"></a> 認証ポリシー

-   [基本認証](api-management-authentication-policies.md#Basic) -基本認証を使用してバックエンド サービスで認証します。

-   [クライアント証明書による認証](api-management-authentication-policies.md#ClientCertificate) -クライアント証明書を使用してバックエンド サービスで認証します。

-   [マネージド ID による認証](api-management-authentication-policies.md#ManagedIdentity) - API Management サービスに対する[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) による認証

##  <a name="Basic"></a> 基本認証
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

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|authentication-basic|ルート要素。|はい|

### <a name="attributes"></a>属性

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|userName|基本認証の資格情報のユーザー名を指定します。|はい|該当なし|
|password|基本認証の資格情報のパスワードを指定します。|はい|該当なし|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="ClientCertificate"></a> クライアント証明書による認証
 `authentication-certificate` ポリシーを使用し、クライアント証明書を使用してバックエンド サービスで認証します。 証明書は最初に [API Management にインストール](https://go.microsoft.com/fwlink/?LinkID=511599)する必要があります。サムプリントによって識別されます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>例

この例では、クライアントの証明書は拇印によって識別されます。
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
この例では、クライアントの証明書はリソース名によって識別されます。
```xml
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|authentication-certificate|ルート要素。|はい|

### <a name="attributes"></a>属性

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|thumbprint|クライアント証明書のサムプリント。|`thumbprint` または `certificate-id` のいずれかが存在しなければなりません。|該当なし|
|証明書 ID|証明書リソースの名前。|`thumbprint` または `certificate-id` のいずれかが存在しなければなりません。|該当なし|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="ManagedIdentity"></a> マネージド ID による認証
 `authentication-managed-identity` ポリシーを使用して、API Management サービスのマネージド ID を利用したバックエンド サービスによる認証を行います。 このポリシーでは、指定されたリソースにアクセスするためのアクセス トークンを Azure Active Directory から取得するために、マネージド ID を効果的に利用します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>
```

### <a name="example"></a>例

```xml
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" />
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|authentication-managed-identity |ルート要素。|はい|

### <a name="attributes"></a>属性

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|resource|文字列 をオンにします。 Azure Active Directory におけるターゲット Web API のアプリ ID の URI (セキュリティで保護されたリソース)。|はい|該当なし|
|output-token-variable-name|文字列 をオンにします。 オブジェクトの種類 `string` としてトークン値を受け取るコンテキスト変数の名前。|いいえ|該当なし|
|ignore-error|ブール値。 `true` に設定された場合、アクセス トークンが取得されなかったとしても、ポリシー パイプラインは引き続き実行されます。|いいえ|false|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

## <a name="next-steps"></a>次の手順
ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](api-management-policy-reference.md)
+ [ポリシーのサンプル](policy-samples.md)
