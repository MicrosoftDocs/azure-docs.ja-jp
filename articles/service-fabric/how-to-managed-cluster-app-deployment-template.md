---
title: ARM テンプレートを使用して Service Fabric マネージド クラスター (プレビュー) アプリケーションをデプロイする
description: Azure Resource Manager テンプレートを使用して、アプリケーションを Azure Service Fabric マネージド クラスター (プレビュー) にデプロイします。
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b2d73180de61f44850b9c52a7ac24b0c23c36db2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100641009"
---
# <a name="deploy-a-service-fabric-managed-cluster-preview-application-using-arm-template"></a>ARM テンプレートを使用して Service Fabric マネージド クラスター (プレビュー) アプリケーションをデプロイする

Azure Service Fabric アプリケーションを Service Fabric マネージド クラスターにデプロイするには、複数の選択肢があります。 Azure Resource Manager を使用することをお勧めします。 Resource Manager を使用すると、アプリケーションとサービスを JSON で記述し、クラスターと同じ Resource Manager テンプレートにデプロイすることができます。 PowerShell や Azure CLI を使ってアプリケーションをデプロイして管理する場合と違い、Resource Manager を使用する場合は、クラスターの準備が整うまで待つ必要はありません。アプリケーションの登録、プロビジョニング、およびデプロイをすべて 1 回の手順で実行できます。 クラスターでアプリケーションのライフ サイクルを管理するには、Resource Manager を使用するのが最良の方法です。 詳細については、「[ベスト プラクティス: コードとしてのインフラストラクチャ](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)」を参照してください。

Resource Manager でアプリケーションをリソースとして管理すると、次のような分野での改善に役立ちます。

* 監査証跡:Resource Manager は、すべての操作を監査し、詳細なアクティビティ ログを保持します。 アクティビティ ログは、アプリケーションとクラスターに対して行われたすべての変更を追跡するのに役立ちます。
* ロールベースのアクセス制御:同じ Resource Manager テンプレートを使用して、クラスターと、そのクラスターにデプロイされたアプリケーションへのアクセスを管理できます。
* 管理効率: Resource Manager を使用すると、クラスターと重要なアプリケーションのデプロイをすべて 1 か所 (Azure portal) で管理できます。

このドキュメントでは、以下の方法について説明します。

> [!div class="checklist"]
>
> * Resource Manager を使用したアプリケーション リソースのデプロイ。
> * Resource Manager を使用したアプリケーション リソースのアップグレード。
> * アプリケーション リソースの削除。

## <a name="deploy-application-resources"></a>アプリケーション リソースのデプロイ

Resource Manager アプリケーション リソース モデルを使用してアプリケーションとそのサービスをデプロイする手順の概要は次のとおりです。
1. アプリケーション コードをパッケージ化します。
1. パッケージをアップロードします。
1. Resource Manager テンプレート内のパッケージの場所をアプリケーション リソースとして参照します。 

詳細については、[アプリケーションのパッケージ化](service-fabric-package-apps.md#create-an-sfpkg)に関する項目を参照してください。

次に、Resource Manager テンプレートを作成し、アプリケーションの詳細情報でパラメーター ファイルを更新し、そのテンプレートを Service Fabric クラスターにデプロイします。 [サンプルを参照してください](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster)。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Resource Manager テンプレートからアプリケーションをデプロイするには、ストレージ アカウントが必要です。 ストレージ アカウントは、アプリケーション イメージをステージングするために使用されます。 

既存のストレージ アカウントを再利用することも、アプリケーションをステージングするための新しいストレージ アカウントを作成することもできます。 既存のストレージ アカウントを使用する場合は、この手順を省略できます。 

![ストレージ アカウントの作成][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>ストレージ アカウントの構成

ストレージ アカウントが作成されたら、アプリケーションをステージングできる BLOB コンテナーを作成します。 Azure portal で、アプリケーションを格納する Azure Storage アカウントに移動します。 **[BLOB]**  >  **[コンテナーの追加]** の順に選択します。 

クラスター内のリソースは、パブリック アクセス レベルを **[プライベート]** に設定することで、セキュリティで保護することができます。 アクセス権は、次の複数の方法で付与できます。

* [Azure Active Directory](../storage/common/storage-auth-aad-app.md) を使用して BLOB とキューへのアクセスを承認する。
* [Azure portal で Azure RBAC](../storage/common/storage-auth-aad-rbac-portal.md) を使用して Azure BLOB とキューのデータへのアクセスを付与する。
* [Shared Access Signature](/rest/api/storageservices/delegate-access-with-shared-access-signature) を使用してアクセスを委任する。

次のスクリーンショットの例では、BLOB の匿名読み取りアクセスを使用しています。

![BLOB の作成][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>ストレージ アカウントでアプリケーションをステージングする

アプリケーションをデプロイするには、事前に BLOB ストレージにアプリケーションをステージングする必要があります。 このチュートリアルでは、アプリケーション パッケージを手動で作成します。 この手順は自動化できることに注意してください。 詳細については、[アプリケーションのパッケージ化](service-fabric-package-apps.md#create-an-sfpkg)に関する記事を参照してください。 

このチュートリアルでは、[Voting サンプル アプリケーション](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)を使用します。

1. Visual Studio で **[Voting]** プロジェクトを右クリックし、 **[Package]\(パッケージ\)** を選択します。

   ![アプリケーションのパッケージ化][PackageApplication]  
1. *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug* ディレクトリに移動します。 内容を zip 形式で *Voting.zip* という名前のファイルに圧縮します。 *ApplicationManifest.xml* ファイルが zip ファイルのルートにあります。

   ![アプリケーションの zip 圧縮][ZipApplication]  
1. ファイルの名前を変更し、拡張子を .zip から *.sfpkg* に変更します。

1. Azure portal で、お使いのストレージ アカウントの **apps** コンテナーで **[アップロード]** を選択して **Voting.sfpkg** をアップロードします。 

   ![アプリ パッケージのアップロード][UploadAppPkg]

これで、アプリケーションがステージングされ、Resource Manager テンプレートを作成してアプリケーションをデプロイできるようになりました。

### <a name="create-the-resource-manager-template"></a>Resource Manager テンプレートの作成

サンプル アプリケーションには、アプリケーションのデプロイに使用できる [Azure Resource Manager テンプレート](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)が含まれています。 テンプレート ファイルの名前は、*UserApp.json* と *UserApp.Parameters.json* です。

> [!NOTE]
> *UserApp.Parameters.json* ファイルを、クラスターの名前で更新する必要があります。
>
>

| パラメーター              | 説明                                 | 例                                                      | 説明                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | デプロイ先クラスターの名前 | sf-cluster123                                                |                                                              |
| application            | アプリケーションの名前                 | Voting                                                       |
| version    | アプリのリソース ID、アプリケーションの種類、バージョン。       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | ApplicationManifest.xml と一致する必要があります                 |              |
| serviceName            | サービスの名前         | VotingWeb                                             | ServiceType の形式でなければなりません            |
| serviceTypeName        | サービスの種類名                | VotingWebType                                                    | ServiceManifest.xml と一致する必要があります                 |
| appPackageUrl          | アプリケーションの BLOB ストレージ URL     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | BLOB ストレージ内のアプリケーション パッケージの URL (URL を設定する手順については、この記事の後半で説明します) |

```json
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>アプリケーションの配置

**New-AzResourceGroupDeployment** コマンドレットを実行して、クラスターを含むリソース グループにアプリケーションをデプロイします。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Resource Manager を使用した Service Fabric アプリケーションのアップグレード

> [!IMPORTANT]
> ARM JSON 定義を使用してデプロイされているサービスはすべて、対応する ApplicationManifest.xml ファイルの DefaultServices セクションから削除する必要があります。


次のいずれかの理由により、Service Fabric クラスターに既にデプロイされているアプリケーションをアップグレードする場合があります。

* 新しいサービスがアプリケーションに追加されます。 サービス定義は、サービスがアプリケーションに追加されるときに *service-manifest.xml* と *application-manifest.xml* ファイルに追加する必要があります。 アプリケーションの新しいバージョンを反映するために、[UserApp.parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json) でアプリケーションの種類のバージョンを 1.0.0 から 1.0.1 に変更する必要もあります。

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* 既存のサービスの新しいバージョンがアプリケーションに追加されます。 例としては、アプリケーション コードの変更や、アプリの種類のバージョンと名前の更新などがあります。 このアップグレードでは、次のように UserApp.Parameters.json を更新します。

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-application-resources"></a>アプリケーション リソースの削除

Resource Manager でアプリケーション リソース モデルを使用してデプロイされたアプリケーションを削除するには、次のようにします。

1. [Get-AzResource](/powershell/module/az.resources/get-azresource) コマンドレットを使用して、アプリケーションのリソース ID を取得します。

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. [Remove-AzResource](/powershell/module/az.resources/remove-azresource) コマンドレットを使用して、アプリケーション リソースを削除します。

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>次のステップ

アプリケーション リソース モデルに関する情報を入手します。

* [Service Fabric のアプリケーションのモデル化](service-fabric-application-model.md)
* [Service Fabric のアプリケーション マニフェストとサービス マニフェスト](service-fabric-application-and-service-manifests.md)
* [ベスト プラクティス: コードとしてのインフラストラクチャ](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [アプリケーションとサービスを Azure リソースとして管理する](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png