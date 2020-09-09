---
title: Azure Service Fabric のリバース プロキシによる安全な通信
description: Azure Service Fabric アプリケーションにリバース プロキシを構成して、安全なエンド ツー エンド通信を実現します。
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256137"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>リバース プロキシを使用したセキュリティで保護されたサービスへの接続

この記事では、リバース プロキシとサービス間で安全な通信を確立し、セキュリティで保護されたエンド ツー エンドのチャネルを実現する方法について説明します。 リバース プロキシに関する詳細については、「[Azure Service Fabric のリバース プロキシ](service-fabric-reverseproxy.md)」を参照してください。

> [!IMPORTANT]
> セキュリティで保護されたサービスへの接続は、リバース プロキシが HTTPS をリッスンするように構成されている場合にのみサポートされます。 この記事では、そのような構成を前提にしています。 Service Fabric でリバース プロキシを構成するには、「[Setup reverse proxy in Azure Service Fabric](service-fabric-reverseproxy-setup.md)」 (Azure Service Fabric でリバース プロキシを設定する) を参照してください。

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>リバース プロキシとサービス間の安全な通信の確立 

### <a name="reverse-proxy-authenticating-to-services"></a>リバース プロキシによるサービスの認証:
リバース プロキシでは、その証明書を利用してサービスにその ID を証明します。 Azure クラスターの場合は、Resource Manager テンプレートの [**Microsoft.ServiceFabric/clusters**](/azure/templates/microsoft.servicefabric/clusters) の「[リソースの種類](../azure-resource-manager/templates/template-syntax.md)」セクションにある ***reverseProxyCertificate*** プロパティで証明書が指定されます。 スタンドアロン クラスターの場合、証明書は ClusterConfig.json の **[セキュリティ]** セクションの ***ReverseProxyCertificate*** または ***ReverseProxyCertificateCommonNames*** プロパティで指定されます。 詳細については、「[スタンドアロン クラスターでリバース プロキシを有効にする](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters)」を参照してください。 

サービスにロジックを実装して、リバース プロキシによって提示される証明書を検証できます。 サービスでは、構成パッケージの構成設定として、許容されるクライアント証明書の詳細事項を指定できます。 これは実行時に読み込まれ、リバース プロキシによって提示される証明書の検証に使用されます。 構成設定を追加するには、[アプリケーション パラメーターの管理](service-fabric-manage-multiple-environment-app-configuration.md)に関する記事を参照してください。 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>サービスの提示する証明書を使用した、リバース プロキシによるサービス ID の検証:
リバース プロキシでは、サービスによって提示された証明書のサーバー証明書の検証を実行するために、次のポリシーをサポートします。None、ServiceCommonNameAndIssuer、および ServiceCertificateThumbprints。
リバース プロキシで使用するポリシーを選択するには、**ApplicationCertificateValidationPolicy** を [fabricSettings](service-fabric-cluster-fabric-settings.md)の **ApplicationGateway/Http** セクションに指定します。

次のセクションで、これらの各オプションの構成の詳細を示します。

### <a name="service-certificate-validation-options"></a>サービス証明書の検証オプション 

- **なし**: リバース プロキシではプロキシ対象のサービス証明書の検証がスキップされて、セキュリティで保護された接続が確立します。 これは既定の動作です。
値が **None**である **ApplicationCertificateValidationPolicy**を、[**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) セクションに指定します。

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

- **ServiceCommonNameAndIssuer**:リバース プロキシでは、証明書の共通名と直近の発行者の拇印に基づいて、サービスから提示された証明書が検証されます。値が **ServiceCommonNameAndIssuer** である **ApplicationCertificateValidationPolicy** を、[**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) セクションに指定します。

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

   サービス共通名と発行者のサムプリントの一覧を指定するには、以下に示すように **fabricSettings** の下に [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) セクションを追加します。 **parameters** 配列には、証明書共通名と発行者のサムプリントの複数のペアを追加できます。 

   エンドポイントのリバース プロキシで接続され、共通名と発行者のサムプリントがここで指定された値のいずれかと一致する証明書が提示されると、TLS チャネルが確立されます。
   証明書の詳細と一致しなかった場合は、リバース プロキシでクライアントの要求が失敗し、502 (無効なゲートウェイ) の状態コードが表示されます。 また HTTP ステータス行に "Invalid SSL Certificate" (無効な SSL 証明書) の語句が含まれます。 

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

- **ServiceCertificateThumbprints**:リバース プロキシでは、サムプリントに基づいてプロキシ対象のサービス証明書が検証されます。 サービスが自己署名証明書を使って構成されている場合は、このルートを選択することができます。値が **ServiceCertificateThumbprints** である **ApplicationCertificateValidationPolicy** を、[**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) セクションに指定します。

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

   また、**ServiceCertificateThumbprints** エントリを使用して **ApplicationGateway/Http** セクションにサムプリントを指定します。 値フィールドでは、次に示すように、コンマ区切りリストとして複数のサムプリントを指定できます。

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

   サーバー証明書のサムプリントがこの構成エントリに表示されている場合、リバース プロキシの TLS 接続は成功します。 それ以外の場合は接続が終了し、クライアントの要求が失敗して 502 (無効なゲートウェイ) のエラーが表示されます。 また HTTP ステータス行に "Invalid SSL Certificate" (無効な SSL 証明書) の語句が含まれます。

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>サービスが、セキュリティで保護されたエンドポイントとセキュリティで保護されていないエンドポイントを公開した場合のエンドポイント選択のロジック
Service Fabric では、1 つのサービスに複数のエンドポイントを構成できます。 詳しくは、「[サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)」をご覧ください。

リバース プロキシは、[service URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy)の **ListenerName** クエリ パラメーターに基づいて要求を転送するエンドポイントを 1 つ選択します。 **ListenerName** パラメーターが指定されていない場合、リバース プロキシは、エンドポイントの一覧から任意のエンドポイントを選択できます。 サービス用に構成されたエンドポイントに応じて、選択されるエンドポイントは、HTTP または HTTPS エンドポイントになる可能性があります。 リバース プロキシが "保護限定モード" で動作すること、つまりセキュリティで保護されたリバース プロキシがセキュリティで保護されていないエンドポイントに要求を転送しないことが求められるシナリオや要件が存在することがあります。 リバース プロキシを保護限定モードに設定するには、値が **true** である **SecureOnlyMode** 構成エントリを、[**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) セクションに指定します。   

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

> [!NOTE]
> リバース プロキシが **SecureOnlyMode** で動作しているときに、クライアントによって HTTP (セキュリティ保護なし) エンドポイントに該当する **ListenerName** が指定された場合、リバース プロキシは、404 (見つかりません) HTTP 状態コードで要求をエラーにします。

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>リバース プロキシ経由でのクライアント証明書認証の設定
リバース プロキシで TLS の終了が発生すると、クライアント証明書のすべてのデータが失われます。 サービスがクライアント証明書の認証を実行するようにするには、[**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) セクションに **ForwardClientCertificate** を指定します。

1. **ForwardClientCertificate** を **false** に設定すると、リバース プロキシのクライアントとの TLS ハンドシェイク時にクライアント証明書は要求されません。
これは既定の動作です。

2. **ForwardClientCertificate** を **true** に設定すると、リバース プロキシのクライアントとの TLS ハンドシェイク時にクライアント証明書が要求されます。
**X-Client-Certificate** という名前のカスタム HTTP ヘッダーに、クライアント証明書のデータが転送されます。 ヘッダーの値は、クライアント証明書のデータが Base64 でエンコードされた PEM 形式の文字列です。 サービスで証明書のデータが調査された後に、要求が成功または失敗し、該当する状態コードが表示されます。
クライアントが証明書を提示しない場合、リバース プロキシにより空のヘッダーが転送され、サービスによって処理されます。

> [!NOTE]
> リバース プロキシは、転送サービスとしてのみ機能します。 クライアント証明書の検証は実行しません。


## <a name="next-steps"></a>次のステップ
* [クラスターでのリバース プロキシの設定と構成](service-fabric-reverseproxy-setup.md)
* 「[セキュリティで保護されたサービスに接続するためのリバース プロキシの構成](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)」を参照します。
* [GitHub のサンプル プロジェクト](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)で、サービス間の HTTP 通信の例を確認します。
* [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services の OWIN を使用する Web API](./service-fabric-reliable-services-communication-aspnetcore.md)
* [クラスター証明書の管理](service-fabric-cluster-security-update-certs-azure.md)
