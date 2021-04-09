---
title: クイックスタート - Hello World を Azure Service Fabric Mesh にデプロイする
description: このクイック スタートでは、Service Fabric Mesh アプリケーションを Azure Service Fabric mesh にデプロイする方法について説明します。
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: quickstart
ms.openlocfilehash: c89cc1972a554cac85ce2a258873f6c810e45167
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217722"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>クイック スタート: Hello World を Service Fabric Mesh にデプロイする

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

[Service Fabric mesh](service-fabric-mesh-overview.md) では、仮想マシンをプロビジョニングする必要がなくなり、Azure でのマイクロサービス アプリケーションの作成と管理が容易になります。 このクイック スタートでは、Azure で Hello World アプリケーションを作成してインターネットに公開します。 この操作は、1 つのコマンドで完結します。 わずか数分でこのビューがブラウザーに表示されます。

![ブラウザーでの Hello World アプリ][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Azure アカウントをまだお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI の設定 
Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このクイック スタートを完了できます。 こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric Mesh CLI 拡張モジュールをインストールしてください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure にサインインしてサブスクリプションを設定します。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>リソース グループの作成
アプリケーションのデプロイ先となるリソース グループを作成します。 既存のリソース グループを使用して、この手順をスキップできます。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>アプリケーションの配置

>[!NOTE]
> 2020 年 11 月 2 日より、Docker の無料プラン アカウントから Docker Hub に対する匿名と認証済みの要求に[ダウンロード レート制限](https://docs.docker.com/docker-hub/download-rate-limit/)が適用されるようになり、IP アドレスによって実施されます。 
> 
> これらのテンプレートには、Docker Hub のパブリック イメージが利用されています。 レート制限を受ける場合があるので注意してください。 詳細については、「[Docker Hub に対する認証](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)」を参照してください。

`az mesh deployment create` コマンドを使用して、リソース グループにアプリケーションを作成します。  次のコマンドレットを実行します。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

前述のコマンドでは、[linux.json template](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json) を使用して Linux アプリケーションがデプロイされます。 Windows アプリケーションをデプロイする場合は、[windows.json template](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json) を使用します。 Windows コンテナー イメージは Linux コンテナー イメージよりも大きく、デプロイに時間がかかることがあります。

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
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>アプリケーションを開く
アプリケーションが正常にデプロイされたら、CLI 出力からサービス エンドポイントのパブリック IP アドレスをコピーします。 Web ブラウザーで IP アドレスを開きます。 Azure Service Fabric mesh のロゴと一緒に Web ページが表示されます。

## <a name="check-the-application-details"></a>アプリケーションの詳細の確認
`az mesh app show` コマンドを使用して、アプリケーションの状態を確認できます。 このコマンドでは、フォローアップできる有用な情報が提供されます。

このクイック スタートのアプリケーション名は `helloWorldApp` です。アプリケーションの詳細を収集するには、次のコマンドを実行します。

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>アプリケーション ログを表示する

`az mesh code-package-log get` コマンドを使用して、デプロイ済みのアプリケーションのログを確認します。

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを削除する準備が整ったら、[az group delete][az-group-delete] コマンドを実行して、リソース グループと、そこに含まれているアプリケーションとネットワークのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

Service Fabric Mesh アプリケーションの作成とデプロイについてさらに詳しく学習するには、チュートリアルを続けてください。
> [!div class="nextstepaction"]
> [マルチサービス Web アプリケーションの作成とデプロイ](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: /cli/azure/install-azure-cli