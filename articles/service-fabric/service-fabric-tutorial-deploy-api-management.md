---
title: Azure で API Management と Service Fabric を統合する
description: Azure API Management をすぐに使い始め、Service Fabric のバックエンド サービスにトラフィックをルーティングする方法について説明します。
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 681a1c5241743a0164d83d73753efa0b6c446109
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735592"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Azure で API Management と Service Fabric を統合する

Service Fabric での Azure API Management のデプロイは高度なシナリオです。  API Management は、バックエンドの Service Fabric サービスのルーティング規則を豊富に備えた API を公開する必要があるときに便利です。 通常、クラウド アプリケーションには、ユーザー、デバイス、またはその他のアプリケーションに単一の受信ポイントを提供するフロントエンド ゲートウェイが必要です。 Service Fabric では、ASP.NET Core アプリケーション、Event Hubs、IoT Hub、Azure API Management など、トラフィック イングレス用に設計された任意のステートレス サービスをゲートウェイとして使用できます。

この記事では、Service Fabric を使用して [Azure API Management](../api-management/api-management-key-concepts.md) を設定し、Service Fabric のバックエンド サービスにトラフィックをルーティングする方法について示します。  完了すると、トラフィックをバックエンド ステートレス サービスに送信するよう API 操作が構成された状態で、API Management が VNET にデプロイされます。 Service Fabric を使用する Azure API Management のその他のシナリオについては、[概要](service-fabric-api-management-overview.md)を参照してください。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>可用性

> [!IMPORTANT]
> この機能は、必要な仮想ネットワーク サポートのために API Management の **Premium** および **Developer** レベルで使用できます。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のことを行います。

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Azure PowerShell](/powershell/azure/install-az-ps) または [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
* ネットワーク セキュリティ グループにセキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を作成します。
* Windows クラスターをデプロイする場合は、Windows 開発環境を設定します。 [Visual Studio 2019](https://www.visualstudio.com)、**Azure 開発** ワークロード、**ASP.NET および Web 開発** ワークロード、 **.NET Core クロス プラットフォーム開発** ワークロードをインストールします。  その後、[.NET 開発環境](service-fabric-get-started.md)をセットアップします。

## <a name="network-topology"></a>ネットワーク トポロジ

この時点までに、セキュリティで保護されている [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)が Azure 上にあり、API Management をサブネットの仮想ネットワーク (VNET) と、API Management 用に設計された NSG にデプロイしました。 この記事のために、[Windows クラスターのチュートリアル](service-fabric-tutorial-create-vnet-and-windows-cluster.md)で設定した VNET、サブネット、および NSG の名前を使用するように API Management Resource Manager テンプレートが事前に構成されています。この記事では、API Management および Service Fabric が同じ仮想ネットワークのサブネット内にある Azure に次のトポロジをデプロイします。

 ![キャプション][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure にサインインしてサブスクリプションを選択する

Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Service Fabric のバックエンド サービスのデプロイ

Service Fabric バックエンド サービスにトラフィックがルーティングされるよう API Management を構成する前に、まずは要求を受け入れる実行中のサービスが必要です。  

既定の Web API プロジェクト テンプレートを使用して、ASP.NET Core の基本的なステートレス リライアブル サービスを作成します。 これにより、サービスの HTTP エンドポイントが作成され、Azure API Management を通じて公開することができます。

Visual Studio を管理者として起動し、ASP.NET Core サービスを作成します。

 1. Visual Studio で、[ファイル] の [新しいプロジェクト] を選択します。
 2. [クラウド] の下にある Service Fabric アプリケーション テンプレートを選択し、 **"ApiApplication"** という名前を付けます。
 3. ステートレス ASP.NET Core サービス テンプレートを選択し、プロジェクトに **"WebApiService"** という名前を付けます。
 4. Web API ASP.NET Core 2.1 プロジェクト テンプレートを選択します。
 5. プロジェクトが作成されたら、`PackageRoot\ServiceManifest.xml` を開いて、エンドポイント リソースの構成から `Port` 属性を削除します。

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    ポートを削除すると、Service Fabric がアプリケーションのポート範囲 (クラスターの Resource Manager テンプレートのネットワーク セキュリティ グループを通じて開かれたもの) から動的にポートを指定できるようになり、そこに API Management からのトラフィックが流れるようになります。

 6. Visual Studio で F5 キーを押して、Web API がローカルで使用できることを確認します。

    Service Fabric Explorer を開き、ASP.NET Core サービスの特定のインスタンスまでドリルダウンして、サービスがリッスンするベース アドレスを表示します。 `/api/values` をベース アドレスに追加し、ブラウザーで開くと、Web API テンプレートの ValuesController で Get メソッドが呼び出されます。 このメソッドは、テンプレートで指定される既定の応答として、2 つの文字列を格納する JSON 配列を返します。

    ```json
    ["value1", "value2"]`
    ```

    これが、Azure の API Management を通じて公開するエンドポイントになります。

 7. 最後に、アプリケーションを Azure のクラスターにデプロイします。 Visual Studio で、Application プロジェクトを右クリックして **[公開]** を選択します。 クラスター エンドポイント (`mycluster.southcentralus.cloudapp.azure.com:19000` など) を指定して、アプリケーションを Azure の Service Fabric クラスターにデプロイします。

`fabric:/ApiApplication/WebApiService` という名前の ASP.NET Core ステートレス サービスが、Azure の Service Fabric クラスターで実行されているはずです。

## <a name="download-and-understand-the-resource-manager-templates"></a>Resource Manager テンプレートのダウンロードと理解

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードして保存します。

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

*network-apim.json* テンプレートを使用すると、Service Fabric クラスターがデプロイされている仮想ネットワーク内に新しいサブネットとネットワーク セキュリティ グループをデプロイできます。

次のセクションでは、*apim.json* テンプレートによって定義されるリソースについて説明します。 詳細については、各セクション内のリンク先のテンプレート リファレンス ドキュメントを参照してください。 *apim.parameters.json* パラメーター ファイルで定義されている構成可能なパラメーターは、この記事の後半で設定します。

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) は、API Management サービス インスタンス (名前、SKU または階層、リソース グループの場所、パブリッシャー情報、および仮想ネットワーク) を記述します。

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) は、API Management セキュリティを構成します。 API Management は、Service Fabric クラスターへのアクセス権を持つクライアント証明書を使用して、サービス検出の際にそのクラスターで認証する必要があります。 この記事では、[Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor)を作成したときに指定した証明書と同じものを使用します。この証明書は、クラスターにアクセスするときに既定で使用できます。

この記事では、クライアント認証とクラスター ノード間のセキュリティに同じ証明書を使用します。 Service Fabric クラスターにアクセスするよう構成した別のクライアント証明書がある場合は、その証明書を使用してもかまいません。 Service Fabric クラスターを作成したときに指定したクラスター証明書の秘密キー ファイル (.pfx) の **名前**、**パスワード**、および **データ** (Base 64 エンコード文字列) を指定します。

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) は、トラフィックが転送されるバックエンド サービスを記述します。

Service Fabric のバックエンドの場合は、特定の Service Fabric サービスではなく、Service Fabric クラスターがバックエンドとなります。 これにより、単一のポリシーでクラスター内の複数のサービスにルーティングすることができます。 この **url** フィールドはクラスター内のサービスの完全修飾サービス名であり、バックエンド ポリシーにサービス名が指定されていない場合に、既定ですべての要求がこのサービスにルーティングされます。 フォールバック サービスが不要の場合は、"fabric:/fake/service" などの仮のサービス名を使用できます。 **resourceId** は、クラスター管理エンドポイントを指定します。  **clientCertificateThumbprint** と **serverCertificateThumbprints** は、クラスターでの認証に使用される証明書を識別します。

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) は製品を作成します。 Azure API Management の製品には、少なくとも 1 つの API に加え、使用量クォータや使用条件が含まれます。 製品が発行されると、開発者は成果物をサブスクライブして、成果物の API の利用を開始できます。

製品のわかりやすい **displayName** と **description** を入力します。 この記事では、サブスクリプションは必須ですが、管理者によるサブスクリプションの承認は必須ではありません。  この製品の **state** が "公開" され、サブスクライバーに表示します。

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) は API を作成します。 API Management における API は、クライアント アプリケーションから呼び出すことのできる一連の操作を表します。 操作を追加し、API を成果物に追加すると、API を発行できます。 API が発行されると、開発者はそれをサブスクライブして使用できます。

* **displayName** には、API の任意の名前を指定できます。 この記事では、「Service Fabric App」を使用します。
* **name** は、"service-fabric-app" などの API に対する一意のわかりやすい名前です。 開発者ポータルとパブリッシャー ポータルには、この名前が表示されます。
* **serviceUrl** は、API が実装されている HTTP サービスを参照します。 要求は、API Management によってこのアドレスに転送されます。 Service Fabric のバックエンドの場合、この URL の値は使用されません。 任意の値を入力できます。 この記事では、たとえば "http:\//servicefabric" などです。
* **path** が API Management サービスのベース URL に付加されます。 ベース URL は、API Management サービス インスタンスによってホストされるすべての API に共通です。 API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。
* **protocols** により、API へのアクセスに使用できるプロトコルが決まります。 この記事では、**http** と **https** を指定します。
* **path** は API のサフィックスです。 この記事では、「myapp」を使用します。

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) では、API Management でAPI を使用する前に、操作をその API に追加する必要があります。  外部クライアントでは、操作を使用して、Service Fabric クラスターで実行中の ASP.NET Core ステートレス サービスと通信します。

フロントエンド API 操作を追加するには、値を入力します。

* **displayName** と **description** で操作を説明します。 この記事では、「Values」を使用します。
* **method** は HTTP 動詞を指定します。  この記事では、**GET** を指定します。
* **urlTemplate** は、API のベース URL に付加され、単一の HTTP 操作を識別します。  この記事では、`/api/values` (.NET バックエンド サービスを追加した場合) または `getMessage` (Java バックエンド サービスを追加した場合) を使用します。  既定では、ここで指定した URL パスが、バックエンドの Service Fabric サービスに送信される URL パスになります。 サービスで使用するのと同じ URL パス ("/api/values" など) を指定すると、何も変更しなくても操作が正常に機能します。 バックエンドの Service Fabric サービスで使用するのとは異なる URL パスを指定することもできますが、その場合は後ほど操作ポリシー内でパスの再生成を指定する必要があります。

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) は、すべてを 1 つにまとめるバックエンド ポリシーを作成します。 このポリシーを使って、要求のルーティング先となるバックエンドの Service Fabric サービスを構成します。 このポリシーは任意の API 操作に適用できます。  詳細については、[ポリシーの概要](../api-management/api-management-howto-policies.md)に関するページをご覧ください。

[Service Fabric のバックエンド構成](../api-management/api-management-transformation-policies.md#SetBackendService)では、次の要求ルーティングをコントロールできます。

* サービス インスタンスの選択。Service Fabric サービス インスタンス名として、ハードコートされた名前 (`"fabric:/myapp/myservice"` など) か、HTTP 要求から生成される名前 (`"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]` など) のいずれかを指定します。
* パーティションの解決。任意の Service Fabric パーティション構成を使用して、パーティション キーを生成します。
* ステートフル サービスのレプリカの選択。
* 解決の再試行の条件。サービスの場所を再解決して要求を再送信するための条件を指定できます。

**policyContent** は、ポリシーの JSON エスケープ XML コンテンツです。  この記事では、前にデプロイした .NET または Java ステートレス サービスに直接要求をルーティングするバックエンド ポリシーを作成します。 受信ポリシーの下に `set-backend-service` ポリシーを追加します。  *sf-service-instance-name* の値を `fabric:/ApiApplication/WebApiService` (前に .NET バックエンド サービスをデプロイした場合) または `fabric:/EchoServerApplication/EchoServerService` (Java サービスをデプロイした場合) に置き換えます。  *backend-id* は、バックエンド リソース (この場合は、*apim.json* テンプレートで定義されている `Microsoft.ApiManagement/service/backends` リソース) を参照します。 *backend-id* を使用して、API Management API を使用して作成された別のバックエンド リソースを参照することもできます。 この記事では、*backend-id* を *service_fabric_backend_name* パラメーターの値に設定します。

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Service Fabric バックエンド ポリシーの全属性については、[API Management バックエンドに関するドキュメント](../api-management/api-management-transformation-policies.md#SetBackendService)を参照してください。

## <a name="set-parameters-and-deploy-api-management"></a>パラメーターの設定と API Management のデプロイ

次のように、デプロイ用の *apim.parameters.json* で空のパラメーターに値を入力します。

|パラメーター|値|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 エンコード文字列&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;XML 文字列&gt;|

*certificatePassword* と *serviceFabricCertificateThumbprint* は、クラスターの設定に使用するクラスター証明書と一致する必要があります。

*serviceFabricCertificate* は base-64 エンコード文字列としての証明書で、次のスクリプトを使用して生成できます。

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

*inbound_policy* で、*sf-service-instance-name* の値を `fabric:/ApiApplication/WebApiService` (前に .NET バックエンド サービスをデプロイした場合) または `fabric:/EchoServerApplication/EchoServerService` (Java サービスをデプロイした場合) に置き換えます。 *backend-id* は、バックエンド リソース (この場合は、*apim.json* テンプレートで定義されている `Microsoft.ApiManagement/service/backends` リソース) を参照します。 *backend-id* を使用して、API Management API を使用して作成された別のバックエンド リソースを参照することもできます。 この記事では、*backend-id* を *service_fabric_backend_name* パラメーターの値に設定します。

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

次のスクリプトを使用して、API Management 用の Resource Manager テンプレートとパラメーター ファイルをデプロイします。

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az deployment group create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az deployment group create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>テストする

これで、[Azure Portal](https://portal.azure.com) から直接、API Management を通じて Service Fabric のバックエンド サービスに要求を送信できるようになりました。

 1. API Management サービスで、 **[API]** を選択します。
 2. 前の手順で作成した **Service Fabric App** API で、 **[テスト]** タブ、 **[Values]** 操作の順に選択します。
 3. **[送信]** ボタンをクリックして、バックエンド サービスにテスト要求を送信します。  次のような HTTP 応答が表示されます。

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にサインインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](https://portal.azure.com) にログインして確認できます。 リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用します。

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>次のステップ

[API Management](../api-management/import-and-publish.md) の使用方法の詳細を確認する

また、[Azure portal](../api-management/how-to-configure-service-fabric-backend.md) を使用して、API Management の Service Fabric バックエンドを作成および管理することもできます。

[azure-powershell]: /powershell/azure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png