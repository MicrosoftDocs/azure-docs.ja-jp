---
title: "Azure Service Fabric と API Management のクイック スタート | Microsoft Docs"
description: "このガイドでは、Azure API Management と Service Fabric の基本的な使用方法を簡単に説明します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2160e2e65de5c65df8a13248bad4f626def86e49
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---

# <a name="service-fabric-with-azure-api-management-quick-start"></a>Azure Service Fabric と API Management のクイック スタート

このガイドでは、Azure API Management と Service Fabric を設定し、Service Fabric のバックエンド サービスにトラフィックを送信する最初の API 操作を構成する方法を説明します。 Service Fabric を使用する Azure API Management のその他のシナリオについては、[概要](service-fabric-api-management-overview.md)を参照してください。 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>API Management と Service Fabric を Azure にデプロイする

まず、API Management と Service Fabric クラスターを Azure の共有 Virtual Network にデプロイします。 これにより、API Management が Service Fabric と直接通信して、サービスを検出し、サービス パーティションを解決して、Service Fabric の任意のバックエンド サービスにトラフィックを直接転送できるようになります。

### <a name="topology"></a>トポロジ

このガイドでは、次のトポロジを Azure にデプロイします。Azure の同じ Virtual Network のサブネット内に Azure API Management と Service Fabric があります。

 ![キャプション][sf-apim-topology-overview]

簡単に始められるよう、デプロイの手順ごとに Resource Manager テンプレートが用意されています。

 - ネットワーク トポロジ:
    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]
 - Service Fabric クラスター:
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]
 - API Management:
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure にサインインしてサブスクリプションを選択する

このガイドでは [Azure PowerShell][azure-powershell] を使用します。 新しい PowerShell セッションを開始するときに、Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。
 
Azure アカウントにサインインします。

```powershell
PS > Login-AzureRmAccount
```

サブスクリプションを選択します。

```powershell
PS > Get-AzureRmSubscription
PS > Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>リソース グループの作成

デプロイの新しいリソース グループを作成し、 リソース グループに名前を付けて、場所を指定します。

```powershell
PS > New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>ネットワーク トポロジをデプロイする

まず、API Management と Service Fabric クラスターのデプロイ先となるネットワーク トポロジを設定します。 [network.json][network-arm] Resource Manager テンプレートは、2 つのサブネットと 2 つのネットワーク セキュリティ グループ (NSG) を持つ Virtual Network (VNET) を作成するよう構成されています。 

[network.parameters.json][network-parameters-arm] パラメーター ファイルには、API Management と Service Fabric のデプロイ先となるサブネットと NSG の名前が含まれています。 このガイドでは、パラメーターの値を変更する必要はありません。 これらの値は API Management と Service Fabric の Resource Manager テンプレートで使用されるので、ここで値を変更すると、それに応じて他の Resource Manager テンプレートでも値を変更しなければならなくなります。 

 1. 次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。

    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]

 2. 次の PowerShell コマンドを使用して、ネットワーク設定用の Resource Manager テンプレートとパラメーター ファイルをデプロイします。

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>Service Fabric クラスターをデプロイする

ネットワーク リソースのデプロイが完了したら、次は Service Fabric クラスターを VNET の所定のサブネットと NSG にデプロイします。 このチュートリアルで使用する Service Fabric の Resource Manager テンプレートは、前の手順で設定した VNET、サブネット、および NSG の名前を使用するようあらかじめ構成されています。 

Service Fabric クラスターの Resource Manager テンプレートは、証明書セキュリティで保護されたクラスターを作成するよう構成されています。 この証明書は、クラスターのノード間通信を保護し、Service Fabric クラスターへのユーザー アクセスを管理するために使用されます。 API Management はこの証明書を使って、サービスを検出する際に Service Fabric Naming Service にアクセスします。

この手順では、クラスターのセキュリティのため Key Vault に証明書があることが必要です。 Key Vault を使ってセキュリティ保護したクラスターを設定する方法について詳しくは、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。

> [!NOTE]
> クラスターのアクセスに使用する証明書に加えて、Azure Active Directory 認証を追加することもできます。 Azure Active Directory は、Service Fabric クラスターへのユーザー アクセスを管理するのにお勧めの方法ですが、このチュートリアルでは必須ではありません。 証明書はクラスターのノード間のセキュリティと Azure API Management 認証のために必要ですが、現時点では Service Fabric バックエンドの Azure Active Directory による認証はサポートしていません。

 1. 次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
 
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]

 2. デプロイの `cluster.parameters.json` ファイルの空のパラメーターに値を入力します。これには、クラスター証明書の [Key Vault 情報](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault)も含まれます。

 3. 次の PowerShell コマンドを使用して、Service Fabric クラスターを作成するための Resource Manager テンプレートとパラメーター ファイルをデプロイします。

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>API Management をデプロイする

最後に、API Management を VNET の所定のサブネットと NSG に デプロイします。 Service Fabric クラスターのデプロイが完了していなくても、API Management のデプロイを開始できます。 

このチュートリアルで使用する API Management の Resource Manager テンプレートは、前の手順で設定した VNET、サブネット、および NSG の名前を使用するようあらかじめ構成されています。 

 1. 次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
 
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

 2. デプロイの `apim.parameters.json` の空のパラメーターに値を入力します。

 3. 次の PowerShell コマンドを使用して、API Management 用の Resource Manager テンプレートとパラメーター ファイルをデプロイします。

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>API Management を構成する

API Management と Service Fabric クラスターがデプロイされたら、API Management に Service Fabric のバックエンドを構成できます。 これにより、Service Fabric クラスターにトラフィックを送信するバックエンド サービス ポリシーを作成できます。

### <a name="api-management-security"></a>API Management のセキュリティ

Service Fabric のバックエンドを構成するには、まず API Management のセキュリティ設定を構成する必要があります。 セキュリティ設定を構成するには、Azure Portal で API Management サービスに移動します。

#### <a name="enable-the-api-management-rest-api"></a>API Management REST API の有効化

API Management REST API は、バックエンド サービスを構成するための現時点で唯一の方法です。 まず、API Management REST API を有効にして保護します。

 1. API Management サービスで、**[セキュリティ]** の **[Management API - プレビュー]** を選択します。
 2. **[API Management REST API の有効化]** チェック ボックスをオンにします。
 3. Management API URL は、後ほど Service Fabric のバックエンドを設定する際に使用する URL です。
 4. 有効期限とキーを選択して**アクセス トークン**を生成し、ページの下部にある **[生成]** ボタンをクリックします。
 5. **アクセス トークン**をコピーして保存します。これは以降の手順で使用します。 アクセス トークンは主キーや 2 次キーとは異なる点に注意してください。

#### <a name="upload-a-service-fabric-client-certificate"></a>Service Fabric クライアント証明書のアップロード

API Management は、Service Fabric クラスターへのアクセス権を持つクライアント証明書を使用して、サービス検出の際にそのクラスターで認証する必要があります。 このチュートリアルでは、単純に Service Fabric クラスターの作成時に指定した証明書を使用します。これは、クラスターへのアクセスに既定で使用できる証明書です。

 1. API Management サービスで、**[セキュリティ]** の **[クライアント証明書 - プレビュー]** を選択します。
 2. **[+ 追加]** ボタンをクリックします。
 2. Service Fabric クラスターの作成時に指定したクラスター証明書の秘密キー ファイル (.pfx) を選択し、名前を付けて、パスワードを指定します。

> [!NOTE]
> このチュートリアルでは、クライアント認証とクラスター ノード間のセキュリティに同じ証明書を使用します。 Service Fabric クラスターにアクセスするよう構成した別のクライアント証明書がある場合は、その証明書を使用してもかまいません。

### <a name="configure-the-backend"></a>バックエンドの構成

API Management のセキュリティが構成されたので、Service Fabric のバックエンドを構成できます。 Service Fabric のバックエンドの場合は、特定の Service Fabric サービスではなく、Service Fabric クラスターがバックエンドとなります。 これにより、単一のポリシーでクラスター内の複数のサービスにルーティングすることができます。

この手順では、前に生成したアクセス トークンと、前の手順で API Management にアップロードしたクラスター証明書の拇印が必要です。

> [!NOTE]
> 前の手順で API Management に別のクライアント証明書を使用した場合は、この手順のクラスター証明書の拇印だけでなく、そのクライアント証明書の拇印も必要になります。

API Management REST API を有効化したときにメモしておいた API Management API URL に次の HTTP PUT 要求を送信して、Service Fabric のバックエンド サービスを構成します。 コマンドが正常に実行されると、`HTTP 201 Created` 応答が表示されます。 各フィールドの詳細については、API Management の[バックエンド API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)を参照してください。

HTTP コマンドと URL:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

要求ヘッダー:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

要求本文:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

この **url** パラメーターはクラスター内のサービスの完全修飾サービス名であり、バックエンド ポリシーにサービス名が指定されていない場合に、既定ですべての要求がこのサービスにルーティングされます。 フォールバック サービスが不要の場合は、"fabric:/fake/service" などの仮のサービス名を使用できます。

各フィールドの詳細については、API Management の[バックエンド API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)を参照してください。

#### <a name="example"></a>例

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Service Fabric のバックエンド サービスのデプロイ

Service Fabric を API Management へのバックエンドとして構成したので、次は Service Fabric サービスにトラフィックを送信する API のバックエンド ポリシーを作成できます。 ただしその前に、要求を受け入れるためには、Service Fabric でサービスが実行している必要があります。

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>HTTP エンドポイントを持つ Service Fabric サービスの作成

このチュートリアルでは、既定の Web API プロジェクト テンプレートを使用して、基本的なステートレス ASP.NET Core リライアブル サービスを作成します。 これにより、サービスの HTTP エンドポイントが作成され、Azure API Management を通じて公開することができます。

```
/api/values
```

まず、[ASP.NET Core 開発のための開発環境を設定](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core)します。

開発環境を設定したら、Visual Studio を管理者として起動し、ASP.NET Core サービスを作成します。

 1. Visual Studio で、[ファイル] の [新しいプロジェクト] を選択します。
 2. [クラウド] の下にある Service Fabric アプリケーション テンプレートを選択し、**"ApiApplication"** という名前を付けます。
 3. ASP.NET Core サービス テンプレートを選択し、プロジェクトに **"WebApiService"** という名前を付けます。
 4. Web API ASP.NET Core 1.1 プロジェクト テンプレートを選択します。
 5. プロジェクトが作成されたら、`PackageRoot\ServiceManifest.xml` を開いて、エンドポイント リソースの構成から `Port` 属性を削除します。
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    これにより、Service Fabric がアプリケーションのポート範囲 (クラスターの Resource Manager テンプレートのネットワーク セキュリティ グループを通じて開いたもの) から動的にポートを指定することができるようになり、そこに API Management からのトラフィックが流れるようになります。
 
 6. Visual Studio で F5 キーを押して、Web API がローカルで使用できることを確認します。 

    Service Fabric Explorer を開き、ASP.NET Core サービスの特定のインスタンスまでドリルダウンして、サービスがリッスンするベース アドレスを表示します。 `/api/values` をベース アドレスに追加してブラウザーで開きます。 すると、Web API テンプレートの ValuesController で Get メソッドが呼び出されます。 このメソッドは、テンプレートで指定される既定の応答として、2 つの文字列を格納する JSON 配列を返します。

    ```json
    ["value1", "value2"]`
    ```

    これが、Azure の API Management を通じて公開するエンドポイントになります。

 7. 最後に、アプリケーションを Azure のクラスターにデプロイします。 [Visual Studio を使用](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box)し、Application プロジェクトを右クリックして **[公開]** を選択します。 クラスター エンドポイント (`mycluster.westus.cloudapp.azure.com:19000` など) を指定して、アプリケーションを Azure の Service Fabric クラスターにデプロイします。

`fabric:/ApiApplication/WebApiService` という名前の ASP.NET Core ステートレス サービスが、Azure の Service Fabric クラスターで実行されているはずです。

## <a name="create-an-api-operation"></a>API 操作の作成

これまでの手順で、Service Fabric クラスターで実行中の ASP.NET Core ステートレス サービスと通信する外部クライアントが使用する API Management 操作を作成する準備が整いました。

 1. Azure Portal にログインし、API Management サービス デプロイに移動します。
 2. API Management サービス ブレードで、**[API - プレビュー]** を選択します。
 3. **[Blank API]\(空の API\)** ボックスをクリックし、ダイアログ ボックスに必要事項を入力して新しい API を追加します。

     - **[Web サービスの URL]**: Service Fabric のバックエンドの場合、この URL の値は使用されません。 任意の値を入力できます。 このチュートリアルでは、`http://servicefabric` を使用します。
     - **[名前]**: API の名前を指定します。 このチュートリアルでは、`Service Fabric App` を使用します。
     - **[URL スキーマ]**: HTTP か HTTPS、またはその両方を選択します。 このチュートリアルでは、`both` を使用します。
     - **[API URL Suffix]\(API URL サフィックス\)**: API のサフィックスを指定します。 このチュートリアルでは、`myapp` を使用します。
 
 4. API が作成されたら、**[+ 操作の追加]** をクリックしてフロントエンドの API 操作を追加します。 次の値を入力します。
    
     - **[URL]**: `GET` を選択して API の URL パスを指定します。 このチュートリアルでは、`/api/values` を使用します。
     
       既定では、ここで指定した URL パスが、バックエンドの Service Fabric サービスに送信される URL パスになります。 サービスで使用するのと同じ URL パス (この例では `/api/values`) を指定する場合は、何も変更しなくても API 操作は正常に機能します。 バックエンドの Service Fabric サービスで使用するのとは異なる URL パスを指定することもできますが、その場合は後ほど操作ポリシー内でパスの再生成を指定する必要があります。
     - **[表示名]**: API の名前を指定します。 このチュートリアルでは、`Values` を使用します。

## <a name="configure-a-backend-policy"></a>バックエンド ポリシーの構成

バックエンド ポリシーは、すべてを 1 つにまとめる役割を果たします。 このポリシーを使って、要求のルーティング先となるバックエンドの Service Fabric サービスを構成します。 このポリシーは任意の API 操作に適用できます。 [Service Fabric のバックエンド構成](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)では、次の要求ルーティングをコントロールできます。 
 - サービス インスタンスの選択。Service Fabric サービス インスタンス名として、ハードコートされた名前 (`"fabric:/myapp/myservice"` など) か、HTTP 要求から生成される名前 (`"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]` など) のいずれかを指定します。
 - パーティションの解決。任意の Service Fabric パーティション構成を使用して、パーティション キーを生成します。
 - ステートフル サービスのレプリカの選択。
 - 解決の再試行の条件。サービスの場所を再解決して要求を再送信するための条件を指定できます。

このチュートリアルでは、前にデプロイした ASP.NET Core ステートレス サービスに直接要求をルーティングするバックエンド ポリシーを作成します。

 1. 編集アイコンをクリックして **[コード ビュー]** を選択し、`Values` 操作の **受信ポリシー**を選択して編集します。
 2. ポリシー コード エディターで、下記に示すように `set-backend-service` ポリシーを受信ポリシーの下に追加し、**[保存]** ボタンをクリックします。
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
           sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
      </inbound>
      <backend>
        <base/>
      </backend>
      <outbound>
        <base/>
      </outbound>
    </policies>
    ```

Service Fabric バックエンド ポリシーの全属性については、[API Management バックエンドに関するドキュメント](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)を参照してください。

### <a name="add-the-api-to-a-product"></a>API を製品に追加する 

API を呼び出すには、ユーザーにアクセス権を付与する製品にその API を追加しておく必要があります。 

 1. API Management サービスで、**[製品 - プレビュー]** を選択します。
 2. 既定では、API Management には Starter と Unlimited という 2 つの製品があります。 Unlimited を選択します。
 3. API を選択します。
 4. **[+ 追加]** ボタンをクリックします。
 5. 前の手順で作成した `Service Fabric App` API を選択し、**[選択]** ボタンをクリックします。

### <a name="test-it"></a>テストする

これで、Azure Portal から直接、API Management を通じて Service Fabric のバックエンド サービスに要求を送信できるようになりました。

 1. API Management サービスで、**[API - プレビュー]** を選択します。
 2. 前の手順で作成した `Service Fabric App` API で、**[テスト]** タブを選択します。
 3. **[送信]** ボタンをクリックして、バックエンド サービスにテスト要求を送信します。

## <a name="next-steps"></a>次のステップ

この時点で、Service Fabric と API Management の基本設定が完了しています。

このチュートリアルでは、Service Fabric クラスターを簡単に設定できるよう、基本的な証明書ベースのユーザー認証を使用しています。 実際の Service Fabric クラスターでは、[Azure Active Directory 認証](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication)を使ったより高度なユーザー認証を使用することをお勧めします。 

次は、アプリケーションが実際のトラフィックに対応できるよう、[Azure API Management で高度な製品設定を作成して構成](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules)します。

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png

