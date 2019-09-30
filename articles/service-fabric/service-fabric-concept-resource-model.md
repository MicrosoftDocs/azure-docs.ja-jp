---
title: Azure Service Fabric アプリケーション リソース モデル | Microsoft Docs
description: この記事では、Azure Resource Manager を使用した Azure Service Fabric アプリケーションの管理の概要を説明します
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: atsenthi
ms.openlocfilehash: 36c0f02202c738ac96d26b748b741cd8eee27380
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241819"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>Service Fabric アプリケーション リソース モデルとは
Azure Resource Manager を使用して、Service Fabric クラスターに Service Fabric アプリケーションをデプロイすることをお勧めします。 この方法を使用すると、アプリケーションとサービスを JSON で記述し、クラスターと同じ Resource Manager テンプレートにデプロイすることができます。 PowerShell または Azure CLI を使用してアプリケーションをデプロイおよび管理する場合とは異なり、クラスターの準備が整うまで待つ必要はありません。 アプリケーションの登録、プロビジョニング、デプロイのプロセスを、すべて 1 ステップで実行できます。 これは、クラスターでアプリケーションのライフサイクルを管理するためのベスト プラクティスです。 詳細については、[ベスト プラクティス](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources)に関する項目を参照してください。

該当する場合は、アプリケーションを Resource Manager のリソースとして管理することで次のメリットが得られます。
* 監査証跡:Resource Manager はすべての操作を監査し、詳細な*アクティビティ ログ* を保存します。このログは、アプリケーションとクラスターに加えられたすべての変更を追跡するのに役立ちます。
* ロールベースのアクセス制御:同じ Resource Manager テンプレートを使用して、クラスターと、そのクラスターにデプロイされたアプリケーションへのアクセスを管理できます。
* Azure Resource Manager (Azure portal 経由) を使用して、クラスターと重要なアプリケーションのデプロイをすべて 1 か所で管理できます。

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Azure Resource Manager を使用した Service Fabric アプリケーションのライフ サイクル 
このドキュメントでは、以下の方法について説明します。

> [!div class="checklist"]
> * Azure Resource Manager を使用したアプリケーション リソースのデプロイ 
> * Azure Resource Manager を使用したアプリケーション リソースのアップグレード
> * アプリケーション リソースの削除

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Azure Resource Manager を使用したアプリケーション リソースのデプロイ  
Azure Resource Manager アプリケーション リソースモデルを使用してアプリケーションとそのサービスをデプロイするには、アプリケーション コードをパッケージ化し、パッケージをアップロードしてから、Azure Resource Manager テンプレートでアプリケーション リソースとしてパッケージの場所を参照する必要があります。 詳細については、[アプリケーションのパッケージ化](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)に関する項目を参照してください。
          
次に、Azure Resource Manager テンプレートを作成し、アプリケーションの詳細でパラメーター ファイルを更新して、Service Fabric クラスターにデプロイします。 [こちら](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)のサンプルを参照してください。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成 
Resource Manager テンプレートからアプリケーションをデプロイするには、アプリケーション イメージをステージングするためのストレージ アカウントが必要です。 既存のストレージ アカウントを再利用するか、新しいストレージ アカウントを作成してアプリケーションをステージングすることができます。 既存のストレージ アカウントを使用する場合は、このステップを省略できます。 

![ストレージ アカウントの作成][CreateStorageAccount]

### <a name="configure-storage-account"></a>ストレージ アカウントの構成 
ストレージ アカウントが作成されたら、アプリケーションをステージングできる BLOB コンテナーを作成する必要があります。 Azure portal で、アプリケーションを格納するストレージ アカウントに移動します。 **[BLOB]** ブレードを選択し、 **[コンテナーの追加]** ボタンをクリックします。 [Blob Public] (BLOB パブリック) アクセス レベルの新しいコンテナーを追加します。
   
![BLOB の作成][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>ストレージ アカウントでアプリケーションをステージングする
アプリケーションをデプロイする前に、アプリケーションを BLOB ストレージにステージングする必要があります。 このチュートリアルではアプリケーション パッケージを手動で作成しますが、この手順は自動化できます。  詳細については、[アプリケーションのパッケージ化](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)に関する項目を参照してください。 以下の手順では、[Voting サンプル アプリケーション](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)を使用します。

1. Visual Studio で Voting プロジェクトを右クリックし、パッケージを選択します。   
![アプリケーションのパッケージ化][PackageApplication]  
2. 作成したばかりの **.\service-fabric-dotnet-quickstart\Voting\pkg\Debug** ディレクトリを開き、**Voting.zip** という名前のファイルに内容を zip 圧縮します。このとき、ApplicationManifest.xml を zip ファイルのルートに配置します。  
![アプリケーションの圧縮][ZipApplication]  
3. ファイルの拡張子を .zip から **.sfpkg** に変更します。
4. Azure portal で、ストレージ アカウントの **apps** コンテナーで **[アップロード]** をクリックして **Voting.sfpkg** をアップロードします。  
![アプリ パッケージのアップロード][UploadAppPkg]

これで、アプリケーションがステージングされました。 以上で、Azure Resource Manager テンプレートを作成してアプリケーションをデプロイする準備が整いました。      
   
### <a name="create-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成
サンプル アプリケーションには、アプリケーションのデプロイに使用できる [Azure Resource Manager テンプレート](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)が含まれています。 テンプレート ファイルの名前は **UserApp.json** と **UserApp.Parameters.json** です。 

> [!NOTE] 
> **UserApp.Parameters.json** ファイルを、クラスターの名前で更新する必要があります。
>
>

| パラメーター              | 説明                                 | 例                                                      | 説明                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | デプロイ先クラスターの名前 | sf-cluster123                                                |                                                              |
| application            | アプリケーションの名前                 | Voting                                                       |
| applicationTypeName    | アプリケーションの種類名           | VotingType                                                   | ApplicationManifest.xml の内容と一致する必要があります                 |
| applicationTypeVersion | アプリケーションの種類のバージョン         | 1.0.0                                                        | ApplicationManifest.xml の内容と一致する必要があります                 |
| serviceName            | サービスの名前         | Voting~VotingWeb                                             | ApplicationName~ServiceType の形式にする必要があります            |
| serviceTypeName        | サービスの種類名                | VotingWeb                                                    | ServiceManifest.xml の内容と一致する必要があります                 |
| appPackageUrl          | アプリケーションの BLOB ストレージ URL     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | BLOB ストレージ内のアプリケーション パッケージの URL (これを設定する手順は以下で説明します) |
       
```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>アプリケーションのデプロイ 
アプリケーションをデプロイするには、New-AzResourceGroupDeployment を実行して、クラスターを含むリソース グループにデプロイします。
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Azure Resource Manager を使用した Service Fabric アプリケーションのアップグレード
以下の理由により、Service Fabric クラスターに既にデプロイされているアプリケーションがアップグレードされます。

1. 新しいサービスがアプリケーションに追加されます。 service-manifest.xml および application-manifest.xml ファイルにサービス定義を追加する必要があります。 次に、アプリケーションの新しいバージョンを反映するために、[UserApp.parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json) でアプリケーションの種類のバージョンを 1.0.0 から 1.0.1 に更新する必要があります。

    ```
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```
2. 既存のサービスの新しいバージョンがアプリケーションに追加されます。 これは、アプリケーション コードの変更と、アプリの種類のバージョンと名前の更新を伴います。

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>アプリケーション リソースの削除
Azure Resource Manager のアプリケーション リソース モデルを使用してデプロイされたアプリケーションは、以下の手順を使用してクラスターから削除できます。

1) [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) コマンドを使用してアプリケーションのリソース ID を取得する  

    #### <a name="get-resource-id-for-application"></a>アプリケーションのリソース ID を取得する
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) を使用してアプリケーション リソースを削除する  

    #### <a name="delete-application-resource-using-its-resource-id"></a>リソース ID を使用してアプリケーション リソースを削除する
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>次の手順
アプリケーション リソース モデルに関する情報を入手します。

* [Service Fabric のアプリケーションのモデル化](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric のアプリケーション マニフェストとサービス マニフェスト](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>関連項目
* [ベスト プラクティス](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [アプリケーションとサービスを Azure リソースとして管理する](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
