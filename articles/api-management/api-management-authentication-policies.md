---
title: "Azure API Management の認証ポリシー | Microsoft Docs"
description: "Azure API Management で使用できる認証ポリシーについて説明します。"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: f447e43799e56114d52b0dc0f5c36265f2870c8e

---
# <a name="api-management-authentication-policies"></a>API Management の認証ポリシー
このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](http://go.microsoft.com/fwlink/?LinkID=398186)」をご覧ください。  
  
##  <a name="a-nameauthenticationpoliciesa-authentication-policies"></a><a name="AuthenticationPolicies"></a> 認証ポリシー  
  
-   [基本認証](api-management-authentication-policies.md#Basic) -基本認証を使用してバックエンド サービスで認証します。  
  
-   [クライアント証明書による認証](api-management-authentication-policies.md#ClientCertificate) -クライアント証明書を使用してバックエンド サービスで認証します。  
  
##  <a name="a-namebasica-authenticate-with-basic"></a><a name="Basic"></a> 基本認証  
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
  
|名前|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|username|基本認証の資格情報のユーザー名を指定します。|はい|該当なし|  
|パスワード|基本認証の資格情報のパスワードを指定します。|はい|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
  
-   **ポリシー スコープ:** API  
  
##  <a name="a-nameclientcertificatea-authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> クライアント証明書による認証  
 `authentication-certificate` ポリシーを使用し、クライアント証明書を使用してバックエンド サービスで認証します。 証明書は最初に [API Management にインストール](http://go.microsoft.com/fwlink/?LinkID=511599)する必要があります。サムプリントによって識別されます。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>例  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>要素  
  
|名前|説明|必須|  
|----------|-----------------|--------------|  
|authentication-certificate|ルート要素。|はい|  
  
### <a name="attributes"></a>属性  
  
|名前|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|thumbprint|クライアント証明書のサムプリント。|はい|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
  
-   **ポリシー スコープ:** API  
  

## <a name="next-steps"></a>次のステップ
ポリシーを使用する方法の詳細については、「[API Management のポリシー](api-management-howto-policies.md)」を参照してください。  


<!--HONumber=Jan17_HO2-->


