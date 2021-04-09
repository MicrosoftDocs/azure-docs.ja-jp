---
title: Service Fabric Mesh を使用する Service Fabric Reliable Disk Volume
description: Azure CLI を使用して、Service Fabric Reliable Disk ベースのボリュームをコンテナー内にマウントして Azure Service Fabric Mesh アプリケーションに状態を保存する方法について説明します。
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: ac65693f2513338695e07cd8a19acb13333e7281
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625788"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Service Fabric Mesh アプリケーションで高可用な Service Fabric Reliable Disk ベースのボリュームをマウントする 

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

コンテナー アプリで状態を保持する一般的な方法は、Azure File Storage などのリモート ストレージ、または Azure Cosmos DB などのデータベースを使用することです。 これにより、リモート ストアに対する読み取りおよび書き込みのネットワーク待機時間が大幅に増えます。

この記事では、Service Fabric Mesh アプリケーションのコンテナー内にボリュームをマウントして高可用な Service Fabric Reliable Disk に状態を保存する方法について説明します。
Service Fabric Reliable Disk は、高可用性のために Service Fabric クラスター内でレプリケートされた書き込みと共に、ローカルの読み取り用のボリュームを提供します。 その結果、読み取りのためのネットワーク呼び出しがなくなり、書き込みのためのネットワーク待機時間が短くなります。 コンテナーが再起動されたり別のノードに移動されたりしても、新しいコンテナー インスタンスは以前と同じボリュームを認識します。 そのため、効率的であり、高可用でもあります。

この例のカウンター アプリケーションには、ブラウザーの Web ページにカウンター値を表示する ASP.NET Core サービスがあります。

`counterService` は、ファイルからカウンター値を繰り返し読み取り、増分してファイルに書き戻します。 このファイルは、Service Fabric Reliable Disk がサポートするボリュームにマウントされたフォルダーに格納されます。

## <a name="prerequisites"></a>前提条件

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このタスクを実行できます。 この記事で Azure CLI を使用するには、`az --version` が少なくとも `azure-cli (2.0.43)` を返すことを確認します。  こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric Mesh CLI 拡張モジュールをインストール (または更新) してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure にサインインしてサブスクリプションを設定します。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

アプリケーションのデプロイ先となるリソース グループを作成します。 次のコマンドは、米国東部の場所に `myResourceGroup` という名前のリソース グループを作成します。 次のコマンドでリソース グループ名を変更する場合は、以降のすべてのコマンドでも忘れずに変更してください。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

>[!NOTE]
> 2020 年 11 月 2 日より、Docker の無料プラン アカウントから Docker Hub に対する匿名と認証済みの要求に[ダウンロード レート制限](https://docs.docker.com/docker-hub/download-rate-limit/)が適用されるようになり、IP アドレスによって実施されます。 
> 
> このテンプレートには、Docker Hub のパブリック イメージが利用されています。 レート制限を受ける場合があるので注意してください。 詳細については、「[Docker Hub に対する認証](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)」を参照してください。

次のコマンドでは、[counter.sfreliablevolume.linux.json テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json)を使用して Linux アプリケーションがデプロイされます。 Windows アプリケーションをデプロイするには、[counter.sfreliablevolume.windows.json テンプレート](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json)を使用します。 大規模なコンテナー イメージはデプロイに時間がかかる場合があります。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

次のコマンドでデプロイの状態を表示することもできます

```azurecli-interactive
az deployment group show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

リソースの種類が `Microsoft.ServiceFabricMesh/gateways` のゲートウェイ リソースの名前を確認してください。 これは、アプリのパブリック IP アドレスを取得するときに使用されます。

## <a name="open-the-application"></a>アプリケーションを開く

アプリケーションが正常にデプロイされたら、アプリのゲートウェイ リソースの ipAddress を取得します。 前述のセクションで確認したゲートウェイ名を使用します。
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

出力には、サービス エンドポイントのパブリック IP アドレスであるプロパティ `ipAddress` があります。 ブラウザーからそれを開きます。 Web ページには、毎秒更新されるカウンター値が表示されます。

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>アプリケーションがボリュームを使用できることを確認する

アプリケーションでは、ボリュームの `counter/counterService` フォルダー内に `counter.txt` という名前のファイルが作成されます。 このファイルの内容は、Web ページに表示されるカウンター値です。

## <a name="delete-the-resources"></a>リソースの削除

Azure で使用しなくなったリソースは頻繁に削除してください。 この例に関連するリソースを削除するには、次のコマンドを使用して、それらがデプロイされたリソース グループを削除します (これにより、リソース グループに関連付けられたすべてが削除されます)。

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上の Service Fabric Reliable Volume Disk サンプル アプリケーションを確認します。
- Service Fabric リソース モデルの詳細については、[Service Fabric Mesh リソース モデル](service-fabric-mesh-service-fabric-resources.md)に関するページを参照してください。
- Service Fabric Mesh の詳細については、[Service Fabric Mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。