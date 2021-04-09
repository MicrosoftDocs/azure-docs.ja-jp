---
title: チュートリアル - Azure Service Fabric Mesh にアプリをデプロイする
description: このチュートリアルでは、テンプレートを使用して Service Fabric Mesh にアプリケーションをデプロイする方法について説明します。
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 589e881eb48daf7da9cd2a934b14acfcc76dc5f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625418"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>チュートリアル: テンプレートを使用して Service Fabric Mesh にアプリケーションをデプロイする

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

このチュートリアルは、シリーズの第 1 部です。 テンプレートを使用して Azure Service Fabric Mesh アプリケーションをデプロイする方法について説明します。  アプリケーションは ASP.NET Web フロントエンド サービスと ASP.NET Core Web API バックエンド サービスで構成されており、これらは Docker Hub にあります。  2 つのコンテナー イメージを Docker Hub からプルして、独自のプライベート レジストリにプッシュします。 その後、アプリケーション用の Azure RM テンプレートを作成し、コンテナー レジストリから Service Fabric Mesh にアプリケーションをデプロイします。 完了すると、Service Fabric Mesh で実行される簡単な To Do List アプリケーションができあがります。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * プライベート Azure Container Registry インスタンスを作成する
> * コンテナー イメージをレジストリにプッシュする
> * RM テンプレート ファイルとパラメーター ファイルを作成する
> * Azure Service Fabric Mesh にアプリケーションをデプロイする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して Azure Service Fabric Mesh にアプリケーションをデプロイする
> * [Service Fabric Mesh で実行されているアプリケーション内のサービスをスケーリングする](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Service Fabric Mesh で実行されているアプリケーションをアップグレードする](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [アプリケーションの削除](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* [Docker のインストール](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Azure CLI と Service Fabric Mesh CLI をローカルにインストールします](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

Service Fabric Mesh アプリケーション内のサービスに関連付けられているコンテナー イメージを、コンテナー レジストリに格納する必要があります。  このチュートリアルでは、プライベートな Azure Container Registry (ACR) インスタンスを使用します。 

次の手順のようにして、ACR のインスタンスを作成します。  ACR のインスタンスを既にセットアップしてある場合は、省略して先に進んでかまいません。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure にサインインしてアクティブなサブスクリプションを設定します。

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次のコマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>コンテナー レジストリを作成する

`az acr create` コマンドを使用して ACR のインスタンスを作成します。 レジストリの名前は Azure 内で一意にする必要があり、英数字で 5 ～ 50 文字にする必要があります。 次の例では、*myContainerRegistry* という名前を使用します。 レジストリの名前が使用されているエラーが発生する場合は、別の名前を選択します。

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

レジストリが作成されると、次のような出力が表示されます。

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Azure Container Registry にイメージをプッシュする

このチュートリアルでは、例として To Do List サンプル アプリケーションを使います。  [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) および [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) サービスのコンテナー イメージは、Docker Hub にあります。 Visual Studio でアプリケーションをビルドする方法については、[Service Fabric Mesh Web アプリのビルド](service-fabric-mesh-tutorial-create-dotnetcore.md)に関するページを参照してください。 Service Fabric Mesh では、Windows または Linux の Docker コンテナーを実行できます。  Linux コンテナーを使用している場合は、Docker で **[Switch to Linux containers]\(Linux コンテナーに切り替える\)** を選択します。  Windows コンテナーを使用している場合は、Docker で **[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択します。

ACR のインスタンスにイメージをプッシュするには、まずコンテナー イメージを用意する必要があります。 ローカル コンテナー イメージがまだない場合は、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、[WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) イメージと [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) イメージを Docker Hub からプルします。

>[!NOTE]
> 2020 年 11 月 2 日より、Docker の無料プラン アカウントから Docker Hub に対する匿名と認証済みの要求に[ダウンロード レート制限](https://docs.docker.com/docker-hub/download-rate-limit/)が適用されるようになり、IP アドレスによって実施されます。 
> 
> これらのコマンドには、Docker Hub のパブリック イメージが利用されています。 レート制限を受ける場合があるので注意してください。 詳細については、「[Docker Hub に対する認証](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)」を参照してください。

Windows イメージをプルします。

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

イメージをレジストリにプッシュするには、ACR ログイン サーバーの完全修飾名を使用して、そのイメージにタグを付けておく必要があります。

次のコマンドを実行して、ACR インスタンスの完全なログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) コマンドを使用して、Docker イメージにタグを付けます。 次の例では、seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 および seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 イメージにタグを付けています。 さまざまなイメージを使用している場合は、次のコマンドでこれらのイメージ名を置き換えてください。

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Azure Container Registry にサインインします。

```azurecli
az acr login -n myContainerRegistry
```

[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドで ACR インスタンスにイメージをプッシュします。

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>コンテナー イメージの一覧表示

ACR インスタンス内のリポジトリを表示するには、次のコマンドを実行します。

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

次の例では、**azure-mesh-todo-service** リポジトリのタグが一覧表示されます。

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

上記の出力により、プライベート コンテナー レジストリ内に `azure-mesh-todo-service:1.0-nanoserver-1709` があることを確認します。  `azure-mesh-todo-webfrontend:1.0-nanoserver-1709` の存在を確認することもできます。

## <a name="retrieve-credentials-for-the-registry"></a>レジストリの資格情報を取得する

> [!IMPORTANT]
> 運用環境では、ACR インスタンスで管理者ユーザーを有効にしないでください。 ここでは便宜上行っています。 運用環境では、ユーザーとシステム両方の認証に[サービス プリンシパル](../container-registry/container-registry-auth-service-principal.md)を使用します。

テンプレートを使用して作成されたレジストリからコンテナー インスタンスをデプロイするには、デプロイの間にレジストリの資格情報を指定する必要があります。 最初に、次のコマンドを使用して、レジストリの管理者ユーザーを有効にします。

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

次に、次のコマンドを使用して、レジストリのログイン サーバー名、ユーザー名、パスワードを取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

次のセクションで RM テンプレートとパラメーター ファイルを作成するときに、ここで返された ACR ログイン サーバー名、ユーザー名、パスワードの値を使用します。

## <a name="download-and-explore-the-template-and-parameters-files"></a>テンプレート ファイルとパラメーター ファイルをダウンロードして調べる

Service Fabric Mesh アプリケーションは、Azure Resource Manager (RM) テンプレートを使用してデプロイおよび管理できる Azure リソースです。 Azure ソリューションのデプロイと管理に関する概念に精通していない場合は、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」および「[Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/templates/template-syntax.md)」をご覧ください。

このチュートリアルでは、例として To Do List サンプルを使います。  新しいテンプレート ファイルとパラメーター ファイルを作成する代わりに、[mesh_rp.windows.json デプロイ テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) ファイルと [mesh_rp.windows.parameter.json パラメーター](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) ファイルをダウンロードします。

### <a name="parameters"></a>パラメーター
アプリケーションのデプロイ後に変更が予想される値がテンプレートにある場合、またはデプロイごとにオプションを変更する場合 (他のデプロイにこのテンプレートを再利用する場合)、ベスト プラクティスは値をパラメーター化することです。 これを行う適切な方法は、デプロイ テンプレートの先頭に "parameters" セクションを作成し、そこでパラメーターの名前とプロパティを指定して、以降のデプロイ テンプレートではそれを参照することです。 各パラメーターの定義には、*type*、*defaultValue*、および省略可能な *metadata* セクションと *description* が含まれます。

parameters セクションは、デプロイ テンプレートの先頭の、*resources* セクションの直前で定義します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

[mesh_rp.windows.json デプロイ テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) では、location、registryPassword、registryUserName、registryServer、frontEndImage、serviceImage、frontEndCpu、serviceCpu、frontEndMemory、serviceMemory、frontEndReplicaCount、serviceReplicaCount の各パラメーターが宣言されています。  各パラメーターの説明はデプロイ テンプレート ファイルをご覧ください。

これらのパラメーターは、[mesh_rp.windows.parameter.json パラメーター](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) ファイルで使用されます。  パラメーター ファイルを別にすることで、デプロイ テンプレート自体を更新することなく、デプロイごとにパラメーターの値を変更できます。

### <a name="overview-of-the-application-and-services"></a>アプリケーションとサービスの概要

サービスは、テンプレートにおいてアプリケーション リソースのプロパティとして指定されています。  アプリケーションはプライベート ネットワークにデプロイされ、これはテンプレートでリソースとして宣言されています。  サービスではボリュームを使用してデータを保持でき、これはテンプレートでリソースとして宣言されています。  サービスごとに、OS の種類、コード パッケージ、レプリカの数、およびネットワークが、サービスのプロパティとして指定されています。  コード パッケージごとに、コンテナー イメージ、エンドポイント、メモリと CPU リソース、およびイメージ リポジトリの資格情報を指定します。 複数のサービスを含む Service Fabric Mesh アプリケーションのテンプレートは、だいたい次のようになります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
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
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

To Do List アプリケーションの仕様については、[mesh_rp.windows.json デプロイ テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) ファイルをご覧ください。

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Azure Service Fabric Mesh にアプリケーションをデプロイする
次のコマンドを使用して、アプリケーションおよび関連リソースを作成し、前の「[レジストリの資格情報を取得する](#retrieve-credentials-for-the-registry)」ステップからの資格情報を指定します。

パラメーター ファイルでは、次のパラメーター値を更新します。

|パラメーター|[値]|
|---|---|
|location|アプリケーションをデプロイするリージョン。  たとえば、"eastus"。|
|registryPassword|前の「[レジストリの資格情報を取得する](#retrieve-credentials-for-the-registry)」で取得したパスワード。 テンプレートのこのパラメーターはセキュリティで保護された文字列であり、デプロイの状態または `az mesh service show` コマンドでは表示されません。|
|registryUserName|「[レジストリの資格情報を取得する](#retrieve-credentials-for-the-registry)」で取得したユーザー名。|
|registryServer|「[レジストリの資格情報を取得する](#retrieve-credentials-for-the-registry)」で取得したレジストリ サーバー名。|
|frontEndImage|フロントエンド サービスのコンテナー イメージ。  たとえば、「 `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709` 」のように入力します。|
|serviceImage|バックエンド サービスのコンテナー イメージ。  たとえば、「 `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709` 」のように入力します。|

アプリケーションをデプロイするには、次のコマンドを実行します。

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

このコマンドを実行すると、次のような JSON スニペットが生成されます。 JSON 出力の ```outputs``` セクションで、```publicIPAddress``` プロパティをコピーします。

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

この情報は、ARM テンプレートの ```outputs``` セクションから取得されます。 下記のように、このセクションではゲートウェイ リソースを参照してパブリック IP アドレスをフェッチします。 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>アプリケーションを開く

アプリケーションが正常にデプロイされたら、サービス エンドポイントのパブリック IP アドレスを取得します。 deployment コマンドは、サービス エンドポイントのパブリック IP アドレスを返します。 必要に応じて、ネットワーク リソースにクエリを送信して、サービス エンドポイントのパブリック IP アドレスを見つけることもできます。 このアプリケーションのネットワーク リソース名は `todolistappNetwork` です。次のコマンドを使用して、その情報を取得します。 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Web ブラウザーで IP アドレスに移動します。

## <a name="check-application-status"></a>アプリケーションの状態を確認する

app show コマンドを使用して、アプリケーションの状態を確認できます。 デプロイされたアプリケーションのアプリケーション名は "todolistapp" なので、その詳細をフェッチします。

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

`az mesh code-package-log get` コマンドを使用して、デプロイ済みのアプリケーションのログを確認します。
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * プライベート コンテナー レジストリを作成する
> * コンテナー イメージをレジストリにプッシュする
> * テンプレート ファイルとパラメーター ファイルを作成する
> * Azure Service Fabric Mesh にアプリケーションをデプロイする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric Mesh で実行しているアプリケーションをスケーリングする](service-fabric-mesh-tutorial-template-scale-services.md)