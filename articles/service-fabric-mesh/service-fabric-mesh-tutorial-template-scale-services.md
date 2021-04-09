---
title: チュートリアル - Azure Service Fabric Mesh で実行されているアプリをスケーリングする
description: このチュートリアルでは、Service Fabric Mesh で実行されているアプリケーション内でサービスをスケーリングする方法について説明します。
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02dc5d43a23c572d441da2bbb7386885bf66ece7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625385"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>チュートリアル:Service Fabric Mesh で実行しているアプリケーションをスケーリングする

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

このチュートリアルは、シリーズの第 2 部です。 [前に Service Fabric Mesh にデプロイ](service-fabric-mesh-tutorial-template-deploy-app.md)されていたアプリケーションのサービス インスタンスの数を手動でスケーリングする方法について説明します。 最終的には、フロントエンド サービスで 3 つのインスタンスが、またデータ サービスで 2 つのインスタンスが実行されている状態になります。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * 必要な数のサービス インスタンスを構成する
> * アップグレードする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [テンプレートを使用して Azure Service Fabric Mesh にアプリケーションをデプロイする](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Service Fabric Mesh で実行しているアプリケーションをスケーリングする
> * [Service Fabric Mesh で実行されているアプリケーションをアップグレードする](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [アプリケーションの削除](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* [Azure CLI と Service Fabric Mesh CLI をローカルにインストールします](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)。

## <a name="manually-scale-your-services-in-or-out"></a>サービスを手動でスケールイン/スケールアウトする

Service Fabric Mesh にアプリケーションをデプロイすることの主な利点の 1 つは、サービスを簡単にスケールイン/スケールアウトできることです。これはサービスに対する負荷量の変化に対処するためや可用性を上げるために使用します。

このチュートリアルでは、To Do List サンプルを例として使用します。このサンプルは[前にデプロイ](service-fabric-mesh-tutorial-template-deploy-app.md)したものであり、現在、実行されているはずです。 このアプリケーションには、WebFrontEnd と ToDoService という 2 つのサービスがあります。 各サービスは最初にレプリカ数 1 でデプロイされました。  WebFrontEnd サービスで実行中のレプリカ数を表示するには、次を実行します。

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

ToDoService サービスで実行中のレプリカ数を表示するには、次を実行します。

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

アプリケーション リソースのデプロイ テンプレートでは、各サービスに *replicaCount* プロパティが与えられます。このプロパティを利用し、そのサービスをデプロイする回数を設定できます。 1 つのアプリケーションは、まとめてデプロイされ、管理される複数サービスで構成できます。各サービスに一意の *replicaCount* 番号が与えられます。 サービス レプリカの数をスケーリングするために、スケーリングするサービスごとにデプロイ テンプレートまたはパラメーター ファイルの *replicaCount* 値を変更できます。  次に、アプリケーションをアップグレードします。

### <a name="modify-the-deployment-template-parameters"></a>デプロイ テンプレートのパラメーターを変更する

アプリケーションのデプロイ後に変更が予想される値がテンプレートにある場合、またはデプロイごとにオプションを変更する場合 (他のデプロイにこのテンプレートを再利用する場合)、ベスト プラクティスは値をパラメーター化することです。

以前は、アプリケーションは [mesh_rp.windows.json デプロイ テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) ファイルと [mesh_rp.windows.parameter.json パラメーター](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) ファイルを利用してデプロイされました。

[mesh_rp.windows.parameter.json パラメーター](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) ファイルをローカルで開き、*frontEndReplicaCount* 値を 3 に設定し、*serviceReplicaCount* 値を 2 に設定します。

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

変更内容をパラメーター ファイルに保存します。  *frontEndReplicaCount* パラメーターと *serviceReplicaCount* パラメーターは [mesh_rp.windows.json デプロイ テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)の *[パラメーター]* セクションで宣言されます。

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

WebFrontEnd サービスの *replicaCount* プロパティは *frontEndReplicaCount* パラメーターを参照し、ToDoService サービスの *replicaCount* プロパティは *serviceReplicaCount* パラメーターを参照します。

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

テンプレートが変更されたら、アプリケーションをアップグレードします。

### <a name="upgrade-your-application"></a>アプリケーションをアップグレードする

アプリケーションの実行中、テンプレートと更新後のパラメーター ファイルを再デプロイすることでアプリケーションをアップグレードできます。

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

これでアプリケーションのローリング アップグレードが開始されます。数分後にサービス インスタンスの増加が確認できるはずです。  WebFrontEnd サービスで実行中のレプリカ数を表示するには、次を実行します。

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

ToDoService サービスで実行中のレプリカ数を表示するには、次を実行します。

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * 必要な数のサービス インスタンスを構成する
> * アップグレードする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric Mesh で実行されているアプリケーションをアップグレードする](service-fabric-mesh-tutorial-template-upgrade-app.md)
