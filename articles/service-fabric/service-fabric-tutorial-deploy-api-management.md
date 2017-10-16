---
title: "Azure Service Fabric と API Management を統合する | Microsoft Docs"
description: "Azure API Management と Service Fabric の基本的な使用方法を簡単に説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: 705212675fc0a869a4374f621d5f2d7e035294dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Service Fabric を使用して API Management をデプロイする
このチュートリアルは、シリーズの第 2 部です。 このチュートリアルでは、Service Fabric を使用して [Azure API Management](../api-management/api-management-key-concepts.md) をセットアップし、Service Fabric のバックエンド サービスにトラフィックをルーティングする方法について示します。  完了すると、トラフィックをバックエンド ステートレス サービスに送信するよう API 操作が構成された状態で、API Management が VNET にデプロイされます。 Service Fabric を使用する Azure API Management のその他のシナリオについては、[概要](service-fabric-api-management-overview.md)を参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * API Management をデプロイする
> * API Management を構成する
> * API 操作の作成
> * バックエンド ポリシーの構成
> * API を製品に追加する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して、セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成する
> * Service Fabric を使用して API Management をデプロイする

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Azure PowerShell モジュールのバージョン 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)または [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールします。
- セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成します。

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure にサインインしてサブスクリプションを選択します。
このチュートリアルでは、[Azure PowerShell][azure-powershell] を使用します。 新しい PowerShell セッションを開始するときに、Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。
 
Azure アカウントにサインインしてサブスクリプションを選択します。

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>API Management をデプロイする
通常、クラウド アプリケーションには、ユーザー、デバイス、またはその他のアプリケーションに単一の受信ポイントを提供するフロントエンド ゲートウェイが必要です。 Service Fabric では、ASP.NET Core アプリケーションなどの任意のステートレス サービスをゲートウェイとして使用できますが、Event Hubs、IoT Hub、Azure API Management など、トラフィック受信用に設計された別のサービスを使用することもできます。 このチュートリアルでは、Azure API Management を Service Fabric アプリケーションへのゲートウェイとして使用する方法の概要を示します。 API Management は Service Fabric と直接統合されるので、バックエンドの Service Fabric サービスへのルーティング規則を豊富に備えた API を公開することができます。 

この時点までに、セキュリティで保護されている [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)が Azure 上にあり、API Management をサブネットの仮想ネットワーク (VNET) と、API Management 用に設計された NSG にデプロイしました。 このチュートリアルで使用する API Management の Resource Manager テンプレートは、前の [Windows クラスター チュートリアル](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター チュートリアル](service-fabric-tutorial-create-vnet-and-linux-cluster.md)で設定した VNET、サブネット、および NSG の名前を使用するようあらかじめ構成されています。 

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

デプロイの `apim.parameters.json` の空のパラメーターに値を入力します。

次のスクリプトを使用して、API Management 用の Resource Manager テンプレートとパラメーター ファイルをデプロイします。

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>API Management を構成する

API Management と Service Fabric クラスターがデプロイされたら、API Management にセキュリティ設定と Service Fabric のバックエンドを構成できます。 これにより、Service Fabric クラスターにトラフィックを送信するバックエンド サービス ポリシーを作成できます。

### <a name="configure-api-management-security"></a>API Management のセキュリティを構成する

Service Fabric のバックエンドを構成するには、まず API Management のセキュリティ設定を構成する必要があります。 セキュリティ設定を構成するには、Azure Portal で API Management サービスに移動します。

#### <a name="enable-the-api-management-rest-api"></a>API Management REST API の有効化

API Management REST API は、バックエンド サービスを構成するための現時点で唯一の方法です。 まず、API Management REST API を有効にして保護します。

 1. API Management サービスで、**[セキュリティ]** から **[Management API]** を選択します。
 2. **[API Management REST API の有効化]** チェック ボックスをオンにします。
 3. **Management API URL** をメモします。後で Service Fabric のバックエンドを設定するときに使用します。
 4. 有効期限とキーを選択して**アクセス トークン**を生成し、ページの下部にある **[生成]** ボタンをクリックします。
 5. **アクセス トークン**をコピーして、保存します。  アクセス トークンは、以降の手順で使用します。 アクセス トークンは主キーや 2 次キーとは異なる点に注意してください。

#### <a name="upload-a-service-fabric-client-certificate"></a>Service Fabric クライアント証明書のアップロード

API Management は、Service Fabric クラスターへのアクセス権を持つクライアント証明書を使用して、サービス検出の際にそのクラスターで認証する必要があります。 わかりやすくするため、このチュートリアルでは、[Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor)を作成したときに指定したのと同じ証明書を使用します。この証明書は、クラスターにアクセスするときに既定で使用できます。

 1. API Management サービスで、**[セキュリティ]** から **[クライアント証明書]** を選択します。
 2. **[+ 追加]** ボタンをクリックします。
 2. Service Fabric クラスターの作成時に指定したクラスター証明書の秘密キー ファイル (.pfx) を選択し、名前を付けて、パスワードを指定します。

> [!NOTE]
> このチュートリアルでは、クライアント認証とクラスター ノード間のセキュリティに同じ証明書を使用します。 Service Fabric クラスターにアクセスするよう構成した別のクライアント証明書がある場合は、その証明書を使用してもかまいません。

### <a name="configure-the-backend"></a>バックエンドの構成

API Management のセキュリティが構成されたので、Service Fabric のバックエンドを構成できます。 Service Fabric のバックエンドの場合は、特定の Service Fabric サービスではなく、Service Fabric クラスターがバックエンドとなります。 これにより、単一のポリシーでクラスター内の複数のサービスにルーティングすることができます。

この手順では、前の手順で生成したアクセス トークンと、API Management にアップロードしたクラスター証明書の拇印が必要です。

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

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
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
2. API Management サービス ブレードで、**[API]** を選択します。
3. 新しい API を追加します。そのためには、**[+API]**、**[空の API]** ボックスの順にクリックし、ダイアログ ボックスに次の必要事項を入力します。

    - **[Web サービスの URL]**: Service Fabric のバックエンドの場合、この URL の値は使用されません。 任意の値を入力できます。 このチュートリアルの場合、"http://servicefabric" を使用します。
    - **[表示名]**: API の名前を指定します。 このチュートリアルの場合、"Service Fabric App" を使用します。
    - **[名前]**: "service-fabric-app" と入力します。
    - **[URL スキーマ]**: **[HTTP]**、**[HTTPS]**、または**[両方]**のいずれかを選択します。 このチュートリアルでは **[両方]**を選択します。
    - **[API URL Suffix]\(API URL サフィックス\)**: API のサフィックスを指定します。 このチュートリアルでは、"myapp" を使用します。
 
4. API の一覧から **[Service Fabric App]** を選択し、**[+ 操作の追加]** をクリックしてフロントエンド API 操作を追加します。 次の値を入力します。
    
    - **[URL]**: **[取得]** を選択し、API の URL パスを指定します。 このチュートリアルでは、"/api/values" を使用します。  既定では、ここで指定した URL パスが、バックエンドの Service Fabric サービスに送信される URL パスになります。 サービスで使用するのと同じ URL パス (この例では "/api/values") を指定する場合は、何も変更しなくても API 操作は正常に機能します。 バックエンドの Service Fabric サービスで使用するのとは異なる URL パスを指定することもできますが、その場合は後ほど操作ポリシー内でパスの再生成を指定する必要があります。
    - **[表示名]**: API の名前を指定します。 このチュートリアルでは、"Values" を使用します。

5. [ **Save**] をクリックします。

## <a name="configure-a-backend-policy"></a>バックエンド ポリシーの構成

バックエンド ポリシーは、すべてを 1 つにまとめる役割を果たします。 このポリシーを使って、要求のルーティング先となるバックエンドの Service Fabric サービスを構成します。 このポリシーは任意の API 操作に適用できます。 [Service Fabric のバックエンド構成](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)では、次の要求ルーティングをコントロールできます。 
 - サービス インスタンスの選択。Service Fabric サービス インスタンス名として、ハードコートされた名前 (`"fabric:/myapp/myservice"` など) か、HTTP 要求から生成される名前 (`"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]` など) のいずれかを指定します。
 - パーティションの解決。任意の Service Fabric パーティション構成を使用して、パーティション キーを生成します。
 - ステートフル サービスのレプリカの選択。
 - 解決の再試行の条件。サービスの場所を再解決して要求を再送信するための条件を指定できます。

このチュートリアルでは、前にデプロイした ASP.NET Core ステートレス サービスに直接要求をルーティングするバックエンド ポリシーを作成します。

 1. 編集アイコンをクリックして、**[コード ビュー]** を選択し、[Values] 操作の**受信ポリシー**を選択および編集します。
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

 1. API Management サービスで、**[製品]** を選択します。
 2. 既定では、API Management には Starter と Unlimited という 2 つの製品があります。 Unlimited を選択します。
 3. **[+ API の追加]** を選択します。
 4. 前の手順で作成した `Service Fabric App` API を選択し、**[選択]** ボタンをクリックします。

### <a name="test-it"></a>テストする

これで、Azure Portal から直接、API Management を通じて Service Fabric のバックエンド サービスに要求を送信できるようになりました。

 1. API Management サービスで、**[API]** を選択します。
 2. 前の手順で作成した **Service Fabric App** API で、**[テスト]** タブ、**[Values]** 操作の順に選択します。
 3. **[送信]** ボタンをクリックして、バックエンド サービスにテスト要求を送信します。  次のような HTTP 応答が表示されます。

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にログインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](http://portal.azure.com) にログインして確認できます。 リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使用します。

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * API Management をデプロイする
> * API Management を構成する
> * API 操作の作成
> * バックエンド ポリシーの構成
> * API を製品に追加する


[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json