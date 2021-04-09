---
title: チュートリアル - Azure Service Fabric Mesh で実行されているアプリをアップグレードする
description: このチュートリアルでは、Service Fabric Mesh で実行されている Service Fabric アプリケーションをアップグレードする方法について説明します。
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 8a71e854f03bee75b757e0a0aa02e7aa2c24469b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626563"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>チュートリアル: Service Fabric Mesh で実行されている Service Fabric アプリケーションをアップグレードする

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

このチュートリアルは、シリーズの第 3 部です。 割り当てられている CPU リソースを増やすことによって、[以前に Service Fabric Mesh にデプロイ](service-fabric-mesh-tutorial-template-deploy-app.md)された Service Fabric アプリケーションをアップグレードする方法について学習します。  完了すると、Web フロントエンド サービスがより多くの CPU リソースで実行するようになります。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの構成を変更する
> * Service Fabric Mesh で実行しているアプリケーションをアップグレードする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [テンプレートを使用して Azure Service Fabric Mesh にアプリケーションをデプロイする](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Service Fabric Mesh で実行しているアプリケーションをスケーリングする](service-fabric-mesh-tutorial-template-scale-services.md)
> * Service Fabric Mesh で実行しているアプリケーションをアップグレードする
> * [アプリケーションの削除](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) を開くか、または[Azure CLI と Service Fabric Mesh CLI をローカルにインストール](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)します。

## <a name="upgrade-application-configurations"></a>アプリケーションの構成をアップグレードする

Service Fabric Mesh にアプリケーションをデプロイすることの主な利点の 1 つは、アプリケーションの構成を簡単に更新できることです。  たとえば、サービスの CPU やメモリ リソースです。

このチュートリアルでは、To Do List サンプルを例として使用します。このサンプルは[前にデプロイ](service-fabric-mesh-tutorial-template-deploy-app.md)したものであり、現在、実行されているはずです。 このアプリケーションには、WebFrontEnd と ToDoService という 2 つのサービスがあります。 各サービスは最初に CPU リソースを 0.5 にしてデプロイされました。  WebFrontEnd サービスの CPU リソースを表示するには、次を実行します。

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

アプリケーション リソースのデプロイ テンプレートでは、各サービスに *cpu* プロパティがあり、それを使用して、要求された CPU リソースを設定できます。 1 つのアプリケーションを、それぞれが固有の *cpu* 設定を持ち、まとめてデプロイおよび管理される、複数のサービスで構成することができます。 Web フロントエンド サービスの CPU リソースを増やすには、デプロイ テンプレートまたはパラメーター ファイルで *cpu* の値を変更します。  次に、アプリケーションをアップグレードします。

### <a name="modify-the-deployment-template-parameters"></a>デプロイ テンプレートのパラメーターを変更する

アプリケーションのデプロイ後に変更が予想される値がテンプレートにある場合、またはデプロイごとにオプションを変更する場合 (他のデプロイにこのテンプレートを再利用する場合)、ベスト プラクティスは値をパラメーター化することです。

以前は、アプリケーションは [mesh_rp.windows.json デプロイ テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) ファイルと [mesh_rp.windows.parameter.json パラメーター](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) ファイルを利用してデプロイされました。

[mesh_rp.windows.parameter.json パラメーター](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) ファイルをローカルで開き、*frontEndCpu* の値を 1 に設定します。

```json
      "frontEndCpu":{
        "value": "1"
      }
```

変更内容をパラメーター ファイルに保存します。  

*frontEndCpu* パラメーターは、[mesh_rp.windows.json デプロイ テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)の *parameters* セクションで宣言されています。

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

WebFrontEnd サービスの *codePackages->resources->requests->cpu* プロパティでは、*frontEndCpu* パラメーターが参照されています。

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>アプリケーションをアップグレードする

アプリケーションの実行中、テンプレートと更新後のパラメーター ファイルを再デプロイすることでアプリケーションをアップグレードできます。

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

これでアプリケーションのローリング アップグレードが開始されます。数分後に CPU リソースの増加を確認できるはずです。  WebFrontEnd サービスの CPU リソースを表示するには、次を実行します。

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの構成を変更する
> * Service Fabric Mesh で実行しているアプリケーションをアップグレードする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric Mesh アプリケーションを削除する](service-fabric-mesh-tutorial-template-remove-app.md)
