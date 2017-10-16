---
title: "Azure Service Fabric のリバース プロキシによる安全な通信 | Microsoft Docs"
description: "リバース プロキシを構成して、安全なエンド ツー エンド通信を実現します。"
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>リバース プロキシを使用したセキュリティで保護されたサービスへの接続

この記事では、リバース プロキシとサービス間で安全な通信を確立し、セキュリティで保護されたエンド ツー エンドのチャネルを実現する方法について説明します。

セキュリティで保護されたサービスへの接続は、リバース プロキシが HTTPS をリッスンするように構成されている場合にのみサポートされます。 このドキュメントの残りの部分では、これを前提としています。
Service Fabric でリバース プロキシを構成するには、「[Azure Service Fabric のリバース プロキシ](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)」を参照してください。

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>リバース プロキシとサービス間の安全な通信の確立 

### <a name="reverse-proxy-authenticating-to-services"></a>リバース プロキシによるサービスの認証:
サービスに対するリバース プロキシ自体の識別には、**クラスター**の[リソースの種類のセクション](../azure-resource-manager/resource-group-authoring-templates.md)の、***reverseProxyCertificate*** プロパティで指定された証明書が使用されます。 サービスにロジックを実装して、リバース プロキシによって提示される証明書を検証できます。 サービスでは、構成パッケージの構成設定として、許容されるクライアント証明書の詳細事項を指定できます。 これは実行時に読み込まれ、リバース プロキシによって提示される証明書の検証に使用されます。 構成設定を追加するには、[アプリケーション パラメーターの管理](service-fabric-manage-multiple-environment-app-configuration.md)に関する記事を参照してください。 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>サービスの提示する証明書を使用した、リバース プロキシによるサービス ID の検証:
リバース プロキシでは、サービスによって提示された証明書のサーバー証明書の検証に、None、ServiceCommonNameAndIssuer、ServiceCertificateThumbprints のオプションのいずれかを使用できます。
これら 3 つのオプションのいずれかを選択するには、[fabricSettings](service-fabric-cluster-fabric-settings.md) の下の ApplicationGateway/Http の要素のパラメーター セクションで、**ApplicationCertificateValidationPolicy** を指定します。

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

これらの各オプションの追加の構成について詳しくは、次のセクションを参照してください。

### <a name="service-certificate-validation-options"></a>サービス証明書の検証オプション 

- **None**: リバース プロキシではプロキシ対象のサービス証明書の検証がスキップされて、セキュリティで保護された接続が確立します。　 これは既定の動作です。
ApplicationGateway/Http の要素のパラメーター セクションで、**ApplicationCertificateValidationPolicy** を **None** の値で指定します。

- **ServiceCommonNameAndIssuer**: リバース プロキシは、証明書の共通名および直近の発行者のサムプリントに基づき、サービスによって提示された証明書を検証します。ApplicationGateway/Http の要素のパラメーター セクションで、**ApplicationCertificateValidationPolicy** を **ServiceCommonNameAndIssuer** の値で指定します。

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

サービス共通名と発行者のサムプリントの一覧を指定するには、以下に示すように fabricSettings の下に **ApplicationGateway/Http/ServiceCommonNameAndIssuer** の要素を追加します。 パラメーター配列の要素には、証明書共通名と発行者のサムプリントの複数のペアを追加できます。 

エンドポイントのリバース プロキシが接続し、共通名と発行者のサムプリントがここで指定された値のいずれかと一致する証明書が提示されると、SSL チャネルが確立されます。 証明書の詳細と一致しなかった場合は、リバース プロキシでクライアントの要求が失敗し、502 (無効なゲートウェイ) の状態コードが表示されます。 また HTTP ステータス行に "Invalid SSL Certificate" (無効な SSL 証明書) の語句が含まれます。 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: リバース プロキシでは、サムプリントに基づいてプロキシ対象のサービス証明書が検証されます。 自己署名証明書でサービスが構成されている場合は、次の方法を選択できます。ApplicationGateway/Http の要素のパラメーター セクションで、**ApplicationCertificateValidationPolicy** を **ServiceCertificateThumbprints** の値で指定します。　

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

また、ApplicationGateway/Http の要素のパラメーター セクションの下で、**ServiceCertificateThumbprints** のエントリを使用してサムプリントを指定します。　 値フィールドでは、次に示すように、コンマ区切りリストとして複数のサムプリントを指定できます。

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

サーバー証明書のサムプリントがこの構成エントリに一覧表示されている場合、リバース プロキシは SSL 接続に成功します。 それ以外の場合は接続が終了し、クライアントの要求が失敗して 502 (無効なゲートウェイ) のエラーが表示されます。 また HTTP ステータス行に "Invalid SSL Certificate" (無効な SSL 証明書) の語句が含まれます。

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>サービスが、セキュリティで保護されたエンドポイントとセキュリティで保護されていないエンドポイントを公開した場合のエンドポイント選択のロジック
Service Fabric では、1 つのサービスに複数のエンドポイントを構成できます。 「[サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)」をご覧ください。

リバース プロキシは、**ListenerName** のクエリ パラメーターに基づいて要求を転送するエンドポイントを 1 つ選択します。 指定されていない場合は、エンドポイント リストから任意のエンドポイントが選択されます。 これは HTTP または HTTPS エンドポイントです。 ある可能性がありますシナリオ/要件で運用するため、「セキュリティで保護されたモードのみ」、つまり、リバース プロキシします。 セキュリティで保護されないエンドポイントへの要求を転送するセキュリティで保護されたリバース プロキシをしたくありません。 指定することによってこれを行う、 **SecureOnlyMode**値を持つ構成エントリ**true** ApplicationGateway/Http 要素のパラメーターのセクションでします。   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> リバース プロキシが **SecureOnlyMode** で動作しているときに、クライアントによって HTTP (セキュリティ保護なし) エンドポイントに対応する **ListenerName** が指定されると、要求が失敗して 404 (見つかりません) の HTTP 状態コードが表示されます。

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>リバース プロキシ経由でのクライアント証明書認証の設定
リバース プロキシで SSL の終了が発生すると、クライアント証明書のすべてのデータが失われます。 サービスがクライアント証明書の認証を実行するようにするには、ApplicationGateway/Http の要素のパラメーター セクションで、**ForwardClientCertificate** を設定します。

1. **ForwardClientCertificate** を **false** に設定すると、リバース プロキシはクライアントとの SSL ハンドシェイク時に、クライアント証明書を要求しません。
これは既定の動作です。

2. **ForwardClientCertificate** を **true** に設定すると、リバース プロキシはクライアントとの SSL ハンドシェイク時に、クライアント証明書を要求します。
**X-Client-Certificate** という名前のカスタム HTTP ヘッダーに、クライアント証明書のデータが転送されます。 ヘッダーの値は、クライアント証明書のデータが Base64 でエンコードされた PEM 形式の文字列です。 サービスで証明書のデータが調査された後に、要求が成功または失敗し、該当する状態コードが表示されます。
クライアントが証明書を提示しない場合、リバース プロキシは空のヘッダーを転送し、サービスによって処理されます。

> リバース プロキシは、単なるフォワーダーです。 クライアントの証明書のいずれかの検証は実行されません。


## <a name="next-steps"></a>次のステップ
* 「[Configure reverse proxy to connect to secure services (セキュリティで保護されたサービスに接続するためのリバース プロキシの構成)](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services)」を参照して Azure Resource Manager テンプレートのサンプルを取得し、サービス証明書のさまざまな検証オプションでセキュリティ保護されたリバース プロキシを構成します。
* [GitHub のサンプル プロジェクト](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)で、サービス間の HTTP 通信の例を確認します。
* [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services の OWIN を使用する Web API](service-fabric-reliable-services-communication-webapi.md)
* [クラスター証明書の管理](service-fabric-cluster-security-update-certs-azure.md)
